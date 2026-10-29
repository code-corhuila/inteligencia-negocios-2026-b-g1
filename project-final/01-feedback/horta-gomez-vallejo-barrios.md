# Retroalimentación · Fase 0 (Definición)

**Proyecto:** Andes Store
**Estudiantes:** Juan José Horta Vanegas, Andrés Felipe Gómez Anacona, Laura Sofía Vallejo Oviedo y María Paula Barrios Fajardo
**Revisión:** jueves 29 de octubre de 2026 · Docente Jesús Ariel González Bonilla
**Avance revisado:** commit `02ffe4f` («Primer avance de proyecto de aula»)

Es el avance más completo de la Fase 0: la ficha está en la ruta correcta, los datos ya están en el repositorio y el archivo .pbix ya tiene las consultas de Power Query iniciadas. Los puntos siguientes son para ajustar antes de la Fase 1 (jueves 5 de noviembre).

## 1. Ajustes a la ficha

1. **Número de integrantes.** La guía permite máximo dos integrantes por proyecto y la ficha declara cuatro. Este punto se define con el docente; mientras tanto, no cambien la ficha.
2. **Doce meses de datos.** La guía exige 12 meses. `ventas.csv` va del 31/01/2024 al 30/12/2024, pero enero tiene solo 5 ventas (todas del 31 de enero): en la práctica son 11 meses, de febrero a diciembre. Completen enero o declaren el periodo real y cómo cumplen el mínimo.
3. **KPI principal.** Las ventas totales describen, pero no explican. La pregunta habla de las *variaciones mensuales*, así que el KPI principal debería ser la **variación mensual de las ventas** = (ventas del mes − ventas del mes anterior) / ventas del mes anterior × 100, desglosada por categoría y método de pago. Ventas totales, ticket promedio y unidades vendidas quedan como KPI de apoyo.
4. **Contexto de los datos.** El dataset de Kaggle es de Argentina: las regiones son Patagonia, Cuyo, NOA y NEA, y los productos incluyen medialunas, manteca y galletitas. Ambienten Andes Store en ese contexto, o adapten los datos, y declaren la moneda: los precios (12,24; 5,21) no son pesos colombianos.
5. **Citar la fuente.** Escriban en la ficha el nombre del dataset, el autor y el enlace de Kaggle, en formato APA.

## 2. Datos personales en un repositorio público

`clientes.csv` trae nombre, apellido y correo de cada cliente, y la guía dice «sin datos personales» en GitHub. Aunque los datos parezcan generados, quiten esas tres columnas (en el CSV y en el .pbix) y dejen solo el código del cliente, la fecha de registro y la región.

## 3. Lo que ya se ve en los datos (insumo para la Fase 1)

Al revisar `ventas.csv` aparecen varios problemas de calidad. Son buen material para la evidencia del antes y el después que pide la Fase 1:

| Problema | Detalle |
|---|---|
| Filas duplicadas | 31 filas repetidas (3.031 en bruto, 3.000 sin duplicados) |
| Formatos de fecha mezclados | `31/01/2024`, `31/1/2024` y `2024/01/31` en la misma columna |
| Fechas que no se convierten | 4 fechas con formato año/mes/día |
| Estado de la venta | 2.550 «Completa», 471 «Pendiente» y 10 «Cancelada»: decidan y justifiquen si las pendientes y canceladas cuentan como venta |
| Regiones escritas de varias formas | «Buenos Aires» y «buenos aires»; «patagonia» en minúscula; aparece «bogota», que no corresponde al contexto |

## 4. Revisión del archivo .pbix

El archivo `Datos.pbix` ya carga las cinco tablas y quita duplicados en ventas y clientes. Para la Fase 1:

- **Fechas.** La consulta de ventas cambia el tipo de `Fecha` dos veces: primero sin configuración regional y luego con «es-CO». La primera conversión es la que falla con los formatos mezclados. Dejen un solo paso con configuración regional y traten aparte las fechas año/mes/día.
- **Relaciones.** Falta la relación de `ventas[Método_Pago]` con `metodos_pago[ID_Metodo]`, que es la que necesita la pregunta de negocio. Además, `productos` se relaciona con `categorias` por el nombre de la categoría (texto), no por el código: usen `ID_Categoria`.
- **Tabla calendario.** El modelo usa las tablas de fecha automáticas de Power BI (LocalDateTable). Desactiven «Fecha y hora automáticas» y creen una tabla calendario propia, marcada como tabla de fechas.
- **Ruta del origen.** Las consultas leen de `C:\Users\Usuario\Downloads\...`. Usen un parámetro de ruta para que el archivo abra en otro equipo.
- **Medidas y reporte.** Todavía no hay medidas DAX ni visuales; eso corresponde a la Fase 2.

## 5. Formato de datos propuesto (solo la forma)

```
                 Dim_Calendario
                       |
Dim_Cliente ---- Fact_Venta ---- Dim_Metodo_Pago
                       |
                 Dim_Producto ---- (categoría como columna)
```

### Fact_Venta (grano: una fila por venta)

| Columna | Tipo | Descripción |
|---|---|---|
| id_venta | Entero | Código único de la venta |
| fecha | Fecha | Día de la venta (se relaciona con Dim_Calendario) |
| id_cliente | Entero | Código del cliente |
| id_producto | Entero | Producto vendido |
| id_metodo | Entero | Método de pago |
| cantidad | Entero | Unidades |
| precio_unitario | Decimal | Precio tomado de productos al momento de la venta |
| valor_venta | Decimal | cantidad × precio_unitario |
| estado | Texto | Completa, Pendiente, Cancelada |

### Dim_Producto (productos y categorías en una sola dimensión)

| Columna | Tipo | Descripción |
|---|---|---|
| id_producto | Entero | Código |
| producto | Texto | Nombre |
| categoria | Texto | Una de las 8 categorías |
| precio_unitario | Decimal | Precio de lista |

### Dim_Cliente (sin datos personales)

| Columna | Tipo | Descripción |
|---|---|---|
| id_cliente | Entero | Código |
| fecha_registro | Fecha | Fecha de registro |
| region | Texto | Región normalizada |
| antiguedad | Texto | Nuevo o Antiguo según la fecha de registro |

### Dim_Metodo_Pago y Dim_Calendario

| Tabla | Columnas |
|---|---|
| Dim_Metodo_Pago | id_metodo, metodo |
| Dim_Calendario | fecha, anio, trimestre, mes, nombre_mes, dia_semana |

## 6. Para la Fase 1 (jueves 5 de noviembre)

- Actualizar la ficha con el periodo real, el KPI ajustado, el contexto y la moneda, y la cita del dataset.
- Quitar las columnas personales de `clientes.csv`.
- Documentar cada paso aplicado en Power Query con su motivo, mostrar el antes y el después de al menos tres problemas de calidad (la tabla de la sección 3 sirve de punto de partida) y escribir el diccionario de datos.
