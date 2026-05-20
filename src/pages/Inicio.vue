<template>
  <PageLayout>
    <div class="inicio-contenedor">
      <h2 class="inicio-title">Este es el sitio de Granada</h2>

      <div class="inicio-btn-grid">
        <RouterLink to="/collection">
          <v-btn class="inicio-btn inicio-btn--small" variant="flat" block>
            Colecciones
          </v-btn>
        </RouterLink>

        <RouterLink to="/record">
          <v-btn class="inicio-btn inicio-btn--small" variant="flat" block>
            Registros
          </v-btn>
        </RouterLink>
      </div>

      <div class="inicio-divider"></div>
    </div>

    <!-- Destacados -->
    <v-row class="mt-8" v-if="collections.length">
      <v-col
        v-for="col in collections"
        :key="col.id"
        cols="12" sm="6" md="4" lg="3"
      >
<BaseCard
  :image="col.thumbnailFull"
  :title="col.title"
  @click="$router.push('/collection/' + col.id)"
/>
      </v-col>
    </v-row>

    <!-- PAGINACIÓN -->
    <v-row v-if="collections.length" class="mt-6">
      <v-col cols="12" class="d-flex justify-center align-center">

        <v-btn
          variant="outlined"
          :disabled="page === 1"
          @click="changePage(page - 1)"
        >
          ←
        </v-btn>

        <div class="mx-4" style="min-width: 40px; text-align:center; font-weight:600;">
          {{ page }}
        </div>

        <v-btn
          variant="outlined"
          :disabled="!hasNextPage"
          @click="changePage(page + 1)"
        >
          →
        </v-btn>

      </v-col>
    </v-row>

  </PageLayout>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import PageLayout from '@/components/PageLayout.vue'
import api from '@/services/api'
import BaseCard from '@/components/BaseCard.vue'

const collections = ref([])
const API_BASE = 'https://arcadium.cluster24.libnamic.eu'

/* PAGINACIÓN */
const page = ref(1)
const limit = 6
const hasNextPage = ref(false)

/* IMÁGENES */
function normalizeThumb(th) {
  if (!th) return '/placeholder.png'
  if (/^https?:\/\//i.test(th)) return th
  return `${API_BASE}${th.startsWith('/') ? '' : '/'}${th}`
}

/* FETCH */
async function fetchCollections() {
  try {
    const { data } = await api.getCollections({
      with_labels: 1,
      fields: 'id,title,thumbnail',
      limit: limit,
      offset: (page.value - 1) * limit
    })

    const items = data.items || data.results || data || []

    collections.value = items.map(c => ({
      ...c,
      thumbnailFull: normalizeThumb(c.thumbnail)
    }))

    hasNextPage.value = items.length === limit

  } catch (e) {
    console.error('Error cargando colecciones destacadas:', e)
  }
}

/* CAMBIO DE PÁGINA */
function changePage(newPage) {
  if (newPage < 1) return
  if (newPage > page.value && !hasNextPage.value) return

  page.value = newPage
  fetchCollections()
}

onMounted(fetchCollections)
</script>

<style scoped>

/* =========================================================
   HERO / TITULOS
========================================================= */

.inicio-title {
  position: relative;

  font-family: var(--font-serif);

  font-size: clamp(2.4rem, 5vw, 3.2rem);

  font-weight: 500;

  margin-bottom: 24px;

  text-align: center;

  letter-spacing: -0.3px;

  color:
    #111111;

  text-shadow:
    0 0 8px rgba(0,255,208,0.12),
    0 0 18px rgba(0,255,208,0.08);
}

.inicio-title::after {
  content: "";

  display: block;

  width: 120px;
  height: 2px;

  margin: 18px auto 0 auto;

  background:
    linear-gradient(
      90deg,
      transparent,
      rgba(0,255,208,0.65),
      transparent
    );

  box-shadow:
    0 0 10px rgba(0,255,208,0.18);
}

/* =========================================================
   BOTONES
========================================================= */

.inicio-btn-grid {
  display: flex;

  justify-content: center;

  gap: 24px;
}

.inicio-btn {
  border-radius: 14px !important;

  box-shadow: none !important;

  transition:
    transform 0.25s ease,
    box-shadow 0.25s ease,
    border-color 0.25s ease,
    background 0.25s ease;
}

.inicio-btn--small {
  height: 42px !important;

  font-size: 14px !important;

  font-weight: 600 !important;

  letter-spacing: 0.02em;

  padding: 0 20px !important;

  background:
    rgba(255,255,255,0.58) !important;

  color:
    #111111 !important;

  border:
    1px solid rgba(255,255,255,0.22) !important;

  backdrop-filter: blur(14px);

  box-shadow:
    0 4px 16px rgba(0,0,0,0.04),
    0 0 10px rgba(0,255,208,0.04);
}

.inicio-btn--small:hover {
  transform: translateY(-2px);

  border-color:
    rgba(0,255,208,0.28) !important;

  color:
    #00ffd0 !important;

  text-shadow:
    0 0 6px rgba(0,255,208,0.55);

  box-shadow:
    0 0 14px rgba(0,255,208,0.12);
}

/* =========================================================
   DIVIDER
========================================================= */

.inicio-divider {
  width: 100%;

  max-width: 420px;

  height: 1px;

  margin: 56px auto 0 auto;

  background:
    linear-gradient(
      90deg,
      transparent,
      rgba(0,255,208,0.35),
      transparent
    );

  box-shadow:
    0 0 10px rgba(0,255,208,0.08);
}

/* =========================================================
   HOVERABLE
========================================================= */

.hoverable {
  position: relative;

  transition:
    transform 0.25s ease,
    box-shadow 0.25s ease,
    border-color 0.25s ease;
}

.hoverable:hover {
  transform: translateY(-3px);

  box-shadow:
    0 8px 22px rgba(0,0,0,0.05),
    0 0 14px rgba(0,255,208,0.08);
}

/* =========================================================
   RESPONSIVE
========================================================= */

@media (max-width: 768px) {

  .inicio-btn-grid {
    flex-direction: column;

    align-items: stretch;
  }

  .inicio-btn {
    width: 100%;
  }

  .inicio-title::after {
    width: 90px;
  }
}

</style>