<script setup>
import { ref, computed, onMounted } from "vue";

const nombreInstitucion = ref("");
const nombreVendedor = ref("");
const nombreSector = ref("");
const productosAgregados = ref([]);

// Cargar los productos desde localStorage
onMounted(() => {
    const datosGuardados = JSON.parse(localStorage.getItem("productosAgregados"));
    if (datosGuardados) {
        productosAgregados.value = datosGuardados;
    }
});

// Computed para calcular el precio total basado en PrecioFarmacia
const precioTotal = computed(() => {
    return productosAgregados.value.reduce((total, producto) => {
        const cantidad = producto.cantidad || 0;
        const precioFarmacia = parseFloat(producto.PrecioFarmacia) || 0; // Convertir PrecioFarmacia a número
        return total + cantidad * precioFarmacia;
    }, 0).toFixed(2); // Redondear a 2 decimales
});

// Función para eliminar productos
const eliminarProducto = (productoID) => {
    const index = productosAgregados.value.findIndex(
        (producto) => producto.ID === productoID
    );
    if (index !== -1) {
        productosAgregados.value.splice(index, 1);
        localStorage.setItem(
            "productosAgregados",
            JSON.stringify(productosAgregados.value)
        );
    }
};
</script>

<template>
    <div>
        <h2>Productos Agregados</h2>

        <!-- Formulario de información -->
        <div style="display: flex; margin: 15px; gap: 10px; flex-wrap: wrap">
            <div>
                <label>Institución: </label><br />
                <input v-model="nombreInstitucion" class="input is-primary" style="width: 200px" />
            </div>
            <div>
                <label>Vendedor: </label><br />
                <input v-model="nombreVendedor" class="input is-primary" style="width: 200px" />
            </div>
            <div>
                <label>Sector: </label><br />
                <input v-model="nombreSector" class="input is-primary" style="width: 200px" />
            </div>
        </div>

        <!-- Botones de exportación -->
        <div style="display: flex; gap: 10px; flex-wrap: wrap">
            <button @click="exportarProformaAExcel" class="button is-warning is-light">
                Exportar como Proforma
            </button>
            <button @click="exportarPedidoAExcel" class="button is-success is-light">
                Exportar como Pedido
            </button>
        </div>

        <!-- Mostrar mensaje si no hay productos -->
        <div v-if="productosAgregados.length === 0">
            <p>No has agregado productos al carrito.</p>
        </div>

        <!-- Tabla de productos -->
        <table v-else ref="productosTable" class="table table-striped-columns is-fullwidth">
            <thead>
                <tr>
                    <th>Acción</th>
                    <th>Cantidad</th>
                    <th>Nombre</th>
                    <th>Principio Activo</th>
                    <th>Presentación</th>
                    <th>PVP Farmacia</th>
                    <th>Marca</th>
                    <th>Descuento</th>
                    <th>PVP</th>
                    <th>Promoción</th>
                    <th>Total Producto</th> <!-- Nueva columna -->
                </tr>
            </thead>
            <tbody>
                <tr v-for="producto in productosAgregados" :key="producto.ID">
                    <td>
                        <button @click="eliminarProducto(producto.ID)" class="button is-danger">
                            X
                        </button>
                    </td>
                    <td>{{ producto.cantidad }}</td>
                    <td>{{ producto.NombreProducto }}</td>
                    <td>{{ producto.PrincipioActivo }}</td>
                    <td>{{ producto.Presentacion }}</td>
                    <td>${{ producto.PrecioFarmacia.toFixed(2) }}</td>
                    <td>{{ producto.MARCA }}</td>
                    <td>{{ producto.Descuento }}</td>
                    <td>{{ producto.PVP || "N/A" }}</td>
                    <td>{{ producto.Promocion || "N/A" }}</td>
                    <td>
                        <!-- Cálculo del total por producto -->
                        ${{ (producto.cantidad * producto.PrecioFarmacia).toFixed(2) }}
                    </td>
                </tr>
                <tr>
                    <td colspan="10" style="text-align: right; font-weight: bold;">Precio Total:</td>
                    <td style="font-weight: bold;">${{ precioTotal }}</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<style scoped>
input {
    margin: 5px;
    padding: 5px;
    width: 200px;
}

table {
    margin-top: 20px;
}

th,
td {
    font-size: 12px;
    text-align: center;
}
</style>
