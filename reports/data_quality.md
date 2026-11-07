# Calidad inicial de los datos de Instacart

**Aprendiz:** Juan Camilo Montes Velásquez
**Origen:** GA1-220501094-01-AA1-EV04 – Fundamentos en Pandas (notebook [`notebooks/data_quality.ipynb`](../notebooks/data_quality.ipynb))

Este informe resume el diagnóstico de calidad que se hizo en la actividad anterior, antes de limpiar los datos. Las acciones de limpieza se documentan en [`data_processing.md`](data_processing.md).

## Archivos

| Archivo | Filas | Columnas | Una fila es | Clave |
|---|---|---|---|---|
| `instacart_orders.csv` | 478.967 | 6 | Un pedido | `order_id` |
| `order_products.csv` | 4.545.007 | 4 | Un producto dentro de un pedido | `order_id` + `product_id` |
| `products.csv` | 49.694 | 4 | Un producto del catálogo | `product_id` |
| `aisles.csv` | 134 | 2 | Un pasillo | `aisle_id` |
| `departments.csv` | 21 | 2 | Un departamento | `department_id` |

Los cinco archivos usan punto y coma (`;`) como separador.

## Problemas encontrados

| Tabla | Problema | Evidencia |
|---|---|---|
| orders | Filas duplicadas | 15 filas idénticas; `order_id` no es único (478.952 distintos) |
| orders | Nulos en `days_since_prior_order` | 28.819, que coinciden con los primeros pedidos (`order_number == 1`) |
| orders | Columnas enteras guardadas como decimal | `days_since_prior_order` es `float64` por sus nulos |
| orders | Valor topado | `days_since_prior_order` llega máximo a 30 (51.338 pedidos con 30) |
| orders | Pedidos sin productos | 28.906 pedidos no aparecen en `order_products` |
| products | Nulos en `product_name` | 1.258, todos del pasillo 100 y el departamento 21 (`missing`) |
| order_products | Nulos en `add_to_cart_order` | 836 |
| aisles / departments | Valor centinela | El pasillo 100 y el departamento 21 se llaman `missing` (pandas no los detecta como nulos) |

## Conclusión

Los datos sirven para analizar los hábitos de compra, pero antes hay que quitar duplicados, corregir tipos, decidir qué hacer con cada tipo de nulo y estandarizar el texto. También hay que distinguir entre registros, pedidos, productos y clientes.
