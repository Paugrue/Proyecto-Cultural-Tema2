<template>
  <div>
    <Hero />

    <section class="page-search-wrap">
      <div class="page-search-inner">
        <SearchBar
          :fields="fieldsList"
          :collections="collectionsList"
          :defaults="advancedDefaults"
          @do-basic-search="onBasicSearch"
          @do-advanced-search="onAdvancedSearch"
        />
      </div>
    </section>

    <!-- ✅ FIX GLOBAL LAYOUT -->
    <main class="page-container">
      <div class="page-content">
        <slot />
      </div>
    </main>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import Hero from '@/components/Hero.vue'
import SearchBar from '@/components/SearchBar.vue'

const props = defineProps({
  fields: { type: Array, default: () => [] },
  collections: { type: Array, default: () => [] }
})

const router = useRouter()
const route = useRoute()

const fieldsList = computed(() =>
  props.fields.length
    ? props.fields
    : [
        { title: 'Título', value: 'title' },
        { title: 'Autor', value: 'author' },
        { title: 'Fecha', value: 'date' },
        { title: 'Descripción', value: 'description' },
        { title: 'Colección', value: 'collections' }
      ]
)

const collectionsList = computed(() =>
  Array.isArray(props.collections)
    ? props.collections.map(c => ({
        id: c.id,
        title: c.title || c.name || 'Sin título'
      }))
    : []
)

const advancedDefaults = computed(() => {
  let rules = []

  try {
    if (route.query.rules) {
      rules = JSON.parse(route.query.rules)
    }
  } catch {
    rules = []
  }

  return {
    scope: route.query.scope || 'records',
    query: route.query.q || '',
    combine: route.query.combine || 'AND',
    rules,
    sortBy: route.query.sortBy || 'default',
    sortDir: route.query.sortDir || 'asc'
  }
})

/* =========================
   SEARCH SIMPLE
========================= */
const onBasicSearch = (query) => {
  router.push({
    path: '/record',
    query: {
      q: query || undefined,
      page: 1
    }
  })
}

/* =========================
   SEARCH AVANZADO
========================= */
const onAdvancedSearch = (payload) => {
  const { scope, query, combine, rules } = payload

  const isCollectionPage = route.path.startsWith('/collection')
  const targetPath = isCollectionPage ? '/collection' : '/record'

  const queryParams = {
    q: query?.trim() || undefined,
    combine,
    rules: rules?.length ? JSON.stringify(rules) : undefined,
    scope,
    page: 1
  }

  Object.keys(queryParams).forEach(key => {
    if (queryParams[key] == null) delete queryParams[key]
  })

  router.push({
    path: targetPath,
    query: queryParams
  })
}
</script>

<style scoped>

/* =========================
   GLOBAL LAYOUT FIX
========================= */

.page-container {
  width: 100%;

  position: relative;
}

/* Glow ambiental sutil */
.page-container::before {
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
      rgba(0,255,200,0.05),
      transparent 72%
    );

  pointer-events: none;

  z-index: 0;

  filter: blur(28px);
}

.page-content {
  position: relative;

  z-index: 1;

  max-width: 1200px;

  margin: 0 auto;

  padding-left: 24px;
  padding-right: 24px;

  padding-top: 16px;
  padding-bottom: 16px;
}

/* =========================
   SEARCH WRAPPER
========================= */

.page-search-wrap {
  width: 100%;

  position: relative;
}

.page-search-inner {
  width: 100%;
}

/* =========================
   SEARCH FIELD — MINIMAL
========================= */

.page-search-inner :deep(.v-field) {

  background:
    rgba(255,255,255,0.72) !important;

  backdrop-filter:
    blur(14px);

  border:
    1px solid rgba(255,255,255,0.42) !important;

  border-radius:
    18px !important;

  box-shadow:
    0 8px 24px rgba(0,0,0,0.04);

  transition:
    border-color 0.25s ease,
    box-shadow 0.25s ease,
    background 0.25s ease,
    transform 0.25s ease;
}

/* Hover/Focus neon elegante */
.page-search-inner :deep(.v-field:hover),
.page-search-inner :deep(.v-field--focused) {

  border-color:
    rgba(0,255,200,0.22) !important;

  background:
    rgba(255,255,255,0.82) !important;

  box-shadow:
    0 10px 28px rgba(0,0,0,0.06),
    0 0 18px rgba(0,255,200,0.10);

  transform:
    translateY(-1px);
}

/* =========================
   INPUT TEXT
========================= */

.page-search-inner :deep(input) {

  color:
    #111111 !important;

  font-size:
    0.96rem;

  font-weight:
    500;
}

/* Placeholder */
.page-search-inner :deep(input::placeholder) {

  color:
    rgba(17,17,17,0.42);
}

/* =========================
   RESPONSIVE
========================= */

@media (max-width: 768px) {

  .page-content {

    padding-left: 18px;
    padding-right: 18px;

    padding-top: 12px;
    padding-bottom: 12px;
  }

  .page-container::before {

    width: 280px;
    height: 280px;
  }
}

</style>