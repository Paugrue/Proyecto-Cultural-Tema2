<template>
  <v-app-bar
    flat
    class="nav-minimal px-8"
    height="72"
  >
    <!-- Botón hamburguesa 100% visible -->
    <v-btn
      icon
      class="d-md-none menu-trigger"
      @click="drawer = !drawer"
    >
      <!-- SVG  -->
      <svg 
        width="28" 
        height="28" 
        viewBox="0 0 24 24" 
        fill="none" 
        stroke="#111" 
        stroke-width="2.5" 
        stroke-linecap="round" 
        stroke-linejoin="round"
      >
        <line x1="3" y1="6" x2="21" y2="6" />
        <line x1="3" y1="12" x2="21" y2="12" />
        <line x1="3" y1="18" x2="21" y2="18" />
      </svg>
    </v-btn>

    <v-spacer />

    <div class="nav-center d-none d-md-flex">
      <router-link to="/inicio" class="menu-link">Inicio</router-link>
      <router-link to="/record" class="menu-link">Registros</router-link>
      <router-link to="/collection" class="menu-link">Colecciones</router-link>
      <router-link to="/pages" class="menu-link">Páginas</router-link>
      <router-link to="/favorites" class="menu-link">Favoritos</router-link>
    </div>

    <v-spacer />
  </v-app-bar>

  <v-navigation-drawer
    v-model="drawer"
    temporary
    location="left"
  >
    <v-list>
      <v-list-item
        v-for="link in links"
        :key="link.name"
        @click="drawer = false"
      >
        <router-link :to="link.path" class="menu-link">
          {{ link.name }}
        </router-link>
      </v-list-item>
    </v-list>
  </v-navigation-drawer>
</template>

<script>
export default {
  data() {
    return {
      drawer: false,
      links: [
        { name: 'Inicio', path: '/inicio' },
        { name: 'Registros', path: '/record' },
        { name: 'Colecciones', path: '/collection' },
        { name: 'Páginas', path: '/pages' },
        { name: 'Favoritos', path: '/favorites' },
      ]
    };
  }
}
</script>

<style scoped>
/* =========================================================
   BARRA DE NAVEGACIÓN – MINIMAL + NEON SUAVE
========================================================= */

.nav-minimal {
  background: #f5f5f7 !important;

  border-bottom:
    1px solid rgba(0, 255, 200, 0.08);

  backdrop-filter:
    blur(18px);

  box-shadow:
    0 0 18px rgba(0, 255, 200, 0.04),
    0 0 40px rgba(0, 255, 200, 0.02);
}

/* =========================================================
   BOTÓN MENÚ (MÓVIL)
========================================================= */

.menu-trigger {
  opacity: 1 !important;
  z-index: 1000 !important;

  color: #111111 !important;

  transition:
    color 0.25s ease,
    text-shadow 0.25s ease,
    transform 0.25s ease;
}

.menu-trigger:hover {
  color: var(--neon-cyan) !important;

  text-shadow:
    0 0 8px rgba(0,255,224,0.28);

  transform: translateY(-1px);
}

/* =========================================================
   CONTENEDOR CENTRAL
========================================================= */

.nav-center {
  display: flex;
  align-items: center;
  gap: 32px;
}

/* =========================================================
   LINKS
========================================================= */

.menu-link {
  font-family: "Inter", sans-serif;

  font-size: 15px;
  font-weight: 500;

  color: rgba(17,17,17,0.78) !important;

  text-decoration: none;

  position: relative;

  transition:
    color 0.25s ease,
    text-shadow 0.25s ease,
    opacity 0.25s ease;
}

/* =========================================================
   HOVER NEON SUTIL
========================================================= */

.menu-link:hover {
  color: var(--neon-cyan) !important;

  text-shadow:
    0 0 10px rgba(0,255,224,0.22);

  opacity: 1;
}

/* =========================================================
   SUBRAYADO NEON
========================================================= */

.menu-link::after {
  content: "";

  position: absolute;

  left: 0;
  bottom: -6px;

  width: 0%;
  height: 1px;

  border-radius: 999px;

  background:
    linear-gradient(
      90deg,
      transparent,
      var(--neon-cyan),
      transparent
    );

  box-shadow:
    0 0 8px rgba(0,255,224,0.22);

  transition:
    width 0.28s ease;
}

.menu-link:hover::after {
  width: 100%;
}
</style>