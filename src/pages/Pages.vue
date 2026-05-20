<template>
  <PageLayout>
    <div class="page-header">
      <h1 class="page-title">Páginas</h1>
      <p class="page-subtitle">
        Explora las diferentes secciones y contenidos de nuestro archivo cultural.
      </p>
    </div>

    <div class="page-content">
      <slot />
    </div>
  </PageLayout>
</template>

<script setup>
import PageLayout from "@/components/PageLayout.vue"
</script>

<style scoped>
/* =========================================================
   ENCABEZADO DE PÁGINA — GLASS + NEON
========================================================= */

.page-header {
  position: relative;

  max-width: 720px;

  margin: 0 auto 56px auto;

  text-align: center;
}

/* glow ambiental */
.page-header::before {
  content: "";

  position: absolute;

  top: -120px;
  left: 50%;

  transform: translateX(-50%);

  width: 320px;
  height: 320px;

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

/* =========================================================
   TÍTULO
========================================================= */

.page-title {
  position: relative;

  z-index: 1;

  font-family: var(--font-serif);

  font-size: clamp(2.6rem, 5vw, 3.4rem);

  font-weight: 500;

  letter-spacing: -0.3px;

  margin-bottom: 20px;

  color:
    #111111;

  text-shadow:
    0 0 8px rgba(0,255,208,0.12),
    0 0 20px rgba(0,255,208,0.08);
}

/* línea neon */
.page-title::after {
  content: "";

  display: block;

  width: 120px;
  height: 2px;

  margin: 18px auto 0 auto;

  background:
    linear-gradient(
      90deg,
      transparent,
      rgba(0,255,208,0.7),
      transparent
    );

  box-shadow:
    0 0 10px rgba(0,255,208,0.18);
}

/* =========================================================
   SUBTÍTULO
========================================================= */

.page-subtitle {
  position: relative;

  z-index: 1;

  font-family: var(--font-sans);

  font-size: 17px;

  line-height: 1.7;

  max-width: 620px;

  margin: 0 auto;

  color:
    rgba(17,17,17,0.72);

  text-shadow:
    0 0 8px rgba(0,255,208,0.04);
}

/* =========================================================
   CONTENIDO
========================================================= */

.page-content {
  position: relative;

  z-index: 1;

  max-width: 960px;

  margin: 0 auto;

  padding: 0 24px;
}

/* =========================================================
   RESPONSIVE
========================================================= */

@media (max-width: 768px) {

  .page-header {
    margin-bottom: 40px;
  }

  .page-title {
    font-size: 2.2rem;
  }

  .page-title::after {
    width: 90px;
  }

  .page-content {
    padding: 0 20px;
  }

  .page-header::before {
    width: 240px;
    height: 240px;
  }
}
</style>