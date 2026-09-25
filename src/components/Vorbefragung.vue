<template>
  <div class="h-full overflow-y-auto p-6 flex flex-col bg-[var(--bg)] vf-screen">
    <p class="eyebrow">Vorbefragung</p>
    <h1 class="font-display text-[24px] text-[var(--ink)] mb-3">Formular</h1>
    <p class="mb-6 text-[13.5px] leading-relaxed text-[var(--ink-soft)]">
      Bitte gib an, wie sehr die folgenden Aussagen auf dich zutreffen. Es gibt keine richtigen
      oder falschen Antworten – uns interessiert deine persönliche Einschätzung.
    </p>

    <div class="space-y-4 mb-6">
      <div v-for="(q, i) in questions" :key="i" class="card">
        <div class="flex gap-2.5 mb-4">
          <span class="q-index">{{ i + 1 }}</span>
          <p class="text-[14px] leading-snug text-[var(--ink)]">{{ q }}</p>
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

    <div class="grow"></div>
    <button class="primary-btn press" :disabled="!allAnswered" @click="submit">
      Weiter
    </button>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const emit = defineEmits(['complete']);

const questions = [
  'Es ist mir wichtig zu verstehen, wie meine personenbezogenen Daten genutzt werden.',
  'Wenn ich die Datenschutzerklärung einer Website direkt verstehe, fühle ich mich in meiner Privatsphäre geschützt.',
  'Wenn eine Website mich nach persönlichen Daten fragt, möchte ich sie nicht nutzen.',
  'Wenn eine Website persönliche Daten erfasst, ist es mir wichtig im Einzelnen zu bestimmen, welche meiner Daten genutzt werden.',
  'Im Alltag akzeptiere ich die meisten Cookie-Banner direkt.',
];

// answers[i] speichert den gewählten Options-Index (0–4), null solange unbeantwortet.
// Index -> Skalenwert: 0 = -2 ("stimme überhaupt nicht zu") ... 4 = +2 ("stimme voll zu")
const answers = ref([null, null, null, null, null]);

const allAnswered = computed(() => answers.value.every((a) => a !== null));

const submit = () => {
  if (!allAnswered.value) return;

  const v = answers.value.map((i) => i - 2); // Index -> -2..+2

  // F5 ist negativ formuliert und wird deshalb subtrahiert statt addiert.
  const score = v[0] + v[1] + v[2] + v[3] - v[4]; // Bereich: -10 .. +10

  let personaId;
  let personaLabel;
  if (score <= 0) {
    personaId = 'gutgläubig';
    personaLabel = 'Gutgläubig';
  } else if (score <= 5) {
    personaId = 'skeptiker';
    personaLabel = 'Skeptisch';
  } else {
    personaId = 'misstrauend';
    personaLabel = 'Misstrauisch';
  }

  emit('complete', {
    answers: answers.value.slice(),
    score,
    personaId,
    personaLabel,
  });
};
</script>

<style scoped>
/* Fallback CSS-Variablen, falls globale Themes nicht geladen sind */
.vf-screen {
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

/* Eyebrow badge */
.eyebrow {
  display: inline-block;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  color: var(--primary);
  margin-bottom: 0.25rem;
}

/* Question Cards */
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

/* Question Index Number Badge */
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

/* Likert Scale Layout */
.likert {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 0.5rem;
  margin-top: 1rem;
  padding: 0.25rem 0;
}

/* Likert Buttons */
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

.likert-dot:focus-visible {
  box-shadow: 0 0 0 3px var(--ring);
}

/* Selected State */
.likert-dot.is-selected {
  background-color: var(--primary);
  border-color: var(--primary);
  color: #ffffff;
  box-shadow: 0 2px 4px rgba(37, 99, 235, 0.25);
  transform: scale(1.05);
}

/* Click/Press Micro-Interaction */
.press:active:not(:disabled) {
  transform: scale(0.96);
}

/* Labels below scale */
.likert-endlabels {
  display: flex;
  justify-content: space-between;
  margin-top: 0.5rem;
  font-size: 0.75rem;
  color: var(--ink-muted);
  font-weight: 500;
}

/* Submit Button */
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

.primary-btn:focus-visible {
  box-shadow: 0 0 0 3px var(--ring);
}

.primary-btn:disabled {
  background-color: var(--primary-disabled);
  color: #94a3b8;
  cursor: not-allowed;
  box-shadow: none;
}
</style>