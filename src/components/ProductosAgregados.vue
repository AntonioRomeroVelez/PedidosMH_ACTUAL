<template>
  <div>
    <h2>Productos Agregados</h2>
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
    <div style="display: flex; gap: 10px; flex-wrap: wrap">
      <!-- Botones para exportar en diferentes formatos -->
      <!-- <button @click="exportarAExcel" class="button is-info is-light">
        Exportar a Excel
      </button> -->
      <button @click="exportarProformaAExcel" class="button is-warning is-light">
        Exportar como Proforma
      </button>
      <button @click="exportarPedidoAExcel" class="button is-success is-light">
        Exportar como Pedido
      </button>
    </div>

    <!-- Mostrar mensaje si no hay productos en el carrito -->
    <div v-if="productosAgregados.length === 0">
      <p>No has agregado productos al carrito.</p>
    </div>

    <!-- Mostrar la tabla con los productos agregados -->
    <table v-else ref="productosTable" class="table table-striped-columns is-fullwidth">
      <thead>
        <tr>
          <th style="font-size: 10px">Acción</th>
          <th style="font-size: 10px">Cantidad</th>
          <th style="font-size: 10px">Nombre</th>
          <th style="font-size: 10px">
            Principio <br />
            Activo
          </th>
          <th style="font-size: 10px">Presentación</th>
          <th style="font-size: 10px">PVP <br />Farmacia</th>
          <th style="font-size: 10px">MARCA</th>
          <th style="font-size: 10px">Descuento</th>
          <th style="font-size: 10px">PVP</th>
          <th style="font-size: 10px">Promoción</th>
          <th>Total Producto</th> <!-- Nueva columna -->
        </tr>
      </thead>
      <tbody>
        <!-- Iteración a través de los productosAgregados -->
        <tr v-for="producto in productosAgregados" :key="producto.ID">
          <td>
            <button @click="eliminarProducto(producto.ID)" class="button is-danger">
              X
            </button>
          </td>
          <td style="font-size: 12px">{{ producto.cantidad }}</td>
          <td style="font-size: 12px">{{ producto.NombreProducto }}</td>
          <td style="font-size: 12px">{{ producto.PrincipioActivo }}</td>
          <td style="font-size: 12px">{{ producto.Presentacion }}</td>
          <td style="font-size: 12px">
            ${{ producto.PrecioFarmacia.toFixed(2) }}
          </td>
          <td style="font-size: 12px">{{ producto.MARCA }}</td>
          <td style="font-size: 12px">{{ producto.Descuento }}</td>
          <td style="font-size: 12px">{{ producto.PVP || "N/A" }}</td>
          <td style="font-size: 12px">{{ producto.Promocion || "N/A" }}</td>
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

<script setup>
// Importar las librerías necesarias
import { ref, onMounted, computed } from "vue";
import ExcelJS from "exceljs";

const nombreInstitucion = ref("");
const nombreVendedor = ref("");
const nombreSector = ref("");

// Declarar las referencias
const productosAgregados = ref([]);

// Cargar los productos desde localStorage
onMounted(() => {
  const datosGuardados = JSON.parse(localStorage.getItem("productosAgregados"));
  if (datosGuardados) {
    productosAgregados.value = datosGuardados;
  }
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

// Función para exportar como Excel
const exportarAExcel = async () => {
  try {
    const workbook = new ExcelJS.Workbook();
    const worksheet = workbook.addWorksheet("Productos");

    // Definir los encabezados de la tabla
    worksheet.columns = [
      { header: "Acción", key: "accion" },
      { header: "Cantidad", key: "cantidad" },
      { header: "Nombre", key: "nombre" },
      { header: "Principio Activo", key: "principioActivo" },
      { header: "Presentación", key: "presentacion" },
      { header: "Precio Farmacia", key: "precioFarmacia" },
      { header: "PVP", key: "pvp" },
      { header: "Promoción", key: "promocion" },
    ];

    // Llenar los datos de la tabla
    productosAgregados.value.forEach((producto) => {
      worksheet.addRow({
        accion: "Eliminar",
        cantidad: producto.cantidad,
        nombre: producto.NombreProducto,
        principioActivo: producto.PrincipioActivo,
        presentacion: producto.Presentacion,
        precioFarmacia: producto.PrecioFarmacia.toFixed(2),
        pvp: producto.PVP || "",
        promocion: producto.Promocion || "",
      });
    });

    // Ajustar el tamaño de las celdas
    worksheet.getColumn(1).width = 15;
    worksheet.getColumn(2).width = 10;
    worksheet.getColumn(3).width = 30;
    worksheet.getColumn(4).width = 20;
    worksheet.getColumn(5).width = 20;
    worksheet.getColumn(6).width = 20;
    worksheet.getColumn(7).width = 10;
    worksheet.getColumn(8).width = 20;
    worksheet.getColumn(9).width = 20;
    worksheet.getColumn(10).width = 20;

    // Establecer estilo (alineación y ajuste de texto)
    worksheet.eachRow({ includeEmpty: true }, (row) => {
      row.alignment = {
        vertical: "middle",
        horizontal: "center",
        wrapText: true,
      };
    });

    // Ajustar el contenido de las celdas para que se adapte a la celda y habilitar el ajuste de texto
    worksheet.eachRow((row, rowNumber) => {
      row.eachCell((cell, colNumber) => {
        // Alineación del texto
        if (colNumber === 1) {
          cell.alignment = {
            vertical: "middle",
            horizontal: "center",
            wrapText: true,
          }; // Para el primer campo
        } else {
          cell.alignment = {
            vertical: "middle",
            horizontal: "left",
            wrapText: true,
          }; // Para el resto de las celdas
        }

        // Establecer bordes para las celdas
        cell.border = {
          top: { style: "thin" },
          left: { style: "thin" },
          bottom: { style: "thin" },
          right: { style: "thin" },
        };
      });
    });

    // Generar archivo Excel
    const buffer = await workbook.xlsx.writeBuffer();
    const blob = new Blob([buffer], { type: "application/octet-stream" });
    const link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = "productos.xlsx";
    link.click();

    localStorage.removeItem("productosAgregados"); /// BORRAR EL LOCAL STORAGE
  } catch (error) {
    console.error("Error al exportar a Excel:", error);
  }
};

// Función para exportar como Proforma

const exportarProformaAExcel = async () => {
  try {
    const workbook = new ExcelJS.Workbook();
    const worksheet = workbook.addWorksheet("Proforma");

    // // Agregar filas con la información de institución, vendedor y sector
    // worksheet.addRow(["Institución:", nombreInstitucion.value]);
    // worksheet.addRow(["Vendedor:", nombreVendedor.value]);
    // worksheet.addRow(["Sector:", nombreSector.value]);
    // worksheet.addRow([]); // Fila vacía para separar la información de la tabla


    // Agregar filas con la información de institución, vendedor y sector
    const institucionRow = worksheet.addRow([`Institución:  ${nombreInstitucion.value}`]);
    const vendedorRow = worksheet.addRow([`Vendedor:  ${nombreVendedor.value}`]);
    const sectorRow = worksheet.addRow([`Sector:  ${nombreSector.value}`]);
    worksheet.addRow([]); // Fila vacía para separar la información de la tabla

    // Unir celdas para cada fila de información (Columnas A y B)
    worksheet.mergeCells(`A${institucionRow.number}: B${institucionRow.number}`);
    worksheet.mergeCells(`A${vendedorRow.number}: B${vendedorRow.number}`);
    worksheet.mergeCells(`A${sectorRow.number}: B${sectorRow.number}`);

    // Aplicar estilos opcionales
    [institucionRow, vendedorRow, sectorRow].forEach(row => {
      row.eachCell((cell) => {
        cell.font = { bold: true };
        cell.alignment = { vertical: "middle", horizontal: "left" };
      });
    });


    // Agregar encabezados en la fila 5
    const headerRow = worksheet.addRow([
      "Cantidad",
      "Nombre",
      "Precio Farmacia",
      "Promoción",
      "MARCA",
      "Presentación",
      "Precio total",
    ]);

    // Aplicar estilos a los encabezados
    headerRow.eachCell((cell) => {
      cell.font = { bold: true, color: { argb: "FFFFFF" } }; // Texto blanco
      cell.alignment = {
        vertical: "middle",
        horizontal: "center",
        wrapText: true,
      };
      cell.fill = {
        type: "pattern",
        pattern: "solid",
        fgColor: { argb: "4F81BD" }, // Azul
      };
      cell.border = {
        top: { style: "thin" },
        left: { style: "thin" },
        bottom: { style: "thin" },
        right: { style: "thin" },
      };
    });

    // Ajustar el ancho de las columnas
    worksheet.columns = [
      { key: "cantidad", width: 10 },
      { key: "nombre", width: 30 },
      { key: "precioFarmacia", width: 15 },
      { key: "promocion", width: 20 },
      { key: "MARCA", width: 20 },
      { key: "presentacion", width: 20 },
      { key: "totalPrecioProducto", width: 20 },
    ];

    // Llenar los datos a partir de la fila 6
    productosAgregados.value.forEach((producto) => {
      const row = worksheet.addRow({
        cantidad: producto.cantidad,
        nombre: producto.NombreProducto,
        precioFarmacia: "$ " + producto.PrecioFarmacia.toFixed(2),
        promocion: producto.Promocion || "",
        MARCA: producto.MARCA || "___",
        presentacion: producto.Presentacion,
        totalPrecioProducto: "$ " + (producto.cantidad * producto.PrecioFarmacia).toFixed(2),
      });

      // Ajustar la altura de la fila dinámicamente
      row.height = undefined;

      // Aplicar bordes y alineación con wrap text
      row.eachCell((cell) => {
        cell.alignment = {
          vertical: "middle",
          horizontal: "left",
          wrapText: true,
        };
        cell.border = {
          top: { style: "thin" },
          left: { style: "thin" },
          bottom: { style: "thin" },
          right: { style: "thin" },
        };
      });
    });


    // Agregar una fila al final con el precio total
    const totalRow = worksheet.addRow([
      "", "", "", "", "", "Total", "$ " + precioTotal.value
    ]);

    // Aplicar formato al total
    totalRow.eachCell((cell, colIndex) => {
      if (colIndex === 7) { // La columna del precio total
        cell.font = { bold: true, color: { argb: "FFFFFF" } };
        cell.alignment = { vertical: "middle", horizontal: "center" };
        cell.fill = {
          type: "pattern",
          pattern: "solid",
          fgColor: { argb: "4F81BD" }, // Azul
        };
        cell.border = {
          top: { style: "thin" },
          left: { style: "thin" },
          bottom: { style: "thin" },
          right: { style: "thin" },
        };
      }
    });




    // Generar archivo Excel
    const buffer = await workbook.xlsx.writeBuffer();
    const blob = new Blob([buffer], { type: "application/octet-stream" });
    const link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = `Proforma_${nombreInstitucion.value}.xlsx`;
    link.click();
    localStorage.removeItem("productosAgregados"); /// BORRAR EL LOCAL STORAGE
  } catch (error) {
    console.error("Error al exportar como Proforma:", error);
  }
};


// Computed para calcular el precio total basado en PrecioFarmacia
const precioTotal = computed(() => {
  return productosAgregados.value.reduce((total, producto) => {
    const cantidad = producto.cantidad || 0;
    const precioFarmacia = parseFloat(producto.PrecioFarmacia) || 0; // Convertir PrecioFarmacia a número
    return total + cantidad * precioFarmacia;
  }, 0).toFixed(2); // Redondear a 2 decimales
});

const exportarPedidoAExcel = async () => {
  try {
    const workbook = new ExcelJS.Workbook();
    const worksheet = workbook.addWorksheet("Pedido");

    // Agregar filas con la información de institución, vendedor y sector
    const institucionRow = worksheet.addRow([`Institución: ${nombreInstitucion.value}`]);
    const vendedorRow = worksheet.addRow([`Vendedor: ${nombreVendedor.value}`]);
    const sectorRow = worksheet.addRow([`Sector: ${nombreSector.value}`]);
    worksheet.addRow([]); // Fila vacía para separar la información de la tabla

    // Unir celdas para cada fila de información (Columnas A y B)
    worksheet.mergeCells(`A${institucionRow.number}: B${institucionRow.number}`);
    worksheet.mergeCells(`A${vendedorRow.number}: B${vendedorRow.number}`);
    worksheet.mergeCells(`A${sectorRow.number}: B${sectorRow.number}`);

    // Aplicar estilos opcionales
    [institucionRow, vendedorRow, sectorRow].forEach(row => {
      row.eachCell((cell) => {
        cell.font = { bold: true };
        cell.alignment = { vertical: "middle", horizontal: "left" };
      });
    });

    // Definir los encabezados en la fila 5
    const headerRow = worksheet.addRow([
      "Cantidad",
      "Nombre",
      "Precio Farmacia",
      "Promoción",
      "MARCA",
      "Presentación",
      "Lote",
      "Fecha de vencimiento",
    ]);

    // Aplicar estilos a los encabezados
    headerRow.eachCell((cell) => {
      cell.font = { bold: true, color: { argb: "000000" } }; // Texto negro
      cell.alignment = {
        vertical: "middle",
        horizontal: "center",
        wrapText: true,
      };
      cell.fill = {
        type: "pattern",
        pattern: "solid",
        fgColor: { argb: "FFFFE699" }, // Color amarillo claro
      };
      cell.border = {
        top: { style: "thin" },
        left: { style: "thin" },
        bottom: { style: "thin" },
        right: { style: "thin" },
      };
    });

    // Ajustar el ancho de las columnas
    worksheet.columns = [
      { key: "cantidad", width: 10 },
      { key: "nombre", width: 30 },
      { key: "precioFarmacia", width: 15 },
      { key: "promocion", width: 20 },
      { key: "MARCA", width: 20 },
      { key: "presentacion", width: 20 },
      { key: "lote", width: 20 },
      { key: "fecha_de_vencimiento", width: 20 },
    ];

    // Llenar los datos a partir de la fila 6
    productosAgregados.value.forEach((producto) => {
      const row = worksheet.addRow({
        cantidad: producto.cantidad,
        nombre: producto.NombreProducto,
        precioFarmacia: "$ " + producto.PrecioFarmacia.toFixed(2),
        promocion: producto.Promocion || "",
        MARCA: producto.MARCA || "___",
        presentacion: producto.Presentacion,
        lote: "", // Campo vacío pero con borde
        fecha_de_vencimiento: "", // Campo vacío pero con borde
      });

      // Permitir que Excel ajuste automáticamente la altura de la fila
      row.height = undefined;

      // Aplicar estilos a cada celda de la fila
      row.eachCell((cell) => {
        cell.alignment = {
          vertical: "middle",
          horizontal: "left",
          wrapText: true,
        };
        cell.border = {
          top: { style: "thin" },
          left: { style: "thin" },
          bottom: { style: "thin" },
          right: { style: "thin" },
        };
      });
    });



    // Generar archivo Excel
    const buffer = await workbook.xlsx.writeBuffer();
    const blob = new Blob([buffer], { type: "application/octet-stream" });
    const link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = `Pedido_${nombreInstitucion.value}.xlsx`;
    link.click();
    localStorage.removeItem("productosAgregados"); /// BORRAR EL LOCAL STORAGE
  } catch (error) {
    console.error("Error al exportar el pedido:", error);
  }
};
</script>
