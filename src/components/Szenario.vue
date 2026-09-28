<template>
  <div class="min-h-screen w-full flex justify-center px-6 py-16 bg-[var(--bg)] sz-screen">
    <div class="w-full max-w-[600px]">
      <p class="eyebrow">Bevor du startest</p>
      <h1 class="font-display text-[28px] text-[var(--ink)] mb-6">Szenario</h1>

      <div class="space-y-4 mb-8">
        <div class="card">
          <h3 class="section-title">Die Situation</h3>
          <p class="text-left section-text">
            In deiner Stadt breitet sich ein neuer, schneller ansteckender Erreger aus. Das städtische
            Gesundheitsamt setzt auf eine freiwillige App zur Kontaktnachverfolgung, um Infektionsketten
            zu stoppen.
          </p>
        </div>

        <div class="card">
          <h3 class="section-title">Deine Aufgabe</h3>
          <p class="text-left section-text">
            Du nutzt die App heute zum ersten Mal. Bitte verhalte dich so, als wäre die Situation
            real und die App eine echte Möglichkeit zum Schutz für dich und andere.
          </p>
        </div>

        <div class="card">
          <h3 class="section-title">Funktionsweise</h3>
          <p class="text-left section-text">
            Die App warnt dich, wenn du in einer besonders infektiösen Umgebung bist. 
            Wie genau die App arbeitet und welche Daten du dafür freigibst, 
            wirst du im folgenden Testverlauf selbst entscheiden.
          </p>
          
        </div>
        <div class="card">
            <h3 class="section-title">Erklärung zu Sensoren</h3>
            <p class="text-left section-text">
              Einige in der App genutzten Sensoren (z. B. Kamera- & Temperaturanalyse) sind für diesen Prototypen simuliert. Bitte nimm für den Test an, dass dein Smartphone diese Fähigkeiten bereits besitzt.      
            </p>
          </div>

      <div class="notice-card">
          <div class="section-title">
            <h3 class="notice-title ">Wichtiger Hinweis </h3>
          </div>
          <p class="text-left section-text">
            Dies ist eine Hochschulstudie. Es gibt keinen echten Erreger, alle eingegebenen oder
            freigegebenen Daten sind simuliert und werden nicht gespeichert.
          </p>
        </div>
      </div>
      <button class="primary-btn press " @click="$emit('continue')">Weiter zur App</button>
    </div>
  </div>
</template>

<script setup>
import { h } from 'vue';

defineEmits(['continue']);

</script>

<style scoped>
/* Fallback CSS-Variablen, abgestimmt auf das bestehende Design */
.sz-screen {
  --bg: #f8fafc;
  --card-bg: #ffffff;
  --ink: #0f172a;
  --ink-soft: #475569;
  --ink-muted: #94a3b8;
  --primary: #2563eb;
  --primary-hover: #1d4ed8;
  --border: #e2e8f0;
  --ring: rgba(37, 99, 235, 0.15);

  /* Farbvariablen für den Hinweis-Block */
  --info-bg: #eff6ff;
  --info-border: #bfdbfe;
  --info-text: #1e40af;
}

.font-display { font-family: 'Fraunces', ui-serif, Georgia, serif; font-weight: 600; }

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

/* Standard Content Cards */
.card {
  background-color: var(--card-bg);
  border: 1px solid var(--border);
  border-radius: 0.75rem;
  padding: 1.25rem;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.02), 0 1px 2px -1px rgba(0, 0, 0, 0.02);
  transition: border-color 0.2s ease, box-shadow 0.2s ease;
}

.card:hover {
  border-color: #cbd5e1;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.04), 0 2px 4px -2px rgba(0, 0, 0, 0.04);
}

/* Specific styling for Notice Card (Info-Box) */
.notice-card {
  background-color: var(--info-bg);
  border: 1px solid var(--info-border);
  border-radius: 0.75rem;
  padding: 1.25rem;
  color: var(--info-text);
  transition: border-color 0.2s ease;
}

.notice-title {
  font-size: 0.9375rem;
  font-weight: 600;
  color: var(--info-text);
  margin: 0;
}

.notice-card .section-text {
  color: #1e3a8a; /* Leicht dunkleres Blau für optimale Lesbarkeit */
}

/* Card Headlines */
.section-title {
  font-size: 0.9375rem;
  font-weight: 600;
  color: var(--ink);
  margin-bottom: 0.375rem;
}

/* Body Text */
.section-text {
  font-size: 0.84375rem; /* 13.5px */
  line-height: 1.6;
  color: var(--ink-soft);
  margin: 0;
}

/* Primary Action Button */
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

.primary-btn:hover {
  background-color: var(--primary-hover);
  box-shadow: 0 4px 6px -1px rgba(37, 99, 235, 0.2);
}

.primary-btn:focus-visible {
  box-shadow: 0 0 0 3px var(--ring);
}

/* Click Micro-Interaction */
.press:active {
  transform: scale(0.98);
}

@media (prefers-reduced-motion: reduce) {
  * { transition-duration: .001ms !important; animation-duration: .001ms !important; }
}
</style>
