<script setup>
import { ref } from 'vue'
import { useFavorites } from '@/composables/useFavorites'
import PageLayout from '@/components/PageLayout.vue'
import { useRouter } from 'vue-router'

const { favorites, toggleFavorite } = useFavorites()
const router = useRouter()

const goToRecord = (id) => {
  router.push(`/record/${id}`)
}

// carrusel ref
const carouselRef = ref(null)

// scroll dinámico con ratón
const handleMouseMove = (e) => {
  const el = carouselRef.value
  if (!el) return

  const rect = el.getBoundingClientRect()
  const x = e.clientX - rect.left
  const width = rect.width

  const edgeSize = 120 // zona lateral sensible

  // izquierda
  if (x < edgeSize) {
    el.scrollBy({ left: -15, behavior: 'auto' })
  }

  // derecha
  if (x > width - edgeSize) {
    el.scrollBy({ left: 15, behavior: 'auto' })
  }
}
</script>

<template>
   <PageLayout @basic-search="onBasicSearch">
  <div class="favorites-background">

    <v-sheet class="favorites-window pa-6" elevation="10" rounded="lg">

      <!-- VACÍO -->
      <div v-if="favorites.length === 0" class="text-center py-10">
        <v-icon size="80" color="grey">
          mdi-image-off-outline
        </v-icon>
        <p class="text-grey mt-2">
          No hay piezas en la exposición
        </p>
      </div>

      <!-- GRID -->
      <v-row v-else-if="favorites.length <= 3" dense>
        <v-col
          v-for="item in favorites"
          :key="item.id"
          cols="12"
          sm="6"
          md="4"
        >
          <v-card class="expo-card" hover>

            <v-btn
              icon
              class="favorite-btn"
              @click.stop="toggleFavorite(item)"
            >
              <v-icon color="red">mdi-heart</v-icon>
            </v-btn>

            <div @click="goToRecord(item.id)">
              <v-img
                :src="item.image"
                height="240"
                cover
                class="expo-image"
              />

              <div class="expo-overlay">
                <span class="expo-title">
                  {{ item.title }}
                </span>
              </div>
            </div>

          </v-card>
        </v-col>
      </v-row>

      <!-- CARRUSEL -->
      <div
        v-else
        ref="carouselRef"
        class="favorites-carousel"
        @mousemove="handleMouseMove"
      >

        <div
          v-for="item in favorites"
          :key="item.id"
          class="carousel-item"
        >
          <v-card class="expo-card">

            <!--  quitar -->
            <v-btn
              icon
              class="favorite-btn"
              @click.stop="toggleFavorite(item)"
            >
              <v-icon color="red">mdi-heart</v-icon>
            </v-btn>

            <div @click="goToRecord(item.id)">

              <v-img
                :src="item.image"
                height="260"
                cover
                class="expo-image"
              />

              <div class="expo-overlay">
                <span class="expo-title">
                  {{ item.title }}
                </span>
              </div>

            </div>

          </v-card>
        </div>

      </div>

    </v-sheet>

  </div>
  </PageLayout>
</template>

<style scoped>
/* =========================
   FONDO GENERAL
========================= */

.favorites-background {
  position: relative;

  min-height: 100%;

  display: flex;

  justify-content: center;
  align-items: flex-start;

  background:
    radial-gradient(
      circle at top,
      rgba(0,153,255,0.08),
      transparent 55%
    );
}

/* glow ambiental */
.favorites-background::before {
  content: "";

  position: fixed;

  top: -180px;
  right: -120px;

  width: 420px;
  height: 420px;

  border-radius: 50%;

  background:
    radial-gradient(
      circle,
      rgba(0,153,255,0.14),
      transparent 72%
    );

  filter: blur(28px);

  pointer-events: none;

  z-index: 0;
}

/* =========================
   CONTENEDOR PRINCIPAL
========================= */

.favorites-window {
  position: relative;

  z-index: 1;

  width: 100%;

  max-width: 1280px;

  overflow: hidden;

  border-radius: 32px !important;

  background:
    linear-gradient(
      145deg,
      rgba(8,16,28,0.96),
      rgba(10,18,34,0.94),
      rgba(8,12,24,0.98)
    ) !important;

  border:
    1px solid rgba(0,153,255,0.22);

  backdrop-filter: blur(24px);

  box-shadow:
    0 18px 48px rgba(0,0,0,0.48),
    0 0 24px rgba(0,153,255,0.14),
    0 0 64px rgba(0,153,255,0.06);
}

/* glow interior */
.favorites-window::before {
  content: "";

  position: absolute;

  inset: 0;

  background:
    radial-gradient(
      circle at top right,
      rgba(0,153,255,0.12),
      transparent 60%
    );

  pointer-events: none;
}

/* borde glow */
.favorites-window::after {
  content: "";

  position: absolute;

  inset: 0;

  border-radius: inherit;

  border:
    1px solid rgba(0,255,255,0.06);

  pointer-events: none;

  box-shadow:
    inset 0 0 24px rgba(0,153,255,0.08);
}

/* =========================
   TARJETAS
========================= */

.expo-card {
  position: relative;

  overflow: hidden;

  border-radius: 24px !important;

  background:
    linear-gradient(
      145deg,
      rgba(14,22,38,0.98),
      rgba(10,16,30,0.94)
    ) !important;

  border:
    1px solid rgba(0,153,255,0.16);

  cursor: pointer;

  transition:
    transform 0.3s ease,
    box-shadow 0.3s ease,
    border-color 0.3s ease,
    background 0.3s ease;
}

/* glow principal */
.expo-card::before {
  content: "";

  position: absolute;

  inset: -1px;

  border-radius: inherit;

  background:
    linear-gradient(
      135deg,
      rgba(0,255,255,0.14),
      transparent 42%,
      rgba(0,153,255,0.16)
    );

  opacity: 0;

  transition:
    opacity 0.35s ease;

  pointer-events: none;
}

/* glow ambiental */
.expo-card::after {
  content: "";

  position: absolute;

  inset: 0;

  background:
    radial-gradient(
      circle at top right,
      rgba(0,153,255,0.12),
      transparent 68%
    );

  opacity: 0;

  transition:
    opacity 0.35s ease;

  pointer-events: none;
}

.expo-card:hover {
  transform:
    translateY(-5px)
    scale(1.01);

  border-color:
    rgba(0,255,255,0.34);

  box-shadow:
    0 18px 42px rgba(0,0,0,0.46),
    0 0 18px rgba(0,153,255,0.18),
    0 0 42px rgba(0,255,255,0.08);

  background:
    linear-gradient(
      145deg,
      rgba(18,28,46,0.98),
      rgba(10,18,34,0.96)
    );
}

.expo-card:hover::before,
.expo-card:hover::after {
  opacity: 1;
}

/* =========================
   IMAGEN
========================= */

.expo-image {
  transition:
    transform 0.5s ease,
    filter 0.35s ease;

  filter:
    saturate(108%)
    contrast(104%);
}

.expo-card:hover .expo-image {
  transform: scale(1.05);

  filter:
    saturate(114%)
    contrast(108%)
    brightness(1.03);
}

/* =========================
   BOTÓN FAVORITO
========================= */

.favorite-btn {
  position: absolute;

  top: 12px;
  right: 12px;

  z-index: 5;

  width: 42px !important;
  height: 42px !important;

  border-radius: 50% !important;

  background:
    rgba(8,14,28,0.72) !important;

  backdrop-filter: blur(14px);

  border:
    1px solid rgba(0,153,255,0.24);

  box-shadow:
    0 0 18px rgba(0,153,255,0.12);

  transition:
    transform 0.25s ease,
    border-color 0.25s ease,
    box-shadow 0.25s ease,
    background 0.25s ease;
}

.favorite-btn:hover {
  transform: scale(1.06);

  border-color:
    rgba(0,255,255,0.34);

  background:
    rgba(0,153,255,0.12) !important;

  box-shadow:
    0 0 18px rgba(0,153,255,0.22),
    0 0 34px rgba(0,255,255,0.08);
}

/* =========================
   OVERLAY
========================= */

.expo-overlay {
  position: absolute;

  bottom: 0;
  left: 0;

  width: 100%;

  padding: 20px;

  background:
    linear-gradient(
      to top,
      rgba(4,8,18,0.94),
      rgba(4,8,18,0.18),
      transparent
    );
}

/* =========================
   TÍTULO
========================= */

.expo-title {
  color:
    rgba(255,255,255,0.96);

  font-size: 1rem;

  font-weight: 600;

  line-height: 1.45;

  letter-spacing: -0.01em;

  text-shadow:
    0 0 14px rgba(0,153,255,0.24);

  display: -webkit-box;

  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;

  overflow: hidden;
}

/* =========================
   CARRUSEL
========================= */

.favorites-carousel {
  display: flex;

  gap: 24px;

  overflow-x: auto;

  padding: 12px 4px;

  scroll-behavior: smooth;

  scrollbar-width: none;
}

/* ocultar scrollbar */
.favorites-carousel::-webkit-scrollbar {
  display: none;
}

/* =========================
   ITEM
========================= */

.carousel-item {
  flex: 0 0 auto;

  width: 280px;

  transition:
    transform 0.28s ease,
    opacity 0.28s ease;
}

.carousel-item:hover {
  transform: translateY(-6px);

  z-index: 3;
}

/* =========================
   EMPTY STATE
========================= */

.text-center {
  text-align: center;
}

.text-grey {
  color:
    rgba(255,255,255,0.5);
}

/* =========================
   RESPONSIVE
========================= */

@media (max-width: 768px) {

  .favorites-window {
    border-radius: 22px !important;

    padding: 20px !important;
  }

  .carousel-item {
    width: 220px;
  }

  .expo-title {
    font-size: 0.92rem;
  }

  .favorite-btn {
    width: 38px !important;
    height: 38px !important;
  }

  .favorites-background::before {
    width: 260px;
    height: 260px;
  }
}
</style>