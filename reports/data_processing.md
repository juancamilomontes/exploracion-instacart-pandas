# Informe de procesamiento de los datos de Instacart

**Aprendiz:** Juan Camilo Montes Velásquez
**Evidencia:** GA1-220501094-01-AA1-EV06 – Limpieza, análisis y estructuración de datos con Pandas (Instacart)
**Notebook:** [`notebooks/pandas_labs.ipynb`](../notebooks/pandas_labs.ipynb)

**Pregunta orientadora:** ¿Cómo preparar y estructurar los datos de Instacart para obtener información confiable sobre los hábitos de compra?

El punto de partida son los problemas del diagnóstico de la actividad anterior ([`data_quality.md`](data_quality.md)).

## 1. Limpieza y estandarización (Lección 2)

| Problema | Decisión | Resultado |
|---|---|---|
| 15 pedidos duplicados (todos miércoles a las 2 a. m., fallo del sistema) | Eliminar con `drop_duplicates` | `orders` pasa de 478.967 a 478.952 filas y `order_id` queda único |
| Columnas enteras como `float64` | Convertir con `astype` a `int8`/`int16`/`int32` y `Int16` (nullable) | `orders`: 23,0 → 7,2 MB; `order_prods`: 145,4 → 54,5 MB |
| Nulos en `days_since_prior_order` (28.817, primeros pedidos) | Conservar | Rellenar con 0 inventaría una compra el mismo día |
| Nulos en `product_name` (1.258, pasillo y departamento `missing`) | Rellenar con `'Unknown'` | 0 nulos |
| Nulos en `add_to_cart_order` (836) | Conservar | Están en 70 pedidos de 65 o más productos: el sistema solo registra posiciones hasta la 64 |
| Espacios dobles (403) y espacios invisibles (16) en nombres | Estandarizar con `.str.replace`, `.str.strip` y `.str.title` | 6.321 nombres cambiaron, solo en espacios y mayúsculas |
| 141 nombres repetidos después de estandarizar | Conservar y documentar | Tienen `product_id` distintos; se agrupa por `product_id` |

Toda la limpieza quedó en un **pipeline** (`pipe`) con un informe de cambios (`data/processed/cleaning_report.csv`) y una validación con `assert`. El pipeline produce exactamente las mismas tablas que la limpieza manual.

| Tabla | Paso | Filas | Nulos | Memoria (MB) |
|---|---|---|---|---|
| orders | crudo | 478.967 | 28.819 | 23,0 |
| orders | sin duplicados | 478.952 | 28.817 | 23,0 |
| orders | tipos | 478.952 | 28.817 | 7,2 |
| products | crudo | 49.694 | 1.258 | 3,1 |
| products | nombres | 49.694 | 0 | 3,1 |
| order_prods | crudo | 4.545.007 | 836 | 145,4 |
| order_prods | tipos | 4.545.007 | 836 | 54,5 |

Otras comprobaciones del Reto 2: `order_prods` no tiene duplicados ni pares pedido-producto repetidos; `aisles` y `departments` no necesitan limpieza de texto; los productos `'Unknown'` aparecen en 9.547 filas (8.254 pedidos) y se conservan porque son compras reales; y el valor centinela `missing` no debe volverse nulo antes de agrupar, porque `groupby` descartaría esas filas.

## 2. Filtrado, ordenamiento y agrupación (Lección 3)

- **Hora pico:** las 10 a. m. (40.578 pedidos). Los domingos el pico se corre a la tarde (15 h, 7.585 pedidos).
- **Días:** el domingo (84.090) y el lunes (82.185) concentran más pedidos.
- **Frecuencia:** el 9,31 % de los pedidos llega exactamente a los 7 días; la mediana de espera es 8 días en fin de semana y 7 entre semana.
- **Segmentos de clientes:**

| Segmento | Clientes | % | Pedidos | Espera mediana (días) |
|---|---|---|---|---|
| Ocasional (1 pedido) | 55.357 | 35,2 | 55.357 | 16 |
| Regular (2 a 4) | 71.553 | 45,4 | 191.649 | 11 |
| Frecuente (5 o más) | 30.527 | 19,4 | 231.946 | 6 |

- Los clientes frecuentes compran más en la mañana (35,2 % frente a 30,0 % de los ocasionales).
- La tasa de recompra baja con la posición del carrito: 0,679 en la posición 1 y 0,617 en la 5.

## 3. Combinación de datasets (Lección 4)

- La **tabla maestra** une `order_prods` con `products`, `aisles`, `departments` y `orders` usando `how='left'` y `validate='m:1'`. Conserva las 4.545.007 filas en cada paso (comprobado con `assert`) y termina con 14 columnas.
- La auditoría con `indicator=True` muestra **28.906 pedidos sin productos**. Un `inner` los descarta sin avisar (4.545.007 filas frente a 4.573.913 con `left`).
- **Por departamento:** produce (1.328.302 artículos, tasa de recompra 0,651), dairy eggs (759.842; 0,671) y snacks (404.850; 0,574).
- **Por pasillo:** la mayor recompra está en milk (0,781) y la menor en spices seasonings (0,152).
- **Por producto:** Banana es el más pedido (66.050 veces) y el líder de produce; en lácteos, el más fiel es Whole Organic Omega 3 Milk (tasa 0,863 con 1.286 pedidos).
- **Por cliente:** 149.626 clientes con productos, 30,38 artículos en promedio; el que más compró (103634) tiene 580 artículos en 18 pedidos.

## 4. Archivos generados (`data/processed/`)

| Archivo | Contenido |
|---|---|
| `cleaning_report.csv` | Informe de cambios del pipeline de limpieza |
| `department_summary.csv` | Artículos y tasa de recompra por departamento |
| `product_profile.parquet` | Ficha de 45.573 productos |
| `department_leaders.csv` | Producto líder en recompra de cada uno de los 18 departamentos |
| `instacart_master.parquet` | Tabla maestra (no se sube a GitHub por pesar ~104 MB; se regenera con el notebook) |

## 5. Conclusiones

1. Limpiar no es borrar: cada problema se resolvió según su causa, y los nulos con causa conocida se conservaron.
2. Traducir cada pregunta a "métrica por grupo" permite responderla con `groupby`, `pivot_table` o `crosstab`.
3. Un `merge` debe validarse: con `how='left'`, `validate` e `indicator` se confirma que no se crean ni pierden filas.
4. Con estos pasos, los datos de Instacart quedan limpios, combinados en una sola tabla y listos para analizar los hábitos de compra con confianza.
