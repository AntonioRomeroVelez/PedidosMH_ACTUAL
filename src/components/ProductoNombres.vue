<template>
  <div v-if="producto" class="container">
    <!-- Mostrar los detalles del producto -->
    <strong>Nombre: </strong>
    <b class="has-text-link">{{ producto.NombreProducto }} </b> <br />
    <strong>Presentación: </strong> {{ producto.Presentacion }} &emsp;
    <strong>Principio activo: </strong> {{ producto.PrincipioActivo }} <br />
    <strong>Promoción: </strong>
    <span v-if="producto.Promocion" class="text-primary">
      {{ producto.Promocion }}</span
    >
    <span v-else class="text-warning"> No tiene promoción</span> &emsp;

    <strong>P. Farmacia: </strong> ${{ producto.PrecioFarmacia }} &emsp;

    <strong>PVP: </strong> ${{ producto.PVP }} &emsp;
    <strong>Descuento: </strong>
    {{ producto.Descuento }} &emsp; <strong>Marca: </strong>
    {{ producto.MARCA }} &emsp;

    <strong>IVA: </strong>&nbsp;
    <span v-if="producto.IVA == 'SI'">
      <b> &emsp;{{ producto.IVA }}</b> ${{ precioConIVA }}
    </span>
    <span v-else>
      {{ producto.IVA }}
    </span>

    <div
      style="display: flex; gap: 5px; justify-content: center; margin: 10px 3px"
    >
      <strong>Cantidad:</strong>
      <input v-model="cantidad" type="number" min="1" class="input is-info" />

      <!-- Botón para agregar al carrito -->
      <div class="text-center">
        <button @click="agregarCarrito" class="button is-link">
          Agregar al Carrito
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
// import ProductosAgregados from "./ProductosAgregados";

const cantidad = ref();

const emit = defineEmits(["agregarProductoHijo"]);

const agregarCarrito = () => {
  if (cantidad.value > 0) {
    // Validamos que la cantidad sea un número positivo
    emit("agregarProductoHijo", cantidad.value, props.producto);
    cantidad.value = ""; // Limpiar el campo después de agregar
  } else {
    alert("Por favor, ingresa una cantidad válida.");
  }
};

const props = defineProps({
  producto: {
    type: Object,
    required: true, // Aseguramos que 'producto' sea obligatorio
  },
  cantidad: {
    type: Number,
    default: 1, // Valor predeterminado para la cantidad
  },
});

// Calcular el precio con IVA (suponiendo que IVA sea "SI" y el porcentaje sea 15%)
const precioConIVA = computed(() => {
  if (props.producto.IVA === "SI") {
    return (props.producto.PrecioFarmacia * 1.15).toFixed(2); // Agregar 15% de IVA
  }
  return props.producto.PrecioFarmacia.toFixed(2); // Si no tiene IVA, mostrar el precio normal
});
</script>

<style scoped>
/* Estilos opcionales para el componente hijo */

.container {
  border: solid 1px silver;
  display: inline-table;
  width: 100%;
  margin: 10px;
  padding: 5px;
  border-radius: 5px;
}

.cantidad {
  border: solid 1px silver;
  width: 80px;
  border-radius: 5px;
  text-align: center;
  outline: none;
}
</style>
