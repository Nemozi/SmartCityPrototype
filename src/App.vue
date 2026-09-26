<template>
  <div class="app-root">

    <!-- ============ WEBSITE-STAGES ============
         Szenario, Vorbefragung und Nachbefragung laufen im normalen
         Website-Layout — bewusst visuell getrennt vom App-Prototyp, damit
         klar wird: die App ist nur ein Teil der Gesamtstudie. -->
    <div v-if="step !== 'prototype'" class="site">

      <!-- 1. Start -->
      <div v-if="step === 'briefing'" class="site-page">
        <div class="site-page-inner">
          <p class="eyebrow">Vor dem Start</p>
          <h1 class="font-display text-[28px] text-[var(--ink)] mb-3">Willkommen zur Studie</h1>
          <p class="p-4 mb-10 text-[15px] leading-relaxed text-[var(--ink-soft)]">
          Im nächsten Schritt stellen wir dir ein paar kurze Fragen, erklären das Szenario und
          leiten dich anschließend zum Prototyp weiter.
          </p>
          <button class="primary-btn press" @click="step = 'vorbefragung'">Weiter</button>
        </div>
      </div>

      <!-- 2. Vorbefragung (ordnet die Person einem Persona-Typ zu) -->
      <Vorbefragung v-if="step === 'vorbefragung'" @complete="onVorbefragungComplete" />

      <!-- 3. Szenario -->
      <Szenario v-if="step === 'szenario'" @continue="step = 'prototype'" />

      <!-- 5. Nachbefragung -->
      <Nachbefragung
        v-if="step === 'survey'"
        :loading="loading"
        :done="submitted"
        @complete="onNachbefragungComplete"
      />
    </div>

    <!-- ============ PROTOTYPE-STAGE ============
         Nur der interaktive App-Prototyp läuft im Smartphone-Rahmen. -->
    <div v-else class="min-h-screen flex items-center justify-center p-4 phone-stage">
      <div class="w-full max-w-[375px] h-[700px] bg-white rounded-[3rem] shadow-2xl overflow-hidden relative border-[8px] border-gray-800">
        <Prototype
          :assignedPersona="userData.persona"
          @finish="onPrototypeComplete"
          @restart="onRestartStudy"
        />
      </div>
    </div>

    <!-- SUCCESS TOAST -->
    <Transition name="toast">
      <div v-if="toastVisible" class="toast">
        <CheckIcon /> Studie gespeichert
      </div>
    </Transition>
  </div>
</template>

<script setup>
import { ref, reactive, h } from 'vue';
import Prototype from './components/Prototype.vue';
import Vorbefragung from './components/Vorbefragung.vue';
import Szenario from './components/Szenario.vue';
import Nachbefragung from './components/Nachbefragung.vue';
import { supabase } from './supabase';

const step = ref('briefing');
const loading = ref(false);
const submitted = ref(false);   // true, sobald die Nachbefragung erfolgreich gespeichert wurde
const toastVisible = ref(false);

const userData = reactive({
  settings: {},
  // aus der Vorbefragung:
  persona: '',
  vorbefragungScore: null,
  vorbefragungAnswers: [],
  // aus der Nachbefragung:
  nachbefragungAnswers: [],
  feedback: '',
});

const CheckIcon = () =>
  h('svg', { viewBox: '0 0 24 24', fill: 'none', width: 16, height: 16 }, [
    h('path', { d: 'M5 12.5 9.5 17 19 7', stroke: 'currentColor', 'stroke-width': 1.8, 'stroke-linecap': 'round', 'stroke-linejoin': 'round' }),
  ]);

const onVorbefragungComplete = (result) => {
  userData.persona = result.personaId;
  userData.vorbefragungScore = result.score;
  userData.vorbefragungAnswers = result.answers;
  step.value = 'szenario';
};

const onPrototypeComplete = (finalSettings) => {
  userData.settings = finalSettings;
  step.value = 'survey';
};

// "Studie neu starten" im Prototyp-Menü: der komplette Prototype wird unmountet
// (dadurch verliert er automatisch seinen internen Zustand) und der Proband
// muss die Vorbefragung zwingend erneut vollständig ausfüllen.
const onRestartStudy = () => {
  userData.persona = '';
  userData.vorbefragungScore = null;
  userData.vorbefragungAnswers = [];
  userData.nachbefragungAnswers = [];
  userData.feedback = '';
  userData.settings = {};
  submitted.value = false;
  step.value = 'vorbefragung';
};

const onNachbefragungComplete = async (result) => {
  userData.nachbefragungAnswers = result.answers;
  userData.feedback = result.feedback;
  await submitToSupabase();
};

const submitToSupabase = async () => {
  loading.value = true;
  const { error } = await supabase.from('study_data').insert([{
    persona: userData.persona,
    pretest_score: userData.vorbefragungScore,
    pretest_answers: userData.vorbefragungAnswers,
    posttest_answers: userData.nachbefragungAnswers,
    exit_feedback: userData.feedback,
    camera_enabled: userData.settings.camera,
    gps_enabled: userData.settings.gps,
    mic_enabled: userData.settings.mic,
    temp_enabled: userData.settings.temp,
  }]);
  loading.value = false;
  if (!error) {
    submitted.value = true;
    toastVisible.value = true;
    window.setTimeout(() => { toastVisible.value = false; }, 2600);
  } else {
    alert('Fehler beim Speichern: ' + error.message);
  }
};
</script>

<style scoped>
.app-root {
  font-family: 'Inter', ui-sans-serif, system-ui, -apple-system, sans-serif;
}
.font-display { font-family: 'Fraunces', ui-serif, Georgia, serif; font-weight: 600; }

@import url('https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,500;9..144,600;9..144,700&family=Inter:wght@400;500;600;700&display=swap');

/* ============ Website-Stages: gemeinsames Theme ============ */
.site {
  --bg: #f8fafc;
  --card-bg: #ffffff;
  --ink: #0f172a;
  --ink-soft: #475569;
  --ink-muted: #94a3b8;
  --primary: #2563eb;
  --primary-hover: #1d4ed8;
  --primary-disabled: #cbd5e1;
  --border: #e2e8f0;
  --ring: rgba(37, 99, 235, 0.15);
  min-height: 100vh;
  background: var(--bg);
  color: var(--ink);
}

.eyebrow {
  display: inline-block;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  color: var(--primary);
  margin-bottom: 0.25rem;
}

.site-page {
  min-height: 100vh;
  width: 100%;
  display: flex;
  justify-content: center;
  padding: 64px 24px 72px;
}
.site-page-inner {
  width: 100%;
  max-width: 600px;
}

.primary-btn {
  width: 100%;
  padding: 0.875rem 1.5rem;
  border-radius: 0.625rem;
  background-color: var(--primary);
  color: #ffffff;
  font-size: 0.9375rem;
  font-weight: 600;
  border: none;
  cursor: pointer;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
  transition: all 0.15s ease;
}
.primary-btn:hover:not(:disabled) {
  background-color: var(--primary-hover);
  box-shadow: 0 4px 6px -1px rgba(37, 99, 235, 0.2);
}
.primary-btn:disabled { background-color: var(--primary-disabled); color: #94a3b8; cursor: not-allowed; }

.press { transition: transform .15s cubic-bezier(.34,1.56,.64,1); }
.press:active:not(:disabled) { transform: scale(.98); }

/* ============ Prototyp-Stage: Smartphone-Rahmen ============ */
.phone-stage {
  background: #cbd5e1;
}

/* ============ Toast (Seiten-Ebene, nicht mehr an die Handy-Box gebunden) ============ */
.toast {
  position: fixed; left: 50%; bottom: 24px; transform: translateX(-50%);
  z-index: 999; display: flex; align-items: center; gap: 6px;
  background: var(--ink, #0f172a); color: #fff; font-size: 12.5px; font-weight: 500;
  padding: 10px 16px; border-radius: 999px; box-shadow: 0 8px 20px rgba(0,0,0,.25);
}
.toast-enter-active { transition: opacity .2s ease, transform .25s cubic-bezier(.34,1.56,.64,1); }
.toast-leave-active { transition: opacity .18s ease, transform .18s ease; }
.toast-enter-from { opacity: 0; transform: translate(-50%, 10px) scale(.9); }
.toast-leave-to { opacity: 0; transform: translate(-50%, 4px); }

@media (prefers-reduced-motion: reduce) {
  * { transition-duration: .001ms !important; animation-duration: .001ms !important; }
}
</style>
