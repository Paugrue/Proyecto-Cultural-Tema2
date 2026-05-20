<template>
  <v-card
    class="base-card"
    variant="flat"
    @click="$emit('click')"
  >

    <!-- IMAGE -->
    <div class="image-wrapper">


      <v-img
        v-if="safeImage"
        :src="safeImage"
        height="240"
        cover
        class="card-image"
        @error="handleImageError"
      >

        <template #placeholder>
          <div class="image-loading">
            <v-progress-circular indeterminate />
          </div>
        </template>

      </v-img>

      <!-- FALLBACK -->
      <div
        v-else
        class="image-fallback"
      >
        <v-icon
          size="42"
          color="grey-lighten-1"
        >
          mdi-image-off-outline
        </v-icon>
      </div>

      <!-- OVERLAY -->
      <div class="card-overlay">

        <div
          v-if="subtitle"
          class="card-subtitle"
        >
          {{ subtitle }}
        </div>

        <div class="card-title">
          {{ title }}
        </div>

      </div>

    </div>

    <!-- DESCRIPTION -->
    <div
      v-if="description"
      class="card-content"
    >

      <div class="card-description">
        {{ description }}
      </div>

    </div>

  </v-card>
</template>

<script setup>
import {
  ref,
  watch,
  computed
} from 'vue'



const props = defineProps({
  id: [String, Number],

  image: String,

  title: String,

  subtitle: String,

  description: String
})

const safeImage = ref(props.image)

watch(
  () => props.image,
  (val) => {
    safeImage.value = val
  }
)

function handleImageError() {
  safeImage.value = null
}


</script>

<style scoped>

/* =========================
   CARD — MINIMAL + NEON SUAVE
========================= */

.base-card {
  width: 100%;

  overflow: hidden;

  border-radius: 18px;

  background:
    rgba(255,255,255,0.78) !important;

  backdrop-filter:
    blur(16px);

  cursor: pointer;

  border:
    1px solid rgba(255,255,255,0.42);

  box-shadow:
    0 10px 30px rgba(0,0,0,0.05);

  transition:
    transform 0.35s ease,
    box-shadow 0.35s ease,
    border-color 0.35s ease;
}

.base-card:hover {
  transform: translateY(-4px);

  border-color:
    rgba(0,255,208,0.20);

  box-shadow:
    0 14px 34px rgba(0,0,0,0.08),
    0 0 18px rgba(0,255,208,0.10);
}

/* =========================
   IMAGE WRAPPER
========================= */

.image-wrapper {
  position: relative;

  width: 100%;

  height: 350px;

  overflow: hidden;
}

/* =========================
   FAVORITE BUTTON
========================= */

.favorite-btn {
  position: absolute;

  top: 12px;
  right: 12px;

  z-index: 20;

  background:
    rgba(0,0,0,0.35) !important;

  backdrop-filter:
    blur(8px);

  border:
    1px solid rgba(255,255,255,0.15);

  transition:
    transform 0.2s ease,
    background 0.2s ease;
}

.favorite-btn:hover {
  transform: scale(1.08);

  background:
    rgba(0,0,0,0.48) !important;
}

/* =========================
   IMAGE
========================= */

.card-image {
  width: 100%;

  height: 100%;

  transition:
    transform 0.8s ease,
    filter 0.4s ease;

  filter:
    contrast(104%)
    saturate(102%);
}

.base-card:hover .card-image {
  transform: scale(1.03);

  filter:
    contrast(106%)
    saturate(105%);
}

/* =========================
   OVERLAY
========================= */

.card-overlay {
  position: absolute;

  inset: 0;

  display: flex;

  flex-direction: column;

  justify-content: flex-end;

  padding:
    20px
    24px
    16px;

  background:
    linear-gradient(
      to top,
      rgba(0,0,0,0.68),
      rgba(0,0,0,0.12),
      transparent
    );
}

/* Glow muy sutil */
.card-overlay::before {
  content: "";

  position: absolute;

  inset: 0;

  background:
    radial-gradient(
      circle at bottom left,
      rgba(0,255,208,0.08),
      transparent 60%
    );

  pointer-events: none;

  opacity: 0;

  transition: opacity 0.35s ease;
}

.base-card:hover .card-overlay::before {
  opacity: 1;
}

/* =========================
   FALLBACK
========================= */

.image-fallback {
  position: absolute;

  inset: 0;

  display: flex;

  align-items: center;

  justify-content: center;

  background:
    linear-gradient(
      135deg,
      #f3f4f6,
      #e5e7eb
    );

  color:
    var(--neon-cyan);
}

/* =========================
   LOADING
========================= */

.image-loading {
  width: 100%;

  height: 100%;

  display: flex;

  align-items: center;

  justify-content: center;

  background:
    #f5f5f5;
}

/* =========================
   SUBTITLE
========================= */

.card-subtitle {
  margin-bottom: 6px;

  font-size: 0.68rem;

  font-weight: 700;

  text-transform: uppercase;

  letter-spacing: 0.12em;

  color:
    rgba(255,255,255,0.78);

  text-shadow:
    0 0 8px rgba(0,255,208,0.12);

  display: -webkit-box;

  -webkit-line-clamp: 1;

  -webkit-box-orient: vertical;

  overflow: hidden;
}

/* =========================
   TITLE
========================= */

.card-title {
  font-family:
    "Archivo Black",
    sans-serif;

  font-size: 1.75rem;

  line-height: 0.92;

  letter-spacing: -0.04em;

  text-transform: uppercase;

  margin: 0;

  color: #ffffff;

  text-shadow:
    0 0 10px rgba(0,255,208,0.10);

  display: -webkit-box;

  -webkit-line-clamp: 3;

  -webkit-box-orient: vertical;

  overflow: hidden;
}

/* =========================
   DESCRIPTION
========================= */

.card-content {
  padding:
    8px
    18px
    16px;
}

.card-description {
  font-size: 0.92rem;

  line-height: 1.5;

  color:
    rgba(17,17,17,0.68);

  display: -webkit-box;

  -webkit-line-clamp: 3;

  -webkit-box-orient: vertical;

  overflow: hidden;
}

/* =========================
   RESPONSIVE
========================= */

@media (max-width: 960px) {

  .image-wrapper {
    height: 360px;
  }

  .card-overlay {
    padding:
      16px
      18px
      14px;
  }

  .card-title {
    font-size: 1.35rem;

    line-height: 0.95;
  }

  .card-subtitle {
    font-size: 0.62rem;

    margin-bottom: 4px;
  }

  .card-content {
    padding:
      6px
      14px
      14px;
  }
}

</style>