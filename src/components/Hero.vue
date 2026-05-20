<template>
  <div
    class="hero"
    :style="{ backgroundImage: `url(${heroImage})` }"
  >
    <div class="hero-overlay">
      <h1>Sitio de Granada</h1>
      <p>Bienvenido al nuevo sitio de Granada.</p>
    </div>
  </div>
</template>

<script setup>
// Importa la imagen 
import heroImage from '@/assets/sitioGranada.jpg'
</script>


<style scoped>
/* =========================================================
   HERO — MINIMAL + NEON SUAVE
========================================================= */

.hero {
  width: 100%;

  aspect-ratio: 16 / 9;

  min-height: 180px;
  max-height: 280px;

  background-size: cover;
  background-position: center;

  position: relative;

  overflow: hidden;

  border-radius: 18px;

  border:
    1px solid rgba(255,255,255,0.38);

  box-shadow:
    0 12px 34px rgba(0,0,0,0.06);

  transition:
    transform 0.35s ease,
    box-shadow 0.35s ease,
    border-color 0.35s ease;
}

.hero:hover {
  transform:
    translateY(-3px);

  border-color:
    rgba(0,255,208,0.16);

  box-shadow:
    0 18px 40px rgba(0,0,0,0.08),
    0 0 18px rgba(0,255,208,0.08);
}

/* =========================================================
   GLOW SUAVE
========================================================= */

.hero::before {
  content: "";

  position: absolute;

  inset: 0;

  background:
    radial-gradient(
      circle at top right,
      rgba(0,255,208,0.10),
      transparent 45%
    );

  opacity: 0;

  transition:
    opacity 0.35s ease;

  pointer-events: none;
}

.hero:hover::before {
  opacity: 1;
}

/* =========================================================
   OVERLAY
========================================================= */

.hero-overlay {
  position: absolute;

  bottom: 0;

  width: 100%;

  padding: 32px 28px;

  background:
    linear-gradient(
      to top,
      rgba(0,0,0,0.68),
      rgba(0,0,0,0.28),
      transparent
    );
}

/* =========================================================
   TITULO
========================================================= */

.hero.hero .hero-overlay h1 {
  font-family:
    "Archivo Black",
    sans-serif;

  font-size:
    clamp(1.8rem, 4vw, 2.4rem);

  font-weight:
    500;

  line-height:
    1.1;

  margin-bottom:
    8px;

  letter-spacing:
    -0.03em;

  text-transform:
    uppercase;

  color:
    #ffffff !important;

  text-shadow:
    0 0 10px rgba(0,255,208,0.10);
}

/* =========================================================
   TEXTO
========================================================= */

.hero.hero .hero-overlay p {
  font-family:
    "Inter",
    sans-serif;

  font-size:
    15px;

  margin:
    0;

  max-width:
    520px;

  color:
    rgba(255,255,255,0.82) !important;

  line-height:
    1.6;

  text-shadow:
    0 0 8px rgba(0,255,208,0.06);
}

/* =========================================================
   RESPONSIVE
========================================================= */

@media (max-width: 768px) {

  .hero {
    max-height: 220px;

    border-radius: 14px;
  }

  .hero-overlay {
    padding: 20px 16px;
  }

  .hero.hero .hero-overlay h1 {
    font-size: 1.6rem;
  }

  .hero.hero .hero-overlay p {
    font-size: 13px;
  }
}
</style>