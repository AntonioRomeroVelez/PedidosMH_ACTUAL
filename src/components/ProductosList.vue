<template>
  <div class="contiainer is-fullwidth">
    <h1>Lista de Productos</h1>

    <!-- Input para buscar productos por nombre -->
    <div>
      <input
        v-model="searchTerm"
        type="text"
        placeholder="Buscar producto..."
        class="input is-primary"
      />
    </div>

    <!-- Verificar si hay productos disponibles para mostrar -->
    <div v-if="filteredProductos.length === 0">
      <p>No se encontraron productos.</p>
    </div>

    <!-- Mostrar productos con el componente hijo ProductoNombres -->
    <ProductoNombres
      v-for="producto in filteredProductos"
      :key="producto.ID"
      :producto="producto"
      @agregarProductoHijo="agregarProductoPadre"
    />
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import ProductoNombres from "./ProductoNombres.vue";
import { useRouter } from "vue-router"; // Para navegación

const productos = ref([]);
const productosAgregados = ref([]); // Aquí es donde guardamos los productos agregados
const searchTerm = ref(""); // Término de búsqueda

// Cargar productos desde el archivo JSON
onMounted(async () => {
  try {
    const response = await fetch("/listaGeneral.json"); // Quita "/public/"
    if (!response.ok) throw new Error("Error al cargar los productos");

    productos.value = await response.json();

    // Recuperar productos del LocalStorage
    const datosGuardados =
      JSON.parse(localStorage.getItem("productosAgregados")) || [];
    productosAgregados.value = datosGuardados; // Evita null
  } catch (error) {
    console.error("Hubo un problema al cargar el JSON:", error);
  }
});

// Definir una propiedad para guardar los datos de producto y cantidad
const productoEnCarrito = ref(null);
const cantidadEnCarrito = ref(0);

const agregarProductoPadre = (cantidad, producto) => {
  // Buscar si el producto ya está agregado en el carrito
  const index = productosAgregados.value.findIndex((p) => p.ID === producto.ID);

  if (index !== -1) {
    // Si el producto ya existe, solo actualizar la cantidad
    productosAgregados.value[index].cantidad = cantidad;
    console.log(
      `Producto ya agregado. Nueva cantidad del producto: ${producto.NombreProducto} es ${productosAgregados.value[index].cantidad}`
    );
  } else {
    // Si el producto no está, agregarlo con la cantidad inicial
    productosAgregados.value.push({ ...producto, cantidad });
    console.log(
      `Producto agregado: ${producto.NombreProducto}, cantidad de ${cantidad} unidades.`
    );
  }

  // Mostrar el carrito actualizado en la consola
  console.log(productosAgregados.value);

  // Guardar los productos agregados en el localStorage
  localStorage.setItem(
    "productosAgregados",
    JSON.stringify(productosAgregados.value)
  );
};

// Filtrar productos
const filteredProductos = computed(() => {
  if (!searchTerm.value) {
    return productos.value;
  }
  return productos.value.filter((producto) =>
    producto.NombreProducto.toLowerCase().includes(
      searchTerm.value.toLowerCase()
    )
  );
});
</script>

<style scoped>
.search-input {
  padding: 10px;
  margin-bottom: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  width: 300px;
}

.btn-toggle {
  margin-top: 20px;
  padding: 10px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  display: inline-block;
}

.btn-toggle:hover {
  background-color: #369d67;
}
</style>
