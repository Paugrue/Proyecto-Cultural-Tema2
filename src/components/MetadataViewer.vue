<template>
  <div v-if="processed && processed.length" class="metadata-container">

    <div
      v-for="(row, i) in processed"
      :key="i"
      class="metadata-row"
    >
      <!-- LABEL -->
      <div class="metadata-label">
        {{ row.label }}
      </div>

      <!-- VALUES -->
      <div class="metadata-value">
        <div
          v-for="(v, j) in row.values"
          :key="j"
          class="metadata-item"
        >

          <!-- ===================== -->
          <!-- TEXTO -->
          <!-- ===================== -->
          <span v-if="v.type === 'text'">
            {{ v.value }}
          </span>

          <!-- ===================== -->
          <!-- LINK -->
          <!-- ===================== -->
          <a
            v-else-if="v.type === 'link'"
            :href="v.value"
            target="_blank"
            class="metadata-link"
          >
            {{ v.label || v.value }}
          </a>

          <!-- ===================== -->
          <!-- RESOURCE -->
          <!-- ===================== -->
          <template v-else-if="v.type === 'resource' && v.value">

            <!-- RECORD -->
            <router-link
              v-if="v.value.type === 'record'"
              :to="`/record/${v.value.id}`"
              class="metadata-link"
            >
              {{ v.label || v.value.title }}
            </router-link>

            <!-- COLLECTION -->
            <router-link
              v-else-if="v.value.type === 'collection'"
              :to="`/collection/${v.value.id}`"
              class="metadata-link"
            >
              {{ v.label || v.value.title }}
            </router-link>

            <!-- MEDIA -->
            <span v-else-if="v.value.type === 'medio'">
              {{ v.label || v.value.title }}
            </span>

          </template>

          <!-- ===================== -->
          <!-- FALLBACK CONTROLADO -->
          <!-- ===================== -->
          <span v-else>
            {{ v.label || v.value || '—' }}
          </span>

        </div>
      </div>
    </div>

  </div>

  <div v-else class="text-grey text-body-2 py-4">
    Sin metadatos detallados
  </div>
</template>

<script setup>
import { computed } from "vue"
import { processMetadata } from "@/utils/metadataProcessor"

const props = defineProps({
  metadata: { type: Object, default: () => ({}) }
})

const processed = computed(() => processMetadata(props.metadata))

// -------- recursos --------
const getMime = (v) => v?.mime || v?.mimetype || ""

const isImage = (v) => getMime(v).startsWith("image")
const isVideo = (v) => getMime(v).startsWith("video")
const isAudio = (v) => getMime(v).startsWith("audio")
</script>

<style scoped>

.metadata-container {
  width: 100%;

  display: flex;
  flex-direction: column;

  gap: 0;
}

/* =========================
   FILA — EDITORIAL + NEON
========================= */

.metadata-row {
  display: grid;

  grid-template-columns: 180px 1fr;

  gap: 24px;

  padding: 20px 0;

  border-bottom:
    1px solid rgba(0,0,0,0.06);

  transition:
    border-color 0.25s ease,
    background 0.25s ease,
    box-shadow 0.25s ease;
}

.metadata-row:hover {

  border-color:
    rgba(0,255,208,0.16);

  background:
    linear-gradient(
      90deg,
      rgba(0,255,208,0.03),
      transparent
    );

  box-shadow:
    inset 0 0 12px rgba(0,255,208,0.03);
}

/* =========================
   LABEL
========================= */

.metadata-label {
  font-size: 0.72rem;

  font-weight: 600;

  letter-spacing: 0.08em;

  text-transform: uppercase;

  color:
    rgba(17,17,17,0.48);

  line-height: 1.4;

  transition:
    color 0.25s ease,
    text-shadow 0.25s ease;
}

.metadata-row:hover .metadata-label {

  color:
    #00cfc8;

  text-shadow:
    0 0 8px rgba(0,255,208,0.12);
}

/* =========================
   VALUE
========================= */

.metadata-value {
  display: flex;

  flex-direction: column;

  gap: 10px;

  min-width: 0;
}

.metadata-item {
  font-size: 1rem;

  line-height: 1.7;

  color:
    rgba(17,17,17,0.88);

  word-break: break-word;
}

/* =========================
   LINKS
========================= */

.metadata-link {
  color:
    #111111;

  text-decoration: none;

  border-bottom:
    1px solid rgba(0,0,0,0.12);

  transition:
    color 0.25s ease,
    border-color 0.25s ease,
    text-shadow 0.25s ease;
}

.metadata-link:hover {

  color:
    #00cfc8;

  border-color:
    rgba(0,255,208,0.32);

  text-shadow:
    0 0 10px rgba(0,255,208,0.14);
}

/* =========================
   EMPTY
========================= */

.text-grey {
  color:
    rgba(17,17,17,0.42);
}

/* =========================
   RESPONSIVE
========================= */

@media (max-width: 768px) {

  .metadata-row {
    grid-template-columns: 1fr;

    gap: 8px;

    padding: 16px 0;
  }

  .metadata-label {
    font-size: 0.68rem;
  }

  .metadata-item {
    font-size: 0.95rem;
  }
}

</style>