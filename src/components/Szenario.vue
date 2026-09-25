<template>
  <div class="h-full overflow-y-auto p-6 flex flex-col bg-[var(--bg)] sz-screen">
    <p class="eyebrow">Bevor du startest</p>
    <h1 class="font-display text-[24px] text-[var(--ink)] mb-5">Das Szenario</h1>

    <div class="space-y-4 mb-6">
      <div class="card">
        <h3 class="section-title">Die Situation</h3>
        <p class="section-text">
          In deiner Stadt breitet sich ein neuer, schneller ansteckender Erreger aus. Das städtische
          Gesundheitsamt setzt auf eine freiwillige App zur Kontaktnachverfolgung, um Infektionsketten
          zu stoppen.
        </p>
      </div>

      <div class="card">
        <h3 class="section-title">Deine Aufgabe</h3>
        <p class="section-text">
          Du richtest die App heute zum ersten Mal ein. Bitte verhalte dich so, als wäre die Situation
          real und die App eine echte Möglichkeit zum Schutz für dich und andere.
        </p>
      </div>

      <div class="notice-card">
        <div class="flex items-center gap-2 mb-2.5">
          <InfoIcon />
          <h3 class="notice-title">Wichtiger Hinweis</h3>
        </div>
        <p class="section-text">
          Dies ist eine Hochschulstudie. Es gibt keinen echten Erreger – alle eingegebenen oder
          freigegebenen Daten sind simuliert und werden nicht gespeichert.
        </p>
      </div>

      <div class="card">
        <h3 class="section-title">Funktionsweise</h3>
        <p class="section-text">
          Die App warnt dich, wenn du Kontakt zu einer positiv getesteten Person hattest. Wie genau
          die App arbeitet und welche Daten du dafür freigibst, wirst du im folgenden Testverlauf
          selbst entscheiden.
        </p>
      </div>
    </div>

    <div class="grow"></div>
    <button class="primary-btn press" @click="$emit('continue')">Weiter zur App</button>
  </div>
</template>

<script setup>
import { h } from 'vue';

defineEmits(['continue']);

const InfoIcon = () =>
  h('svg', { viewBox: '0 0 24 24', fill: 'none', width: 16, height: 16 }, [
    h('circle', { cx: 12, cy: 12, r: 9, stroke: 'currentColor', 'stroke-width': 1.8 }),
    h('path', { d: 'M12 11v5.5', stroke: 'currentColor', 'stroke-width': 1.8, 'stroke-linecap': 'round' }),
    h('path', { d: 'M12 8h.01', stroke: 'currentColor', 'stroke-width': 1.8, 'stroke-linecap': 'round' }),
  ]);
</script>

<style scoped>
.sz-screen {
  --bg: #F5F2EC;
  --surface: #FFFFFF;
  --ink: #1C2420;
  --ink-soft: #5B6B63;
  --border: #E4DECF;
  --primary: #1F5749;
  --amber: #B9701C;
  --amber-soft: #FBF1E3;
  font-family: 'Inter', ui-sans-serif, system-ui, -apple-system, sans-serif;
  color: var(--ink);
}
.font-display { font-family: 'Fraunces', ui-serif, Georgia, serif; font-weight: 600; }

@import url('https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,500;9..144,600;9..144,700&family=Inter:wght@400;500;600;700&display=swap');

.eyebrow { font-size: 11px; font-weight: 600; letter-spacing: .06em; color: var(--primary); text-transform: uppercase; margin-bottom: 6px; }

.card {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 18px;
  padding: 18px;
  box-shadow: 0 1px 2px rgba(28,36,32,.04);
}

.section-title { font-family: 'Fraunces', ui-serif, Georgia, serif; font-weight: 600; font-size: 15px; color: var(--ink); margin-bottom: 6px; }
.section-text { font-size: 13.5px; line-height: 1.6; color: var(--ink-soft); }

.notice-card {
  background: var(--amber-soft);
  border: 1px solid rgba(185,112,28,.2);
  border-left: 4px solid var(--amber);
  border-radius: 16px;
  padding: 16px 18px;
}
.notice-title { font-size: 13px; font-weight: 700; color: var(--amber); }
.notice-card svg { color: var(--amber); flex-shrink: 0; }

.primary-btn {
  width: 100%; padding: 15px; border-radius: 14px;
  background: var(--primary); color: #fff;
  font-weight: 600; font-size: 13px; letter-spacing: .02em; text-align: center;
}

.press { transition: transform .15s cubic-bezier(.34,1.56,.64,1), opacity .15s; }
.press:active { transform: scale(.97); opacity: .92; }

@media (prefers-reduced-motion: reduce) {
  * { transition-duration: .001ms !important; animation-duration: .001ms !important; }
}
</style>
