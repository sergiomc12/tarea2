# Changelog

Formato basado en [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added

- `AGENTS.md` inicial: alcance completo extraído de `hw02-2026-2.docx`
  (procedimiento paso a paso, estructura obligatoria del informe,
  resumen de los 4 anexos), tabla de cobertura real del notebook de
  referencia sección por sección (qué sirve como apoyo y qué hay que
  construir desde cero), y hechos verificados directamente contra
  `application_train.csv` (307.511 filas × 122 columnas, balance del
  target 91,93%/8,07%, 68 columnas con nulos y sus porcentajes reales).

- Regla transversal en `AGENTS.md`: todo gráfico/tabla debe ir acompañado
  de una explicación profunda (no solo mostrarse) — aplica a los 8 pasos
  del procedimiento, no es opcional.
- Entorno del proyecto inicializado con `uv` (`pyproject.toml`, `uv.lock`,
  `.venv`): pandas, numpy, matplotlib, seaborn, scikit-learn,
  imbalanced-learn, category_encoders, jupyter, nbconvert, ipykernel.
  Verificado que todos importan correctamente.
- Estructura de carpetas creada: `notebooks/`, `output/figures/`,
  `output/tables/`, `report/`.
- Especificación completa de formato IEEE agregada a `AGENTS.md`
  (columnas, tipografía, numeración romana de tablas/arábiga de figuras
  con versalitas, sección de código de una sola columna) — reemplaza la
  descripción genérica anterior de "Courier New" suelto.
- Decidido: informe se maqueta en LaTeX, clase `IEEEtran`. Esqueleto
  creado en `report/main.tex` (secciones mapeadas contra `hw02-2026-2.docx`,
  bloque `strip`+`listings` para el código en Courier 8pt de una columna,
  bibliografía con las 2 referencias exigidas). Sin distribución LaTeX
  local instalada — compilar vía Overleaf o instalar MiKTeX/TeX Live.

- Autoría confirmada: Sergio Mondragon Contreras. `report/main.tex`
  actualizado con el nombre real; nombre de archivo final definido en
  `AGENTS.md` como `t2_2026-1_mondragon.contreras.sergio.pdf` (asumiendo
  un solo nombre de pila — confirmar si hay segundo nombre).

- MiKTeX instalado localmente (vía `winget`, paquete `MiKTeX.MiKTeX`) y
  `report/main.tex` compilado con éxito con `pdflatex` (2 pasadas) →
  `report/main.pdf` (2 páginas). Fix necesario: el entorno `strip` que usa
  la sección de Código Fuente para romper a una columna no lo provee
  `IEEEtran` — lo da el paquete `cuted` (`\usepackage{cuted}`), agregado
  al preámbulo. `.gitignore` actualizado para excluir artefactos de build
  de LaTeX (`*.aux`, `*.log`, etc.) sin excluir el PDF final.
- `notebooks/tarea2_home_credit.ipynb` creado y **ejecutado de punta a
  punta contra el CSV real** (no simulado) — pasos 1 a 3 del
  procedimiento:
  - **Paso 1** (Descripción del problema): redactado en base a
    `HomeCreditDefault.docx`, en términos propios.
  - **Paso 2** (Descripción general del dataset): shape, tipos de dato
    por columna, cardinalidad de categóricas, rango de numéricas — con
    conclusión explícita de cómo condiciona los pasos siguientes
    (Tabla I exportada a `output/tables/tabla01_tipos_de_dato.csv`).
  - **Paso 3** (Análisis del Target): balance real (91,93%/8,07%),
    Fig. 1a/1b (barras + pastel, 300dpi, exportada a
    `output/figures/fig01_target_balance.png`), explicación de qué es el
    balanceo de datos y postura propia sobre la asimetría de costos en
    riesgo crediticio (Tabla II en
    `output/tables/tabla02_balance_target.csv`).
- **Bug real encontrado y corregido durante la ejecución:** pandas 3.0.5
  (la versión instalada) reporta las columnas de texto con dtype `str`
  en vez de `object` — el primer intento de contar columnas
  categóricas con `.get('object', 0)` daba 0 en vez de 16. Corregido para
  sumar ambas etiquetas (`object` + `str`), robusto a la versión de
  pandas. Documentado como riesgo conocido.
- **`report/main.tex` actualizado con el contenido real** (antes solo
  tenía el esqueleto con comentarios `%` — el PDF compilado no reflejaba
  nada del notebook). Ahora incluye: Introducción, El Problema y
  Metodología redactados; subsecciones IV-A/B/C (pasos 1-3) con el texto
  completo, Tabla I (tipos de dato), Tabla II (balance del target) y
  Fig. 1 insertadas con las convenciones IEEE correctas (numeración
  romana/arábiga separada, caption arriba/abajo respectivamente);
  subsecciones IV-D a IV-H y las secciones V-VII marcadas explícitamente
  como *pendiente* (en cursiva, sin inventar contenido); sección de
  Código Fuente con las funciones reales `load_data`, `describe_dataset`,
  `analyze_target` y `mainProcess` (refactorizadas del notebook, cada una
  en su propio recuadro `\lstlisting`, dentro del bloque `strip` a una
  columna). Recompilado con `pdflatex` (2 pasadas) y **verificado
  visualmente** rasterizando las 4 páginas con `pdftoppm` — layout de dos
  columnas, tablas y figura correctas, código legible.

- **Versión Word agregada**: `report/build_docx.py` (nuevo, usa
  `python-docx`) genera `report/main.docx` con el mismo contenido que
  `main.tex` — título/autor a una columna, cuerpo a dos columnas (vía XML
  de bajo nivel, `python-docx` no expone columnas en su API alta),
  Tabla I/II con "TABLE I/II" en versalitas, Fig. 1 con caption abajo,
  sección de Código Fuente a una columna con las 4 funciones reales, cada
  una en su propio recuadro (bordes de párrafo). LibreOffice instalado
  (`winget`, `TheDocumentFoundation.LibreOffice`) para verificar
  visualmente el `.docx` convirtiéndolo a PDF y rasterizando, mismo
  método que con el `.tex`.
  - **Incidente evitado:** la primera conversión de verificación
    (`soffice --headless --convert-to pdf`) escribió sobre
    `report/main.pdf` — pisando el PDF real compilado desde LaTeX. Se
    detectó de inmediato (tamaño de archivo no coincidía) y se corrigió
    recompilando `main.tex`. Desde entonces, cualquier conversión de
    verificación del `.docx` se hace en un directorio de salida distinto
    (`--outdir`), nunca en `report/` directamente.
  - **Bug real encontrado y corregido:** las tablas generadas con
    `python-docx` no traían ancho de columna fijo — Word/LibreOffice
    autoajustaban y colapsaban columnas de contenido corto (la Tabla I
    perdía la columna de valores numéricos por completo; la Tabla II se
    solapaba con el texto siguiente). Corregido fijando
    `w:tblLayout type="fixed"` + ancho explícito por celda
    (`set_table_fixed_layout`/`col_widths_in`) — verificado visualmente
    que ya no ocurre.

- **Diseño de código cambiado a orientado a objetos**, a pedido explícito:
  el enunciado dice "encuadre cada función **o método**" — la palabra
  "método" implica clases, no solo funciones sueltas. Los tres artefactos
  (notebook, `main.tex`, `build_docx.py`) se reescribieron alrededor de
  una única clase orquestadora, `HomeCreditAnalysis`, con un método por
  paso del procedimiento (`cargar_datos`, `describir_dataset`,
  `analizar_target`, y los `# TODO` de los pasos 4-8 ya declarados como
  métodos futuros de la misma clase — no se crean clases nuevas por
  paso). `mainProcess()` instancia la clase y llama a sus métodos en
  orden. Notebook re-ejecutado de punta a punta, `main.tex` recompilado
  (5 páginas) y `main.docx` regenerado — los tres verificados
  visualmente, mismos resultados numéricos que antes del refactor
  (307,511×122, 91.93%/8.07%).
- **Todas las referencias a `AGENTS.md` eliminadas de los tres artefactos
  del ejercicio** (notebook, `main.tex`, `build_docx.py`), a pedido
  explícito — `AGENTS.md` es un documento de trabajo interno, no algo que
  deba citarse en el código o el informe entregable. Cada referencia se
  reemplazó por `hw02-2026-2.docx` (con la sección/anexo específico
  cuando aplicaba) o simplemente se quitó cuando no había un punto
  equivalente en el enunciado (ej. la nota sobre el dtype `str` de pandas
  3.x, que es un detalle de implementación sin contraparte en el
  enunciado). `AGENTS.md`/`CHANGELOG.md` siguen siendo documentos de
  seguimiento internos del desarrollo y sí pueden referenciarse entre
  sí — la restricción aplica solo al contenido que se entrega.

### Pendiente

- Confirmar mecanismo de compilación de `report/main.tex` para la versión
  final (ya se compiló localmente con MiKTeX — decidir si esa es la vía
  definitiva o se prefiere Overleaf).
- Pasos 4 a 8 del procedimiento (Calidad de datos, Limpieza, Conversión de
  categóricas, Balanceo, Ranking de atributos + PCA): sin empezar — son
  los que no tienen ningún apoyo en el notebook de referencia, ver tabla
  de cobertura en `AGENTS.md`. Cada uno, al desarrollarse en el notebook,
  debe volver a portarse a `report/main.tex` (no es automático).
- Resumen (abstract), Resultados, Conclusiones y Recomendaciones: solo se
  pueden redactar de verdad cuando cierren los 8 pasos.
