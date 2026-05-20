<template>
  <v-footer class="footer-minimal">
    <v-container class="text-center">
      <span class="footer-text">
        © 2026 — Proyecto Cultural. Todos los derechos reservados.
      </span>
    </v-container>
  </v-footer>
</template>

<style scoped>
.footer-minimal {

  background:
    rgba(255,255,255,0.72) !important;

  backdrop-filter:
    blur(18px);

  border-top:
    1px solid rgba(255,255,255,0.28);

  padding:
    40px 0;

  box-shadow:
    0 -4px 20px rgba(0,0,0,0.04),
    0 0 18px rgba(0,255,224,0.04);
}

/* =========================================================
   TEXTO FOOTER — NEON SUAVE
========================================================= */

.footer-text {

  color:
    var(--neon-cyan);

  letter-spacing:
    0.04em;

  text-shadow:
    0 0 8px rgba(0,255,224,0.18);

  transition:
    color 0.25s ease,
    text-shadow 0.25s ease,
    opacity 0.25s ease;
}

.footer-text:hover {

  color:
    var(--neon-pink);

  text-shadow:
    0 0 12px rgba(255,77,255,0.35),
    0 0 22px rgba(0,255,224,0.18);

  opacity: 1;
}
</style>