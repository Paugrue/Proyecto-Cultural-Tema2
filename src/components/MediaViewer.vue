<template>

  <!-- GALLERY -->
  <div class="media-grid">

    <div
      v-for="(m, i) in items"
      :key="m.id || i"
      class="media-thumb"
      @click="open(i)"
    >

      <!-- IMAGE -->
      <img
        v-if="m.type === 'image'"
        :src="m.thumbnail || m.full"
        class="thumb-image"
      />

      <!-- FILE -->
      <div
        v-else
        class="media-fallback"
      >

        <v-icon
          size="34"
          color="white"
        >
          mdi-file
        </v-icon>

      </div>

    </div>

  </div>

  <!-- VIEWER -->
  <v-dialog
    v-model="dialog"
    fullscreen
    transition="fade-transition"
  >

    <v-card class="viewer-card">

      <!-- TOPBAR -->
      <div class="viewer-topbar">

        <div class="viewer-counter">
          {{ index + 1 }} / {{ items.length }}
        </div>

        <v-btn
          icon="mdi-close"
          variant="text"
          color="white"
          @click="dialog = false"
        />

      </div>

      <!-- LEFT -->
      <v-btn
        icon="mdi-chevron-left"
        class="nav-button nav-left"
        variant="text"
        color="white"
        @click="prev"
      />

      <!-- RIGHT -->
      <v-btn
        icon="mdi-chevron-right"
        class="nav-button nav-right"
        variant="text"
        color="white"
        @click="next"
      />

      <!-- CONTENT -->
      <div class="viewer-content">

        <!-- IMAGE -->
        <img
          v-if="current?.type === 'image'"
          :src="current.full"
          class="viewer-image"
        />

        <!-- VIDEO -->
        <video
          v-else-if="current?.type === 'video'"
          controls
          :src="current.full"
          class="viewer-video"
        />

        <!-- PDF -->
        <iframe
          v-else-if="current?.type === 'pdf'"
          :src="current.full"
          class="viewer-pdf"
        />

        <!-- FALLBACK -->
        <div
          v-else
          class="viewer-unsupported"
        >
          Archivo no soportado
        </div>

      </div>

    </v-card>

  </v-dialog>

</template>

<script setup>
import {
  ref,
  computed
} from "vue"

const props = defineProps({
  items: {
    type: Array,
    default: () => []
  }
})

const dialog = ref(false)

const index = ref(0)

const current = computed(
  () => props.items[index.value]
)

/* =========================
   OPEN
========================= */

const open = (i) => {

  index.value = i

  dialog.value = true
}

/* =========================
   NEXT
========================= */

const next = () => {

  index.value =
    (index.value + 1)
    % props.items.length
}

/* =========================
   PREV
========================= */

const prev = () => {

  index.value =
    index.value === 0
      ? props.items.length - 1
      : index.value - 1
}
</script>

<style scoped>

/* =========================
   GRID
========================= */

.media-grid {
  display: grid;

  grid-template-columns:
    repeat(
      auto-fill,
      minmax(180px, 1fr)
    );

  gap: 18px;
}

/* =========================
   THUMB — EDITORIAL + NEON
========================= */

.media-thumb {
  position: relative;

  height: 240px;

  overflow: hidden;

  cursor: pointer;

  background:
    rgba(255,255,255,0.72);

  backdrop-filter: blur(16px);

  border:
    1px solid rgba(255,255,255,0.42);

  border-radius: 18px;

  box-shadow:
    0 8px 24px rgba(0,0,0,0.05);

  transition:
    transform 0.3s ease,
    border-color 0.3s ease,
    box-shadow 0.3s ease;
}

.media-thumb:hover {
  transform: translateY(-3px);

  border-color:
    rgba(0,255,208,0.22);

  box-shadow:
    0 12px 28px rgba(0,0,0,0.08),
    0 0 18px rgba(0,255,208,0.12);
}

/* =========================
   IMAGE
========================= */

.thumb-image {
  width: 100%;

  height: 100%;

  object-fit: cover;

  transition:
    transform 0.7s ease,
    filter 0.35s ease;

  filter:
    contrast(104%)
    saturate(104%);
}

.media-thumb:hover .thumb-image {
  transform: scale(1.03);

  filter:
    contrast(106%)
    saturate(108%);
}

/* =========================
   FALLBACK
========================= */

.media-fallback {
  width: 100%;

  height: 100%;

  display: flex;

  align-items: center;

  justify-content: center;

  background:
    rgba(255,255,255,0.82);

  color:
    #00cfc8;

  text-shadow:
    0 0 10px rgba(0,255,208,0.12);
}

/* =========================
   VIEWER
========================= */

.viewer-card {
  width: 100%;

  height: 100vh;

  background:
    rgba(245,245,247,0.96) !important;

  backdrop-filter: blur(18px);

  overflow: hidden;

  position: relative;
}

/* =========================
   TOPBAR
========================= */

.viewer-topbar {
  position: absolute;

  top: 0;

  left: 0;

  width: 100%;

  z-index: 20;

  display: flex;

  align-items: center;

  justify-content: space-between;

  padding:
    18px
    22px;

  background:
    linear-gradient(
      to bottom,
      rgba(255,255,255,0.88),
      rgba(255,255,255,0.35),
      transparent
    );

  backdrop-filter: blur(14px);

  border-bottom:
    1px solid rgba(255,255,255,0.4);
}

.viewer-counter {
  font-size: 0.9rem;

  letter-spacing: 0.04em;

  color:
    rgba(17,17,17,0.68);

  transition:
    color 0.25s ease,
    text-shadow 0.25s ease;
}

.viewer-counter:hover {
  color:
    #00cfc8;

  text-shadow:
    0 0 8px rgba(0,255,208,0.14);
}

/* =========================
   CONTENT
========================= */

.viewer-content {
  width: 100%;

  height: 100vh;

  display: flex;

  align-items: center;

  justify-content: center;

  padding: 60px;
}

/* =========================
   MEDIA
========================= */

.viewer-image,
.viewer-video,
.viewer-pdf {
  max-width: 100%;

  max-height: 90vh;

  border-radius: 14px;

  box-shadow:
    0 10px 32px rgba(0,0,0,0.08);
}

.viewer-image {
  object-fit: contain;
}

/* =========================
   PDF
========================= */

.viewer-pdf {
  width: 100%;

  height: 90vh;

  border: none;
}

/* =========================
   NAV BUTTONS
========================= */

.nav-button {
  position: absolute !important;

  top: 50%;

  transform: translateY(-50%);

  z-index: 30;

  width: 54px !important;

  height: 54px !important;

  background:
    rgba(255,255,255,0.72) !important;

  backdrop-filter: blur(14px);

  border:
    1px solid rgba(255,255,255,0.42);

  box-shadow:
    0 8px 24px rgba(0,0,0,0.06);

  transition:
    transform 0.25s ease,
    border-color 0.25s ease,
    box-shadow 0.25s ease,
    color 0.25s ease;
}

.nav-button:hover {
  transform:
    translateY(-50%)
    scale(1.04);

  border-color:
    rgba(0,255,208,0.22);

  color:
    #00cfc8 !important;

  box-shadow:
    0 12px 28px rgba(0,0,0,0.08),
    0 0 18px rgba(0,255,208,0.12);
}

.nav-left {
  left: 20px;
}

.nav-right {
  right: 20px;
}

/* =========================
   UNSUPPORTED
========================= */

.viewer-unsupported {
  font-size: 1rem;

  color:
    rgba(17,17,17,0.6);
}

/* =========================
   RESPONSIVE
========================= */

@media (max-width: 960px) {

  .media-grid {
    grid-template-columns:
      repeat(
        auto-fill,
        minmax(140px, 1fr)
      );

    gap: 12px;
  }

  .media-thumb {
    height: 180px;
  }

  .viewer-content {
    padding: 20px;
  }

  .nav-button {
    width: 44px !important;

    height: 44px !important;
  }

  .nav-left {
    left: 10px;
  }

  .nav-right {
    right: 10px;
  }
}

</style>