<template>
  <div class="min-h-screen w-full flex justify-center px-6 py-16 bg-[var(--bg)] nb-screen">
    <div class="w-full max-w-[600px]">

      <template v-if="!done">
        <p class="eyebrow">Nachbefragung</p>
        <h1 class="font-display text-[28px] text-[var(--ink)] mb-3">Dein Eindruck</h1>
        <p class=" p-4 text-left mb-8 text-[15px] leading-relaxed text-[var(--ink-soft)]">
          Zum Abschluss noch ein paar Fragen zu deinem Eindruck von der App. Auch hier gibt es
          keine richtigen oder falschen Antworten.
          <br>
        </p>

        <div class="space-y-4 mb-6">
          <div v-for="(q, i) in questions" :key="i" class="card">
            <div class="flex gap-2.5 mb-4">
              <span class="q-index">{{ i + 1 }}</span>
              <p class="text-left text-[14px] leading-snug text-[var(--ink)]">{{ q }}</p>
            </div>

            <div class="likert" role="radiogroup" :aria-label="q">
              <button
                v-for="opt in 5"
                :key="opt"
                type="button"
                role="radio"
                :aria-checked="answers[i] === opt - 1"
                class="likert-dot press"
                :class="{ 'is-selected': answers[i] === opt - 1 }"
                @click="answers[i] = opt - 1"
              >{{ opt }}</button>
            </div>
            <div class="likert-endlabels">
              <span>Stimme überhaupt nicht zu</span>
              <span>Stimme voll zu</span>
            </div>
          </div>
        </div>

        <div class="card mb-8">
          <label class="field-label">Weiteres Feedback (optional)</label>
          <textarea
            v-model="feedback"
            placeholder="Gibt es noch etwas, das du uns mitteilen möchtest?"
            class="field h-28 resize-none"
          ></textarea>
        </div>

        <button class="primary-btn press" :disabled="!allAnswered || loading" @click="submit">
          {{ loading ? 'Wird gespeichert …' : 'Antworten senden' }}
        </button>
      </template>

      <template v-else>
        <div class="card done-card">
          <p class="eyebrow">Abgeschlossen</p>
          <h1 class="font-display text-[28px] leading-snug text-[var(--ink)] mb-2">
  Danke für deine Teilnahme!
</h1>
          <p class="text-[14px] leading-relaxed text-[var(--ink-soft)]">
            Deine Antworten wurden gespeichert. Du kannst dieses Fenster jetzt schließen.
          </p>
        </div>
      </template>

    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const props = defineProps({
  loading: { type: Boolean, default: false },
  done: { type: Boolean, default: false },
});

const emit = defineEmits(['complete']);

const questions = [
  'Ich konnte gut nachvollziehen, welche Daten zu welchem Zweck gesammelt werden.',
  'Ich hatte das Gefühl, die Kontrolle über meine persönlichen Daten zu haben.',
  'Ich kann mir vorstellen, eine ähnliche Anwendung zur Reduktion von Virusinfektionen zu nutzen.',
  'Ich wäre bereit, persönliche Informationen zu teilen, wenn dadurch ein Nutzen für die Gesellschaft entsteht.',
  'Ich fand diese Anwendung sehr umständlich zu benutzen.',
  'Ich wäre bereit, persönliche Informationen zu teilen, wenn ich persönlich einen Nutzen daraus ziehe.',
];

// answers[i] speichert den gewählten Options-Index (0–4), null solange unbeantwortet.
const answers = ref(questions.map(() => null));
const feedback = ref('');

const allAnswered = computed(() => answers.value.every((a) => a !== null));

const submit = () => {
  if (!allAnswered.value || props.loading) return;

  const v = answers.value.map((i) => i - 2); // Index -> -2..+2, zur Auswertung

  console.group('Nachbefragung – Antworten');
  questions.forEach((q, i) => {
    console.log(`  N${i + 1}: Option ${answers.value[i] + 1}/5 (Skalenwert ${v[i]}) – "${q}"`);
  });
  if (feedback.value.trim()) console.log('Freitext-Feedback:', feedback.value.trim());
  console.groupEnd();

  emit('complete', {
    answers: answers.value.slice(),
    feedback: feedback.value.trim(),
  });
};
</script>

<style scoped>
/* Fallback CSS-Variablen, passend zu Vorbefragung/Szenario */
.nb-screen {
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
}

.font-display { font-family: 'Fraunces', ui-serif, Georgia, serif; font-weight: 600; height: auto }

.eyebrow {
  display: inline-block;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  color: var(--primary);
  margin-bottom: 0.25rem;
}

.card {
  background-color: var(--card-bg);
  border: 1px solid var(--border);
  border-radius: 0.75rem;
  padding: 1.25rem;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.02), 0 1px 2px -1px rgba(0, 0, 0, 0.02);
  transition: border-color 0.2s ease, box-shadow 0.2s ease;
}
.card:focus-within,
.card:hover {
  border-color: #cbd5e1;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.04), 0 2px 4px -2px rgba(0, 0, 0, 0.04);
}

.q-index {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  width: 1.5rem;
  height: 1.5rem;
  border-radius: 9999px;
  background-color: #eff6ff;
  color: var(--primary);
  font-size: 0.75rem;
  font-weight: 700;
}

.likert {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 0.5rem;
  margin-top: 1rem;
  padding: 0.25rem 0;
}

.likert-dot {
  flex: 1;
  aspect-ratio: 1;
  max-width: 2.75rem;
  max-height: 2.75rem;
  border-radius: 9999px;
  border: 1.5px solid var(--border);
  background-color: var(--card-bg);
  color: var(--ink-soft);
  font-size: 0.875rem;
  font-weight: 600;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.15s cubic-bezier(0.4, 0, 0.2, 1);
  outline: none;
}
.likert-dot:hover:not(.is-selected) {
  border-color: #94a3b8;
  background-color: #f8fafc;
  color: var(--ink);
}
.likert-dot:focus-visible { box-shadow: 0 0 0 3px var(--ring); }
.likert-dot.is-selected {
  background-color: var(--primary);
  border-color: var(--primary);
  color: #ffffff;
  box-shadow: 0 2px 4px rgba(37, 99, 235, 0.25);
  transform: scale(1.05);
}

.press:active:not(:disabled) { transform: scale(0.96); }

.likert-endlabels {
  display: flex;
  justify-content: space-between;
  margin-top: 0.5rem;
  font-size: 0.75rem;
  color: var(--ink-muted);
  font-weight: 500;
}

.field-label {
  display: block;
  font-size: 0.75rem;
  font-weight: 600;
  color: var(--ink-soft);
  margin-bottom: 0.4rem;
}
.field {
  width: 100%;
  background-color: #f8fafc;
  border: 1px solid var(--border);
  border-radius: 0.625rem;
  padding: 0.625rem 0.75rem;
  font-size: 0.875rem;
  color: var(--ink);
  font-family: inherit;
}
.field::placeholder { color: var(--ink-muted); }
.field:focus { outline: none; border-color: var(--primary); box-shadow: 0 0 0 3px var(--ring); }

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
  outline: none;
}
.primary-btn:hover:not(:disabled) {
  background-color: var(--primary-hover);
  box-shadow: 0 4px 6px -1px rgba(37, 99, 235, 0.2);
}
.primary-btn:focus-visible { box-shadow: 0 0 0 3px var(--ring); }
.primary-btn:disabled {
  background-color: var(--primary-disabled);
  color: #94a3b8;
  cursor: not-allowed;
  box-shadow: none;
}

.done-card { text-align: left; }

@media (prefers-reduced-motion: reduce) {
  * { transition-duration: .001ms !important; animation-duration: .001ms !important; }
}
</style>
