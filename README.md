# Exploración de datos de Instacart con Python y Pandas

**Aprendiz:** Juan Camilo Montes Velásquez
**Programa:** Tecnólogo en Análisis y Desarrollo de Software (ADSO), SENA
**Evidencia:** GA1-220501094-01-AA1-EV04 – Fundamentos en Pandas: Explorando Instacart

## Descripción

Exploración inicial de cinco conjuntos de datos de Instacart (plataforma de compras de supermercado) para conocer su estructura, consultar registros con indexación y filtros, y obtener indicadores básicos sobre los hábitos de compra de los clientes.

**Pregunta orientadora:** ¿Cómo utilizar Python y Pandas para explorar los datos de Instacart, identificar las características de los pedidos y productos, y obtener información relevante sobre los hábitos de compra de sus clientes?

## Estructura del repositorio

```
exploracion-instacart-pandas/
├── README.md
├── .gitignore
├── requirements.txt
├── data/
│   └── raw/          <- aquí van los 5 CSV (no se suben a GitHub)
└── notebooks/
    └── Exploracion_Instacart_Pandas.ipynb
```

## Datos

| Archivo | Registros | Columnas | Contenido |
|---|---|---|---|
| `instacart_orders.csv` | 478.967 | 6 | Pedidos: usuario, día, hora y días desde el pedido anterior |
| `products.csv` | 49.694 | 4 | Catálogo de productos con pasillo y departamento |
| `aisles.csv` | 134 | 2 | Nombres de los pasillos |
| `departments.csv` | 21 | 2 | Nombres de los departamentos |
| `order_products.csv` | 4.545.007 | 4 | Productos de cada pedido, posición en el carrito y recompra |

Los archivos usan punto y coma (`;`) como separador.

Los CSV **no están en el repositorio** por su tamaño (`order_products.csv` pesa unos 90 MB). Para ejecutar el notebook, descárgalos de la carpeta compartida del curso y guárdalos en `data/raw/`:
https://drive.google.com/drive/folders/19vYGYdE0hI1DzvpBcCZskzJ0u2TZjwas

## Cómo ejecutarlo

```bash
python -m venv .venv
.venv\Scripts\activate        # Windows
pip install -r requirements.txt
jupyter notebook notebooks/Exploracion_Instacart_Pandas.ipynb
```

También se puede abrir en Google Colab, subiendo los CSV y ajustando las rutas de carga.

## Contenido del notebook

1. Comprensión del problema y reconocimiento de los datos
2. Importación y exploración inicial (`read_csv`, `head`, `tail`, `shape`)
3. Estructura y calidad inicial (`columns`, `dtypes`, `info`, valores ausentes, tabla de diagnóstico)
4. Indexación y filtrado lógico (`loc[]`, condiciones simples y combinadas)
5. Indicadores básicos (`mean`, `sum`, `describe`) y diferencia entre registros, pedidos y usuarios
6. Síntesis: respuestas, hallazgos, limitaciones y conclusiones

## Principales hallazgos

- La mitad de los pedidos se hace entre las 10 y las 16 horas (mediana: 13 h).
- Entre un pedido y el siguiente pasan en promedio 11,1 días (mediana: 7 días).
- Cerca del 59 % de los artículos comprados son recompras.
- Los datos tienen valores ausentes, 15 pedidos duplicados y 28.906 pedidos sin productos registrados.
