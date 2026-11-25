# Tablero Streamlit

Frontend Streamlit **autocontenido** del análisis electoral y
poblacional de Sonora. Incluye su propia copia de la lógica de datos
y figuras para no depender del paquete compartido con Dash.

## Ejecutar

```bash
# desde la raíz del repo
uv run streamlit run vothmo/dashboard/streamlit/app.py    # → :8501
# o como módulo
uv run python -m vothmo.dashboard.streamlit.app
```

## Estructura

| Archivo | Rol |
|---|---|
| `app.py` | La aplicación completa: configuración de página, navegación oculta (`/` = portada-historia, `/herramienta` = pestañas) y todas las páginas. |
| `data.py` | **Copia** de `vothmo/dashboard/data.py`: cargadores tidy memoizados (`lru_cache`) y tablas derivadas. |
| `figures.py` | **Copia** de `vothmo/dashboard/figures.py`: constructores de figuras Plotly. |
| `narrativa.py` | **Copia** de `vothmo/dashboard/narrativa.py`: textos markdown. |
| `data/` | **Copia de los artefactos** de `data/processed/` que usa el tablero (~20 MB): `conteos_sonora.csv.gz`, `mun_ine_inegi.csv`, `censo_inegi_municipios.csv`, `conapo_sonora.csv`, `escenarios_*.csv` (4), `municipios_simp.gpkg`, `distritos_simp_{2019..2025}.gpkg` (7) y `secciones_simp_{2019..2025}.gpkg` (7). |

## Dependencias que deben quedarse en su lugar

- `data.py`, `figures.py` y `narrativa.py` deben permanecer **dentro de
  este paquete**: se importan entre sí con rutas
  `vothmo.dashboard.streamlit.*` (`app.py` → `data`/`figures`/`narrativa`,
  `figures.py` → `data`, `narrativa.py` → `data`).
- `data.py` ya **no** importa `vothmo.config`: su `PROCESSED` apunta a la
  copia local `./data/` (`Path(__file__).parent / "data"`). Los artefactos
  se regeneran con el pipeline (`notebooks/00_preparacion_datos.ipynb` →
  `data/processed/`); para actualizarlos aquí hay que volver a copiarlos.
- `data/` es una **copia**: si el pipeline regenera los artefactos, este
  tablero seguirá mostrando la versión copiada hasta sincronizar.

## Caveats

- **Son copias, no imports compartidos**: los originales siguen en
  `vothmo/dashboard/` para el frontend Dash. Si se corrige un bug o se
  agrega una figura/función en los originales, hay que **sincronizar
  manualmente** estas copias o los dos tableros divergirán.
- Streamlit **no recarga módulos importados**: tras editar cualquier
  archivo de este paquete hay que reiniciar el proceso (Ctrl+C y volver
  a ejecutar), no basta con recargar la página.
