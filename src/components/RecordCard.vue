<template>
  <v-card
    class="record-card hoverable"
    @click="$router.push('/record/' + normalized.id)"
  >
    <v-img
      :src="normalized.thumbnail"
      height="180"
      cover
      class="rounded-t-lg"
    />

    <v-card-title class="record-title">
      {{ normalized.title }}
    </v-card-title>
  </v-card>
</template>

<script>
const API_BASE = 'https://arcadium.cluster24.libnamic.eu'

export default {
  props: { record: Object },

  computed: {
    normalized() {
      const r = this.record || {}

      const title =
        r.title ||
        r.metadata_fields?.["dcterms:title"]?.[0]?.["@value"] ||
        "Sin título"

      let thumbnail = r.thumbnail
      if (!thumbnail) thumbnail = '/placeholder.png'
      else if (!/^https?:\/\//.test(thumbnail)) {
        thumbnail = `${API_BASE}${thumbnail.startsWith('/') ? '' : '/'}${thumbnail}`
      }

      return {
        id: r.id,
        title,
        thumbnail
      }
    }
  }
}
</script>

<style scoped>

.record-card {
  position: relative;

  cursor: pointer;

  overflow: hidden;

  border-radius: 18px;

  background:
    rgba(255,255,255,0.74);

  backdrop-filter:
    blur(16px);

  border:
    1px solid rgba(255,255,255,0.42);

  box-shadow:
    0 8px 24px rgba(0,0,0,0.05);

  transition:
    transform 0.25s ease,
    box-shadow 0.25s ease,
    border-color 0.25s ease,
    background 0.25s ease;
}

/* glow ambiental sutil */
.record-card::before {
  content: "";

  position: absolute;

  inset: 0;

  border-radius: inherit;

  background:
    radial-gradient(
      circle at top right,
      rgba(0,255,200,0.08),
      transparent 55%
    );

  opacity: 0;

  transition:
    opacity 0.3s ease;

  pointer-events: none;
}

.record-card:hover {

  transform:
    translateY(-4px);

  border-color:
    rgba(0,255,200,0.22);

  background:
    rgba(255,255,255,0.82);

  box-shadow:
    0 12px 28px rgba(0,0,0,0.08),
    0 0 18px rgba(0,255,200,0.10);
}

.record-card:hover::before {
  opacity: 1;
}

.record-title {
  position: relative;

  z-index: 1;

  font-size: 0.95rem;

  font-weight: 700;

  letter-spacing: -0.01em;

  color:
    #111111;

  transition:
    color 0.25s ease,
    text-shadow 0.25s ease;
}

.record-card:hover .record-title {

  color:
    #00cfc8;

  text-shadow:
    0 0 10px rgba(0,255,200,0.10);
}

</style>