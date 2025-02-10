<template>
  <div id="app" class="container is-fullwidth">
    <!-- Agregar un enlace de navegación -->

    <nav
      class="navbar is-fixed-top"
      role="navigation"
      aria-label="main navigation"
    >
      <div class="container">
        <router-link to="/ProductosList" class="button is-info mr-5">
          Mostrar Productos
        </router-link>

        <router-link to="/ProductosAgregados" class="button is-warning mr-5">
          Mostrar Pedido
        </router-link>
      </div>
    </nav>
    <section class="section">
      <div class="container">
        <!-- El contenido del componente se inyectará aquí -->
        <router-view></router-view>
      </div>
    </section>
  </div>
</template>

<script setup>
// No necesitas agregar nada aquí para Vue Router
</script>

<style>
/* Estilos opcionales */
nav {
  margin-bottom: 20px;
}

router-link {
  margin-right: 10px;
  text-decoration: none;
  color: #42b983;
}

router-link:hover {
  text-decoration: underline;
}
</style>
