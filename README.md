# Análisis de datos de Instacart con Python y Pandas

**Aprendiz:** Juan Camilo Montes Velásquez
**Programa:** Tecnólogo en Análisis y Desarrollo de Software (ADSO), SENA

| Evidencia | Contenido | Notebook | Informe |
|---|---|---|---|
| GA1-220501094-01-AA1-EV04 – Fundamentos en Pandas | Exploración inicial y calidad de los datos | [`notebooks/data_quality.ipynb`](notebooks/data_quality.ipynb) | [`reports/data_quality.md`](reports/data_quality.md) |
| GA1-220501094-01-AA1-EV06 – Limpieza, análisis y estructuración | Lecciones 2, 3 y 4: laboratorios guiados y retos | [`notebooks/pandas_labs.ipynb`](notebooks/pandas_labs.ipynb) | [`reports/data_processing.md`](reports/data_processing.md) |

**Pregunta orientadora (EV06):** ¿Cómo preparar y estructurar los datos de Instacart para obtener información confiable sobre los hábitos de compra?

## Estructura del repositorio

```
exploracion-instacart-pandas/
├── README.md
├── .gitignore
├── requirements.txt
├── data/
│   ├── raw/                         <- los 5 CSV originales (no se suben)
│   └── processed/                   <- archivos generados por pandas_labs.ipynb
│       ├── cleaning_report.csv
│       ├── department_summary.csv
│       ├── product_profile.parquet
│       ├── department_leaders.csv
│       └── instacart_master.parquet (no se sube, ver abajo)
├── notebooks/
│   ├── data_quality.ipynb           <- EV04
│   └── pandas_labs.ipynb            <- EV06
└── reports/
    ├── data_quality.md              <- EV04
    └── data_processing.md           <- EV06
```

## Datos

| Archivo | Registros | Columnas | Una fila es |
|---|---|---|---|
| `instacart_orders.csv` | 478.967 | 6 | Un pedido |
| `order_products.csv` | 4.545.007 | 4 | Un producto dentro de un pedido |
| `products.csv` | 49.694 | 4 | Un producto del catálogo |
| `aisles.csv` | 134 | 2 | Un pasillo |
| `departments.csv` | 21 | 2 | Un departamento |

Los archivos usan punto y coma (`;`) como separador. **No se suben al repositorio** por su tamaño (`order_products.csv` pesa unos 90 MB). Descárgalos de la carpeta del curso y guárdalos en `data/raw/`:
https://drive.google.com/drive/folders/19vYGYdE0hI1DzvpBcCZskzJ0u2TZjwas

### Archivos procesados (`data/processed/`)

| Archivo | Qué contiene | Lo genera |
|---|---|---|
| `cleaning_report.csv` | Informe de cambios del pipeline de limpieza (filas, nulos y memoria por paso) | Laboratorio 2 |
| `department_summary.csv` | Artículos y tasa de recompra por departamento | Lección 4 |
| `product_profile.parquet` | Una fila por producto: veces pedido, clientes, tasa de recompra y posición media en el carrito | Laboratorio 4 |
| `department_leaders.csv` | Producto con mayor tasa de recompra de cada departamento (mínimo 1 000 pedidos) | Laboratorio 4 |
| `instacart_master.parquet` | Tabla maestra: las cinco tablas unidas (4.545.007 filas, 14 columnas) | Lección 4 |

`instacart_master.parquet` pesa unos 104 MB, más que el límite de 100 MB de GitHub, así que está en `.gitignore`. Se genera de nuevo al ejecutar `pandas_labs.ipynb`.

## Cómo ejecutarlo

Requisitos: Python 3.12 o superior (pandas 3 necesita 3.11+) y Git.

```bash
git clone https://github.com/juancamilomontes/exploracion-instacart-pandas.git
cd exploracion-instacart-pandas
python -m venv .venv
.\.venv\Scripts\Activate.ps1          # Windows PowerShell
# source .venv/bin/activate          # macOS / Linux
python -m pip install -r requirements.txt
jupyter lab
```

1. Copia los cinco CSV en `data/raw/`.
2. Abre `notebooks/pandas_labs.ipynb` y ejecuta todas las celdas en orden (*Run → Run All Cells*). Cada lección usa las variables de la anterior, así que no se deben ejecutar celdas sueltas. Tarda menos de un minuto y crea los archivos de `data/processed/`.
3. `notebooks/data_quality.ipynb` (EV04) se ejecuta de forma independiente.

`requirements.txt` se generó con `python -m pip freeze` e incluye pandas 3.1.1, NumPy 2.5.4, PyArrow 26.0.1 (necesario para guardar Parquet) y JupyterLab 4.6.5. Si PowerShell bloquea la activación, ejecuta una vez `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`.

## Flujo de trabajo con Git

- `main`: versiones estables.
- `develop`: integra el trabajo de cada actividad.
- `feature/pandas-data-processing`: rama de la EV06 (lecciones 2, 3 y 4), fusionada en `develop`.

## Principales resultados (EV06)

- La limpieza eliminó 15 pedidos duplicados, redujo la memoria de `orders` de 23,0 a 7,2 MB y estandarizó 6.321 nombres de productos.
- La hora pico es las 10 a. m., y el 19,4 % de los clientes (frecuentes) hace casi la mitad de los pedidos.
- La tabla maestra une las cinco tablas sin crear ni perder filas: 4.545.007 filas.

El detalle está en [`reports/data_processing.md`](reports/data_processing.md).
