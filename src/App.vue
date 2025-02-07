<template>
  <div id="app">
    <!-- Agregar un enlace de navegación -->
    <nav
      class="navbar navbar-expand-lg navbar-light bg-light mr-auto"
      style="display: flex; gap: 10px"
    >
      <router-link to="/IndexProductos" class="btn btn-primary my-2 my-sm-0"
        >Inicio</router-link
      >

      <router-link to="/ProductosList" class="btn btn-success my-2 my-sm-0"
        >Mostrar Productos</router-link
      >

      <router-link to="/ProductosAgregados" class="btn btn-warning my-2 my-sm-0"
        >Productos Agregados</router-link
      >
    </nav>

    <!-- El contenido del componente se inyectará aquí -->
    <router-view></router-view>
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
