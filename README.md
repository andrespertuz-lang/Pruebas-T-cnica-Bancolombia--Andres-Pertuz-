# Análisis de riesgo crediticio — sector eléctrico colombiano (Bancolombia)

Scorecard de riesgo crediticio (inspirado en Altman Z-Score, adaptado a
utilities reguladas) construido a partir de estados financieros en formato
**XBRL** publicados en el RNVE de la Superintendencia Financiera de
Colombia, para 5 empresas: **EPM, ISA, ISAGEN, CELSIA y EMGESA/Enel
Colombia**, con datos anuales entre 2015 y 2025.

## Contenido del repositorio

```
├── analisis_riesgo_crediticio.ipynb              # notebook con todo el análisis
├── resultado_completo.xlsx                       # salida paso 1: ratios por empresa-año
├── scorecard_riesgo.xlsx                         # salida paso 2: scorecard final
├── Analisis_Riesgo_Sector_Electrico_Bancolombia_Andres_Pertuz.pbix   # tablero Power BI
├── *.xbrl                                        # estados financieros de entrada (55 archivos, las 5 empresas)
└── video/                                        # video de presentación de resultados
```

Los `.xbrl` están todos en la raíz del repositorio. No hace falta separarlos por
empresa: el notebook identifica a qué empresa pertenece cada archivo leyendo
su contenido (función `empresa_canonica`), no por el nombre de la carpeta.

## Qué hace el análisis

El notebook tiene 2 partes:

**1. Extracción de ratios (celda 1).** Lee los archivos `.xbrl` de cada
carpeta de datos, identifica los contextos de balance y de resultados del
año, y calcula 4 ratios de riesgo por empresa-año:

- Deuda financiera / EBITDA (apalancamiento)
- Margen EBITDA (rentabilidad operativa)
- Razón corriente (liquidez de corto plazo)
- Cobertura de intereses (capacidad de servicio de deuda)

Incluye correcciones documentadas para casos puntuales donde el XBRL no
trae el dato completo (`VALORES_VERIFICADOS`, verificados a mano contra el
PDF del estado financiero) o donde el emisor reportó en la unidad
equivocada (`CORRECCIONES_DE_ESCALA`).

**2. Scorecard (celda 2).** Toma `resultado_completo.xlsx`, puntúa cada
ratio de 1 a 5 con peso igual (25% cada uno), calcula un score compuesto,
lo clasifica en banda de riesgo (Bajo / Moderado / Alto), agrega la
tendencia año a año por empresa, y lo cruza con eventos cualitativos reales
documentados (cambios de calificación, fusiones, intervenciones — fuente:
Fitch, BRC o prensa).

## Requisitos

- Python 3.9+ con Jupyter (`pip install jupyter openpyxl`)
- Opcional: Power BI Desktop para abrir el `.pbix`

## Cómo reproducir la ejecución

1. Clona o descarga este repositorio y abre una terminal en esa carpeta.

2. Instala la dependencia:
   ```bash
   pip install jupyter openpyxl
   ```

3. Abre el notebook:
   ```bash
   jupyter notebook analisis_riesgo_crediticio.ipynb
   ```

4. **Antes de correr la celda 2**, genera `resultado_completo.xlsx`
   ejecutando esto en una celda nueva justo después de la celda 1 (que ya
   define las funciones de extracción), apuntando a la carpeta del
   repositorio (todos los `.xbrl` están en la raíz):

   ```python
   from pathlib import Path

   archivos = encontrar_archivos_xbrl(Path("."))
   resultados = procesar_todos(archivos)
   guardar_excel(resultados, Path("resultado_completo.xlsx"))
   ```

5. Corre la celda 2 (scorecard). Va a leer `resultado_completo.xlsx` y
   generar `scorecard_riesgo.xlsx` con el resultado final. Al final debe
   imprimir algo como:
   ```
   Listo -> scorecard_riesgo.xlsx (55 filas)
   ```

6. (Opcional) Abre `Analisis_Riesgo_Sector_Electrico_Bancolombia_Andres_Pertuz.pbix`
   en Power BI Desktop para ver el tablero interactivo con estos resultados.

### Notas

- Algunas filas del Excel de salida quedan con la columna `notas` con
  observaciones (dato no encontrado en el XBRL, corrección de escala
  aplicada, valor verificado a mano). Es esperado: no todos los emisores
  reportan todas las partidas de la misma forma año a año.
- `resultado_completo.xlsx` y `scorecard_riesgo.xlsx` ya están incluidos en
  el repositorio con el resultado ya generado, por si solo quieres
  revisarlos sin correr nada.

## Video de resultados
https://youtu.be/rzVS0dNzdEE
