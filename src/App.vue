<template>
  <div id="app" class="container">
    <!-- Agregar un enlace de navegación -->

    <nav class="navbar is-fixed-top" role="navigation" aria-label="main navigation">
      <div style="padding: 5px">
        <router-link to="/ProductosList" class="button is-info mr-5">
          Mostrar Productos
        </router-link>

        <router-link to="/ProductosAgregados" class="button is-warning mr-5">
          Mostrar Pedido
        </router-link>

      </div>
    </nav>
    <!-- El contenido del componente se inyectará aquí -->
    <router-view style="margin-top: 30px"></router-view>
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

@media screen and (max-width: 768px),
print {
  .button.is-fullwidth-mobile {
    display: flex;
    width: 100%;
  }
}
</style>
