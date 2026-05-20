<template>
  <v-app class="app-wrapper">
    <AppHeader />

    <v-main class="main-content">
      <router-view class="page-view" />
    </v-main>

    <AppFooter />
  </v-app>
</template>

<script setup>
import AppHeader from '@/components/AppHeader.vue'
import AppFooter from '@/components/AppFooter.vue'
</script>


<style scoped>

/* =========================================================
   APP WRAPPER — GLASS + NEON MINIMAL
========================================================= */

.app-wrapper {
  position: relative;

  overflow: hidden;

  font-family: var(--font-sans);

  background:
    linear-gradient(
      180deg,
      #f8f8f9,
      #f2f2f3
    );

  color:
    #111111;

  min-height: 100vh;

  display: flex;

  flex-direction: column;
}

/* glow ambiental */
.app-wrapper::before {
  content: "";

  position: fixed;

  top: -180px;
  right: -140px;

  width: 420px;
  height: 420px;

  border-radius: 50%;

  background:
    radial-gradient(
      circle,
      rgba(0,255,208,0.08),
      transparent 72%
    );

  filter: blur(24px);

  pointer-events: none;

  z-index: 0;
}

/* glow secundario */
.app-wrapper::after {
  content: "";

  position: fixed;

  bottom: -220px;
  left: -160px;

  width: 420px;
  height: 420px;

  border-radius: 50%;

  background:
    radial-gradient(
      circle,
      rgba(255,77,255,0.05),
      transparent 72%
    );

  filter: blur(28px);

  pointer-events: none;

  z-index: 0;
}

/* =========================================================
   MAIN CONTENT
========================================================= */

.main-content {
  position: relative;

  z-index: 1;

  flex: 1;

  padding-top: 64px;

  padding-bottom: 96px;
}

/* =========================================================
   PAGE TRANSITION
========================================================= */

.page-view {
  opacity: 0;

  transform: translateY(6px);

  animation:
    fadeInPage 0.45s ease forwards;
}

@keyframes fadeInPage {

  to {
    opacity: 1;

    transform: translateY(0);
  }
}

/* =========================================================
   RESPONSIVE
========================================================= */

@media (max-width: 768px) {

  .main-content {
    padding-top: 48px;

    padding-bottom: 64px;
  }

  .app-wrapper::before,
  .app-wrapper::after {
    width: 260px;
    height: 260px;
  }
}

</style>