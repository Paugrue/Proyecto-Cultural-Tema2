<template>
  <div class="search-section">
    <div class="search-box">

      <v-text-field
        v-model="search"
        placeholder="Buscar registros, colecciones..."
        variant="outlined"
        density="comfortable"
        hide-details
        prepend-inner-icon="mdi-magnify"
        class="search-input"
        @keyup.enter="onBasicSearch"
      />

      <v-btn
        class="search-btn"
        color="primary"
        @click="onBasicSearch"
      >
        Buscar
      </v-btn>

      <v-btn
        variant="text"
        class="advanced-btn"
        color="primary"
        @click="advancedOpen = true"
      >
        Avanzada
      </v-btn>
    </div>

    <AdvancedSearchDialog
      v-model="advancedOpen"
      :fields="fields"
      :collections="collectionOptions"
      :defaults="defaults"
      @do-advanced-search="onAdvanced"
    />
  </div>
</template>

<script setup>
import { ref, watch, computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import AdvancedSearchDialog from './AdvancedSearchDialog.vue'

/* ─────────────────────────────────────────────
   Props y eventos (JS puro)
───────────────────────────────────────────── */

const props = defineProps({
  fields: { type: Array, default: () => [] },
  collections: { type: Array, default: () => [] },
  defaults: { type: Object, default: () => ({}) }
})

const emit = defineEmits([
  'doBasicSearch',
  'doAdvancedSearch'
])

const route = useRoute()
const router = useRouter()

const search = ref('')
const advancedOpen = ref(false)

/* ─────────────────────────────────────────────
   Normalización de colecciones
───────────────────────────────────────────── */

const collectionOptions = computed(() => {
  if (!props.collections.length) return []

  return props.collections
    .filter(c => c.id != null && (c.title || c.name))
    .map(c => ({
      id: c.id,
      title: c.title || c.name || 'Sin título'
    }))
})

/* ─────────────────────────────────────────────
   Sincronizar input con ?q de la URL
───────────────────────────────────────────── */

watch(
  () => route.query.q,
  val => {
    search.value = val || ''
  },
  { immediate: true }
)

/* ─────────────────────────────────────────────
   Búsqueda básica
───────────────────────────────────────────── */
function onBasicSearch() {
  const q = search.value.trim()
  if (!q) return

  router.push({
    path: '/search',
    query: {
      q,
      scope: 'all', 
      page: 1
    }
  })
}

/* ─────────────────────────────────────────────
   Búsqueda avanzada
───────────────────────────────────────────── */

function onAdvanced(payload) {
  const query = {
    q: payload.query?.trim() || undefined,
    scope: payload.scope,
    combine: payload.combine,
    page: 1
  }

  if (payload.rules?.length) {
    query.rules = JSON.stringify(payload.rules)
  }

  // Limpiar undefined / null
  Object.keys(query).forEach(k => {
    if (query[k] == null) delete query[k]
  })

  router.push({ path: '/search', query })
  advancedOpen.value = false
}
</script>


<style scoped>

/* =========================================================
   SECCIÓN DE BÚSQUEDA — GLASS LIGHT + NEON
========================================================= */

.search-section {
  position: relative;

  display: flex;

  justify-content: center;

  margin: 40px 0 56px;

  padding: 0 16px;
}

/* glow ambiental */
.search-section::before {
  content: "";

  position: absolute;

  top: -80px;

  width: 420px;
  height: 420px;

  border-radius: 50%;

  background:
    radial-gradient(
      circle,
      rgba(0,255,200,0.08),
      transparent 72%
    );

  filter: blur(24px);

  pointer-events: none;
}

/* =========================================================
   CAJA DE BÚSQUEDA
========================================================= */

.search-box {
  position: relative;
  z-index: 1;

  display: grid;

  grid-template-columns: 1fr auto auto;

  gap: 12px;

  width: 100%;

  max-width: 880px;
}

/* =========================================================
   INPUT
========================================================= */

.search-input :deep(.v-field) {
  border-radius: 16px !important;

  height: 56px;

  background:
    rgba(255,255,255,0.72) !important;

  border:
    1px solid rgba(255,255,255,0.42) !important;

  backdrop-filter: blur(16px);

  box-shadow:
    0 8px 24px rgba(0,0,0,0.05);

  transition:
    border-color 0.25s ease,
    box-shadow 0.25s ease,
    background 0.25s ease;
}

.search-input :deep(.v-field:hover),
.search-input :deep(.v-field--focused) {
  border-color:
    rgba(0,255,200,0.34) !important;

  box-shadow:
    0 0 12px rgba(0,255,200,0.12),
    0 0 28px rgba(0,255,200,0.06);

  background:
    rgba(255,255,255,0.82) !important;
}

.search-input :deep(input) {
  color:
    rgba(17,17,17,0.92) !important;

  font-family: var(--font-sans);
}

.search-input :deep(input::placeholder) {
  color:
    rgba(17,17,17,0.42) !important;
}

/* =========================================================
   BOTÓN BUSCAR
========================================================= */

.search-btn {
  border-radius: 16px !important;

  height: 56px !important;

  background:
    linear-gradient(
      135deg,
      #00ffd0,
      #00b8ff
    ) !important;

  border:
    1px solid rgba(0,255,200,0.22) !important;

  color:
    #ffffff !important;

  font-family: var(--font-sans);

  font-weight: 700;

  letter-spacing: 0.02em;

  padding: 0 26px;

  box-shadow:
    0 0 14px rgba(0,255,200,0.18);

  transition:
    transform 0.25s ease,
    box-shadow 0.25s ease,
    border-color 0.25s ease;
}

.search-btn:hover {
  transform: translateY(-2px);

  border-color:
    rgba(0,255,200,0.42) !important;

  box-shadow:
    0 0 18px rgba(0,255,200,0.28),
    0 0 38px rgba(0,153,255,0.12);
}

/* =========================================================
   BOTÓN AVANZADO
========================================================= */

.advanced-btn {
  height: 56px !important;

  border-radius: 16px !important;

  background:
    rgba(255,255,255,0.62) !important;

  border:
    1px solid rgba(255,255,255,0.42) !important;

  color:
    rgba(17,17,17,0.82) !important;

  backdrop-filter: blur(16px);

  font-family: var(--font-sans);

  font-weight: 600;

  padding: 0 22px;

  box-shadow:
    0 6px 18px rgba(0,0,0,0.04);

  transition:
    transform 0.25s ease,
    border-color 0.25s ease,
    box-shadow 0.25s ease,
    color 0.25s ease;
}

.advanced-btn:hover {
  transform: translateY(-2px);

  color:
    #00cdb2 !important;

  border-color:
    rgba(0,255,200,0.32) !important;

  box-shadow:
    0 0 10px rgba(0,255,200,0.12);
}

/* =========================================================
   RESPONSIVE
========================================================= */

@media (max-width: 768px) {

  .search-box {
    grid-template-columns: 1fr;
  }

  .search-btn,
  .advanced-btn {
    width: 100%;
  }

  .search-section::before {
    width: 260px;
    height: 260px;
  }
}

</style>