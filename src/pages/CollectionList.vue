<template>
  <PageLayout>
    <v-row>

      <!-- LOADING -->
      <v-col cols="12" v-if="loading">
        <v-row>
          <v-col v-for="i in 8" :key="i" cols="12" sm="6" md="4" lg="3">
            <v-skeleton-loader type="image, article" class="mb-4" />
          </v-col>
        </v-row>
      </v-col>

      <!-- LISTADO -->
      <v-col
        v-else
        v-for="col in collections"
        :key="col.id"
        cols="12"
        sm="6"
        md="4"
        lg="3"
      >
<BaseCard
  :image="col.imageDisplay"
  :title="col.title"
  @click="$router.push('/collection/' + col.id)"
/>
      </v-col>

      <!-- EMPTY -->
      <v-col
        v-if="!loading && collections.length === 0"
        cols="12"
        class="text-center py-12"
      >
        <v-icon size="64" color="grey-lighten-2">
          mdi-database-off-outline
        </v-icon>

        <p class="text-grey-darken-1 mt-4 text-h6">
          No se han encontrado colecciones.
        </p>

        <v-btn
          variant="text"
          color="primary"
          class="mt-2"
          @click="$router.push('/collection')"
        >
          Ver todas las colecciones
        </v-btn>
      </v-col>

      <!-- PAGINACIÓN -->
      <v-col
        cols="12"
        class="d-flex justify-center mt-6"
        v-if="showPagination"
      >
        <div class="pagination-wrapper">

          <v-btn
            variant="outlined"
            :disabled="page === 1"
            @click="changePage(page - 1)"
          >
            ←
          </v-btn>

          <div class="page-number">
            {{ page }}
          </div>

          <v-btn
            variant="outlined"
            :disabled="!hasNextPage"
            @click="changePage(page + 1)"
          >
            →
          </v-btn>

        </div>
      </v-col>

    </v-row>
  </PageLayout>
</template>

<script setup>
import { ref, watch, computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import PageLayout from '@/components/PageLayout.vue'
import api from '@/services/api'
import BaseCard from '@/components/BaseCard.vue'

const route = useRoute()
const router = useRouter()

const collections = ref([])
const loading = ref(false)

const page = ref(parseInt(route.query.page) || 1)

const limit = 6
const hasNextPage = ref(false)

const API_BASE = 'https://arcadium.cluster24.libnamic.eu'

/* =========================
   HELPERS
========================= */

function processCollection(item) {
  let tags = []
  const rawMetadata = item.joined_metadata || item.metadata || ""

  if (Array.isArray(rawMetadata)) {
    tags = rawMetadata.map(t =>
      typeof t === 'object' ? (t.value || t['@value'] || '') : t
    )
  } else if (typeof rawMetadata === 'string' && rawMetadata.length > 0) {
    tags = rawMetadata.split(',').map(s => s.trim()).filter(Boolean)
  }

  let img = item.preview || item.thumbnail || item.image || '/placeholder.png'

  if (img !== '/placeholder.png' && !img.startsWith('http')) {
    img = `${API_BASE}${img.startsWith('/') ? '' : '/'}${img}`
  }

  return {
    ...item,
    id: item.id || Math.random(),
    imageDisplay: img,
    cleanTags: tags.filter(Boolean)
  }
}

/* =========================
   FETCH DATA
========================= */

async function fetchData() {
  loading.value = true

  const scopes = (route.query.scope || 'collections').split(',')

  try {
    let activeFilters = []

    if (route.query.rules) {
      try {
        activeFilters = JSON.parse(route.query.rules)
      } catch (e) {
        console.warn("Error parseando reglas:", e)
      }
    }

    const params = {
      with_labels: 1,
      fields: 'id,title,thumbnail,preview,description,joined_metadata',

      limit: limit,
      offset: (page.value - 1) * limit,

      search: route.query.q?.trim() || '',
      combine: route.query.combine || 'AND',
    }

    if (activeFilters.length > 0) {
      params.filters = activeFilters
    }

    if (route.query.sortBy) params.sort = route.query.sortBy
    if (route.query.sortDir) params.direction = route.query.sortDir

    const response = await api.getCollections(params)

    const apiData =
      response.data?.data ||
      response.data?.items ||
      (Array.isArray(response.data) ? response.data : [])

    collections.value = apiData.map(processCollection)

    hasNextPage.value = apiData.length === limit

  } catch (error) {
    console.error("Error en fetchData collections:", error)
    collections.value = []
    hasNextPage.value = false
  } finally {
    loading.value = false
  }
}

/* =========================
   PAGINACIÓN
========================= */

function changePage(newPage) {
  if (newPage < 1) return
  if (newPage > page.value && !hasNextPage.value) return

  page.value = newPage

  router.push({
    query: {
      ...route.query,
      page: newPage
    }
  })

  window.scrollTo({ top: 0, behavior: 'smooth' })
}

const showPagination = computed(() => {
  return page.value > 1 || hasNextPage.value
})

/* =========================
   WATCHERS
========================= */

watch(() => route.query, fetchData, { immediate: true, deep: true })

watch(() => route.query.page, (p) => {
  page.value = parseInt(p) || 1
})
</script>

<style scoped>
.collection-card {
  position: relative;

  cursor: pointer;

  overflow: hidden;

  border-radius: 22px;

  background:
    linear-gradient(
      145deg,
      rgba(12,12,12,0.94),
      rgba(18,18,18,0.9)
    );

  border:
    1px solid rgba(0,255,200,0.12);

  backdrop-filter: blur(18px);

  transition:
    transform 0.28s ease,
    box-shadow 0.28s ease,
    border-color 0.28s ease,
    background 0.28s ease;
}

/* glow exterior */
.collection-card::before {
  content: "";

  position: absolute;

  inset: -1px;

  border-radius: inherit;

  background:
    linear-gradient(
      135deg,
      rgba(0,255,200,0.16),
      transparent 42%,
      rgba(0,153,255,0.12)
    );

  opacity: 0;

  transition:
    opacity 0.35s ease;

  pointer-events: none;
}

/* glow ambiental */
.collection-card::after {
  content: "";

  position: absolute;

  inset: 0;

  background:
    radial-gradient(
      circle at top right,
      rgba(0,255,200,0.08),
      transparent 68%
    );

  opacity: 0;

  transition:
    opacity 0.35s ease;

  pointer-events: none;
}

.collection-card:hover {
  transform:
    translateY(-4px)
    scale(1.005);

  border-color:
    rgba(0,255,200,0.34);

  box-shadow:
    0 14px 34px rgba(0,0,0,0.42),
    0 0 16px rgba(0,255,200,0.16),
    0 0 38px rgba(0,153,255,0.08);

  background:
    linear-gradient(
      145deg,
      rgba(15,15,15,0.96),
      rgba(22,22,22,0.92)
    );
}

.collection-card:hover::before,
.collection-card:hover::after {
  opacity: 1;
}

/* =========================
   IMAGE LOADING
========================= */

.image-loading {
  height: 100%;

  display: flex;

  align-items: center;
  justify-content: center;

  background:
    linear-gradient(
      135deg,
      rgba(8,8,8,0.98),
      rgba(16,16,16,0.92)
    );

  position: relative;
}

/* glow loader */
.image-loading::after {
  content: "";

  position: absolute;

  width: 140px;
  height: 140px;

  border-radius: 50%;

  background:
    radial-gradient(
      circle,
      rgba(0,255,200,0.12),
      transparent 70%
    );

  filter: blur(24px);
}

/* =========================
   TITLES
========================= */

.card-title {
  position: relative;

  z-index: 1;

  font-size: 1.02rem;

  font-weight: 700;

  line-height: 1.4;

  margin-bottom: 10px;

  color:
    rgba(255,255,255,0.94);

  letter-spacing: 0.01em;

  text-shadow:
    0 0 10px rgba(0,255,200,0.12);
}

/* =========================
   SUBTITLE
========================= */

.card-subtitle {
  font-size: 0.72rem;

  text-transform: uppercase;

  letter-spacing: 0.12em;

  color:
    rgba(0,255,200,0.78);

  margin-bottom: 10px;

  text-shadow:
    0 0 10px rgba(0,255,200,0.16);
}

/* =========================
   DESCRIPTION
========================= */

.card-description {
  font-size: 0.92rem;

  line-height: 1.7;

  color:
    rgba(255,255,255,0.66);

  display: -webkit-box;

  -webkit-box-orient: vertical;

  overflow: hidden;

  text-shadow:
    0 0 8px rgba(255,255,255,0.02);
}

/* =========================
   PAGINATION
========================= */

.pagination-wrapper {
  display: flex;

  align-items: center;
  justify-content: center;

  gap: 24px;
}

.pagination-wrapper :deep(.v-btn) {
  width: 46px !important;

  min-width: 46px !important;

  height: 46px !important;

  border-radius: 50% !important;

  background:
    rgba(255,255,255,0.04) !important;

  border:
    1px solid rgba(0,255,200,0.14) !important;

  color:
    rgba(255,255,255,0.9) !important;

  backdrop-filter: blur(12px);

  box-shadow:
    0 0 10px rgba(0,255,200,0.04);

  transition:
    transform 0.25s ease,
    border-color 0.25s ease,
    box-shadow 0.25s ease,
    background 0.25s ease;
}

.pagination-wrapper :deep(.v-btn:hover) {
  transform:
    translateY(-2px)
    scale(1.04);

  border-color:
    rgba(0,255,200,0.38) !important;

  background:
    rgba(0,255,200,0.08) !important;

  box-shadow:
    0 0 14px rgba(0,255,200,0.18),
    0 0 30px rgba(0,153,255,0.08);
}

.page-number {
  min-width: 42px;

  text-align: center;

  font-size: 1rem;

  font-weight: 700;

  color:
    rgba(255,255,255,0.94);

  text-shadow:
    0 0 10px rgba(0,255,200,0.14);
}

/* =========================
   RESPONSIVE
========================= */

@media (max-width: 768px) {

  .collection-card {
    border-radius: 18px;
  }

  .card-title {
    font-size: 0.96rem;
  }

  .card-description {
    font-size: 0.88rem;
  }

  .pagination-wrapper {
    gap: 16px;
  }

  .pagination-wrapper :deep(.v-btn) {
    width: 42px !important;

    min-width: 42px !important;

    height: 42px !important;
  }
}
</style>