# AGENTS.md — Tarea 2: Machine Learning (Home Credit Default Risk)

> Instrucciones de desarrollo para el/los agente(s) de IA que construyan la
> solución de esta tarea. Define el alcance exacto pedido por el enunciado,
> qué material de apoyo sirve para qué parte, los hechos ya verificados del
> dataset (para no re-derivarlos de cero en cada sesión), y la estructura
> exigida del informe final.
>
> Versión del documento: 1.0 | Última actualización: 2026-07-31

---

## 0.1 Antes de proponer cambios

1. Revisar `CHANGELOG.md` para ver qué parte del procedimiento ya está
   resuelta y qué sigue pendiente.
2. La fuente normativa de **qué** hay que entregar es `hw02-2026-2.docx`
   — este `AGENTS.md` la reorganiza para desarrollo, pero si hay duda o
   conflicto, el docx original manda.
3. Actualizar `CHANGELOG.md` con una entrada bajo `[Unreleased]` por cada
   paso del procedimiento que se complete.
4. Actualizar este `AGENTS.md` únicamente si cambia el alcance del
   ejercicio (ej. el profesor amplía el enunciado) — no para detalles de
   implementación, esos van en el código/notebook y en el propio informe.

---

## Qué es este proyecto

Tarea 2 del curso de Machine Learning (fecha del enunciado: 26 de julio de
2026). Es un ejercicio de **análisis exploratorio de datos (EDA)**,
**limpieza**, **codificación de categóricos**, **balanceo de clases** y
**ranking de atributos por capacidad predictiva**, aplicado al dataset
público de la competencia Kaggle **Home Credit Default Risk**.

**No es un proyecto de software con tests/CI** — el entregable final es un
**único archivo PDF** con el desarrollo completo del procedimiento,
código fuente incluido, siguiendo una estructura de informe académico
estricta (ver "Estructura obligatoria del informe" más abajo).

**Alcance de datos — restricción explícita del enunciado:** *"Para
propósitos de este ejercicio hacer uso solo del archivo:
`application_train.csv`"*. Los demás CSV (`bureau.csv`,
`previous_application.csv`, `POS_CASH_balance.csv`,
`credit_card_balance.csv`, `installments_payments.csv`,
`bureau_balance.csv`) **no se usan** para resolver esta tarea — están en
`data/` porque son parte del dataset completo de Kaggle, pero cualquier
sección del notebook de referencia que haga merge con esas tablas
(bureau, previous_application) queda **fuera de alcance**, ver más abajo.

---

## Fuentes de este proyecto y qué apoyo real ofrece cada una

| Archivo | Rol | Es determinante? |
|---|---|---|
| `hw02-2026-2.docx` | Enunciado completo de la tarea — qué hay que entregar, cómo, y estructura del informe. | **Sí** — fuente normativa única. |
| `HomeCreditDefault.docx` | Descripción de la competencia Kaggle (contexto de negocio de Home Credit, qué es cada uno de los 10 archivos, diccionario de columnas, métrica de evaluación ROC-AUC). | No — apoyo. Útil casi en exclusiva para la sección **"Descripción del problema"** (origen y propósito original de los datos). |
| `home-credit-default-risk-extensive-eda.ipynb` | Notebook de EDA de referencia (Kaggle, en inglés). | No — apoyo parcial, ver tabla de cobertura abajo. **No** resuelve limpieza, codificación, balanceo ni ranking/PCA — esas partes hay que construirlas desde cero. |

### Cobertura real del notebook de referencia, sección por sección del enunciado

No asumir que el notebook cubre todo el enunciado — **no lo hace**. Esta
tabla es el resultado de leerlo completo, no una suposición:

| Sección del enunciado (`hw02-2026-2.docx`) | ¿El notebook ayuda? | Detalle |
|---|---|---|
| 1. Descripción del problema | Parcial | El notebook tiene una intro casi idéntica a `HomeCreditDefault.docx` (mismo texto, en inglés) — usar `HomeCreditDefault.docx` como fuente principal, está en español. |
| 2. Descripción general del dataset | **Sí** | `application_train.shape`, `.head()`, `.columns.values` — patrón directo, adaptar. |
| 3. Análisis del Target | **Sí** | Gráfico de barras de balance de clases (celda `cell-53`) — usar como base, pero el enunciado también pide investigar y opinar sobre balanceo de datos, eso no está en el notebook. |
| 4. Calidad de los datos | Parcial | Función `missing_data()` (celda `cell-35`) da % de nulos por columna — reutilizable. **No** cubre: filas con nulos, outliers, duplicados, columnas de una sola categoría — construir aparte. |
| 4b. EDA antes de limpieza (Anexo 1) | Parcial | Los gráficos `plot_stats`/`plot_distribution`/`plot_distribution_comp` (categórica vs TARGET, KDE por clase) son un buen patrón de estilo para el EDA — reutilizar la lógica, no el dataset (el notebook mezcla `application_train` con `bureau`/`previous_application`, fuera de alcance aquí). |
| 5. Limpieza de datos | **No** | El notebook nunca limpia el dataset (ni elimina columnas, ni imputa, ni trata outliers). Construir desde cero, guiado por Anexo 1 del enunciado. |
| 5b. EDA después de limpieza | **No** | Repetir el mismo patrón de gráficos que en el punto 4b, sobre el dataset ya limpio. |
| 6. Conversión de categóricas a numéricas (Anexo 2) | **No** | El notebook nunca codifica categóricas (las usa como texto directo en `value_counts`/`groupby`). Construir desde cero usando las técnicas del Anexo 2 (resumen completo más abajo). |
| 6b. EDA después de conversión | **No** | Repetir el patrón de gráficos, ahora sobre variables numéricas/codificadas. |
| 7. Balanceo de datos (Anexo 3) | **No** | El notebook solo *muestra* el desbalance (barra de conteo), nunca lo corrige. Construir desde cero. |
| 8. Ranking de atributos + histogramas + scatter + PCA (Anexo 4) | **No** | Completamente fuera del notebook. Construir desde cero con `sklearn.feature_selection` (filtros) y `sklearn.decomposition.PCA`. |
| Secciones "Bureau data", "Previous application data", "Contract type" del notebook | — | **Fuera de alcance** — usan `bureau.csv`/`previous_application.csv`, prohibido por el enunciado ("hacer uso solo de `application_train.csv`"). No replicar. |

**Conclusión práctica:** el notebook es útil como plantilla de **estilo de
gráficos** (barras categoría-vs-target, KDE por clase, manejo de outliers
en un histograma) para las secciones 2-4, pero **más de la mitad del
trabajo real (limpieza, encoding, balanceo, ranking/PCA) no tiene ningún
apoyo en el material dado — hay que diseñarlo e implementarlo de cero**,
usando los Anexos del enunciado como guía conceptual, no como código.

---

## Hechos ya verificados del dataset (`application_train.csv`)

Verificados directamente contra el CSV real en `data/` — no re-derivar
estos números salvo para confirmarlos en el informe con el código propio
(el enunciado pide mostrar el procedimiento, no solo el resultado):

- **307.511 filas × 122 columnas** (sin contar el header).
- **Balance del target:** `TARGET=0` (no incumple) = 282.686 filas
  (**91,93%**); `TARGET=1` (incumple) = 24.825 filas (**8,07%**). Ratio
  ≈ 11,4 : 1. Muy por debajo del umbral de 10-20% que el Anexo 3 marca
  como desbalance — **este dataset está claramente desbalanceado**.
- **Tipos de dato (aproximado por muestreo, confirmar con
  `application_train.dtypes` en el notebook):** ~16 columnas
  verdaderamente categóricas/texto (`NAME_CONTRACT_TYPE`, `CODE_GENDER`,
  `FLAG_OWN_CAR`, `FLAG_OWN_REALTY`, `NAME_TYPE_SUITE`,
  `NAME_INCOME_TYPE`, `NAME_EDUCATION_TYPE`, `NAME_FAMILY_STATUS`,
  `NAME_HOUSING_TYPE`, `OCCUPATION_TYPE`, `WEEKDAY_APPR_PROCESS_START`,
  `ORGANIZATION_TYPE`, `FONDKAPREMONT_MODE`, `HOUSETYPE_MODE`,
  `WALLSMATERIAL_MODE`, `EMERGENCYSTATE_MODE`) y ~106 numéricas (incluye
  IDs, flags binarios 0/1, y continuas). Ojo: `FLAG_OWN_CAR` y
  `FLAG_OWN_REALTY` vienen como texto `'Y'`/`'N'`, no como 0/1 — hay que
  codificarlas igual que cualquier categórica binaria.
- **68 de las 122 columnas tienen al menos un valor nulo.** Las de mayor
  porcentaje de nulos son casi todas información normalizada del edificio
  donde vive el cliente (ver `HomeCreditDefault.docx`, columnas
  `_AVG`/`_MODE`/`_MEDI`):

  | Columna(s) | % nulos |
  |---|---|
  | `COMMONAREA_AVG/MODE/MEDI` | 69,87% |
  | `NONLIVINGAPARTMENTS_AVG/MODE/MEDI` | 69,43% |
  | `FONDKAPREMONT_MODE` | 68,39% |
  | `LIVINGAPARTMENTS_AVG/MODE/MEDI` | 68,35% |
  | `FLOORSMIN_AVG/MODE/MEDI` | 67,85% |
  | `YEARS_BUILD_AVG/MODE/MEDI` | 66,50% |
  | `OWN_CAR_AGE` | 65,99% |
  | `EXT_SOURCE_1` | 56,38% |
  | `OCCUPATION_TYPE` | 31,35% |
  | `EXT_SOURCE_3` | 19,83% |
  | `NAME_TYPE_SUITE` | 0,42% |
  | `EXT_SOURCE_2` | 0,21% |
  | `AMT_GOODS_PRICE` | 0,09% |
  | `AMT_ANNUITY`, `CNT_FAM_MEMBERS` | <0,01% (12 y 2 filas) |
  | `DAYS_LAST_PHONE_CHANGE` | <0,01% (1 fila) |

  **Advertencia importante para la sección de Limpieza:** `EXT_SOURCE_1`
  tiene 56% de nulos pero es, según consenso público sobre este dataset,
  de los atributos con mayor poder predictivo del `TARGET` (junto con
  `EXT_SOURCE_2`/`EXT_SOURCE_3`) — **no eliminar columnas solo por
  porcentaje de nulos sin antes evaluar su relación con el target**; el
  enunciado mismo pide "concluir" el criterio de limpieza, este es un caso
  de estudio perfecto para justificar por qué el umbral de "demasiados
  nulos" no puede ser una regla ciega.
- Los demás archivos del dataset (`bureau.csv`: 1.716.428 filas / 17 cols;
  `bureau_balance.csv`: 27.299.925 filas / 3 cols — el más pesado, 359MB;
  `previous_application.csv`: 1.670.214 filas / 37 cols;
  `installments_payments.csv`: 13.605.401 filas / 8 cols;
  `POS_CASH_balance.csv`: 10.001.358 filas / 8 cols;
  `credit_card_balance.csv`: 3.840.312 filas / 23 cols) **no se cargan**
  en esta tarea — mencionarlos solo en la sección "Descripción del
  problema" como contexto del dataset completo de Kaggle, citando
  `HomeCreditDefault.docx`.

---

## Regla transversal: todo gráfico va acompañado de explicación profunda

**Esto aplica a cada uno de los 8 pasos del procedimiento, sin excepción —
no es opcional ni "nice to have".** El enunciado lo exige explícitamente
(*"Explique, concluya, no solo coloque información"*) y es, en la
práctica, el criterio que más pesa en la calidad del informe final.

Regla concreta a seguir en el notebook y en el informe:

- **Ningún gráfico se presenta solo.** Cada figura va precedida por una
  frase que dice qué se va a mostrar y por qué, y seguida por un párrafo
  de interpretación — no una descripción de lo obvio ("la barra azul es
  más alta"), sino una lectura sustantiva: qué patrón revela, si es
  consistente con lo esperado del dominio (riesgo crediticio), y qué
  implica para los pasos siguientes (limpieza, encoding, modelado).
- Cuando un gráfico compara `TARGET=0` vs `TARGET=1` (KDE, barras
  categoría-vs-target), la explicación debe cuantificar la diferencia
  (ej. "la tasa de incumplimiento en *Civil marriage* es ~10% vs. ~7% en
  *Widow*", no solo "hay diferencia").
- Toda tabla igual de tratamiento: números crudos + una frase que diga qué
  significan para la decisión que se está tomando en ese paso.
- Cada figura y tabla numerada (Fig. N / Tabla N, exigido por el
  enunciado) y referenciada por ese número en el texto que la explica.
- Esta regla es la razón por la que cada uno de los 3 checkpoints de EDA
  (antes de limpieza, después de limpieza, después de conversión) debe
  reflejar explícitamente **qué cambió** respecto al checkpoint anterior,
  no repetir los mismos gráficos sin comentario comparativo.

---

## Procedimiento exigido (orden exacto del enunciado)

Cada paso debe ir acompañado de código fuente, explicación del
procedimiento y una conclusión — el enunciado es explícito: *"Explique,
concluya, no solo coloque información"*. Los pasos marcados **(3 puntos)**
están explícitamente ponderados en el enunciado — dedicarles el mayor
rigor.

1. **Descripción del problema** — origen de `application_train.csv`
   (competencia Kaggle Home Credit Default Risk), quién es Home Credit,
   para qué se usó originalmente el dataset (predecir riesgo de impago
   para incluir financieramente a población sin historial crediticio).
   Apoyarse en `HomeCreditDefault.docx`.

2. **Descripción general del dataset** — filas/columnas, tipos de dato por
   columna, rangos de valores, cardinalidad. Explicar cómo esto condiciona
   secciones posteriores (ej. 307K filas es suficiente para entrenar un
   modelo robusto pero el desbalance de clases sigue siendo un problema
   independiente del volumen).

3. **Análisis del Target** — proporción de clases (usar los números reales
   de arriba: 91,93% / 8,07%), gráfico de barras o pastel, comentario sobre
   balanceo. Investigar y explicar qué es el balanceo de datos y cómo
   afecta resultados (no solo definir — dar postura propia, el enunciado
   lo pide explícitamente: *"presente su propio punto de vista"*).

4. **Calidad de los datos (3 puntos)** — % de nulos por columna, filas con
   nulos, outliers/valores inconsistentes, duplicados, columnas de una
   sola categoría (`nunique() == 1`). **EDA antes de la limpieza** (Anexo 1
   completo: tipos, distribuciones, nulos, outliers, únicos,
   correlaciones, análisis del target, sugerencias de limpieza).

5. **Limpieza de datos (3 puntos)** — eliminar columnas con demasiados
   nulos (con criterio justificado, ver advertencia de `EXT_SOURCE_1`
   arriba), rellenar faltantes según tipo (mediana/moda vs. imputación más
   sofisticada), tratar outliers (ej. `DAYS_EMPLOYED` tiene un valor
   centinela conocido de 365243 que hay que investigar y tratar
   explícitamente — visible en el notebook de referencia, celda
   `cell-104`/`cell-105`, como "no es realista"). **EDA después de la
   limpieza.**

6. **Conversión de categóricas a numéricas (Anexo 2)** — aplicar la(s)
   técnica(s) adecuada(s) por columna (ver resumen de técnicas abajo),
   verificar cuántas columnas nuevas se generan, conservar los atributos
   convertidos en el dataset. **EDA después de la conversión.**

7. **Balanceo de datos (Anexo 3)** — elegir y justificar una técnica
   (undersampling, oversampling, SMOTE, o class weights — ver resumen
   abajo). Con 307K filas y solo 8% de clase positiva, SMOTE o class
   weights suelen ser preferibles a undersampling puro (undersampling
   descartaría ~258K filas de la clase mayoritaria, perdiendo señal) —
   decidir y justificar con datos, no de oficio.

8. **Ranking de atributos según capacidad predictiva (Anexo 4)** — **solo
   si el dataset ya se balanceó**. Usar técnicas **Filter** (exigido
   explícitamente por el enunciado, no Wrapper ni Embedded). Implementar
   ≥3 indicadores por atributo (ver candidatos en "Ranking de atributos"
   abajo), mostrar tabla de ranking, concluir qué atributos tienen mejor
   capacidad predictiva, histograma de cada atributo por clase, diagrama
   de dispersión de los atributos seleccionados coloreado por clase, PCA
   sobre atributos originales y PCA sobre atributos seleccionados.
   Investigar y precisar en el informe si los algoritmos Filter usados
   aplican a clasificación y/o regresión, y si son solo binarios o también
   aplican a multiclass/multilabel/multitarget (pregunta de investigación
   explícita del Anexo 4, responder en el informe, no en el código).

---

## Anexo 2 — Técnicas de codificación de categóricas (resumen para implementación)

| Técnica | Tipo de atributo | Cols generadas | Cuándo usarla aquí |
|---|---|---|---|
| Label Encoding (`sklearn.preprocessing.LabelEncoder`) | Ordinal, o binarias sin orden real (`FLAG_OWN_CAR`, `FLAG_OWN_REALTY`, `CODE_GENDER`) | 1 | Rápido para binarias — 2 categorías nunca introducen un orden falso problemático. |
| One-Hot Encoding (`pd.get_dummies` / `OneHotEncoder`) | Nominal | N-1 por columna (con `drop_first=True`) | Para nominales de baja cardinalidad: `NAME_CONTRACT_TYPE`, `NAME_INCOME_TYPE`, `NAME_FAMILY_STATUS`, `NAME_HOUSING_TYPE`, `WEEKDAY_APPR_PROCESS_START`. |
| Ordinal Encoding (`OrdinalEncoder` con orden explícito) | Ordinal con orden real | 1 | `NAME_EDUCATION_TYPE` tiene orden natural (Lower secondary < Secondary < Incomplete higher < Higher education < Academic degree) — usar Ordinal, no One-Hot, para preservarlo. |
| Frequency Encoding | Nominal de alta cardinalidad | 1 | `ORGANIZATION_TYPE` (decenas de categorías) — evita explosión dimensional del One-Hot. |
| Target Encoding | Nominal, alto riesgo overfitting | 1 | Evitar salvo con validación cruzada — el enunciado marca este riesgo explícitamente en el Anexo 2. |
| Binary/Hash Encoding (`category_encoders.BinaryEncoder`) | Nominal, muchísimas categorías (>100) | log₂(n) | `OCCUPATION_TYPE` u `ORGANIZATION_TYPE` si Frequency Encoding no es suficiente. |

## Anexo 3 — Balanceo de datos (resumen)

| Técnica | Qué hace | Apto para este dataset (307K filas, 8% positivos)? |
|---|---|---|
| Undersampling | Reduce la clase mayoritaria | Descartaría ~258K filas — pérdida de señal grande, usar con cautela o combinarlo. |
| Oversampling simple | Duplica la clase minoritaria | Riesgo de overfitting por filas idénticas repetidas. |
| SMOTE (`imblearn.over_sampling.SMOTE`) | Sintetiza ejemplos minoritarios por interpolación | Buena opción — genera diversidad sin descartar mayoría. Requiere que las categóricas ya estén codificadas (paso 6 antes que este). |
| Class Weights (`class_weight='balanced'`) | No toca los datos, pondera la función de pérdida | Alternativa sin alterar el dataset — útil para comparar contra SMOTE. |

## Anexo 4 — Ranking de atributos, técnicas Filter candidatas (clasificación binaria)

Implementar **al menos 3** de estos indicadores (`sklearn.feature_selection`):

- `f_classif` (ANOVA F-value) — numéricas vs. target categórico.
- `mutual_info_classif` (Información Mutua) — captura relaciones no
  lineales, aplica a numéricas y categóricas codificadas.
- `chi2` — requiere valores no negativos (categóricas codificadas o
  numéricas escaladas a [0, ∞)).
- Correlación de Pearson/Spearman de cada atributo numérico con `TARGET`.

Para el informe: `f_classif`/`chi2`/correlación son específicos de
clasificación (comparan contra una clase discreta); sus análogos para
regresión son `f_regression`/correlación con un target continuo. La
mayoría de estos filtros Filter evalúan **una variable a la vez contra un
único target discreto** — aplican de forma nativa a problemas
**binarios y multiclass** (ej. `f_classif`/`mutual_info_classif` soportan
más de 2 clases de forma nativa en scikit-learn), pero **no** a
multilabel/multitarget sin adaptación (hay que correr el filtro por cada
target por separado, o usar variantes multi-output específicas) —
confirmar y explicitar esto en el informe, es una pregunta directa del
Anexo 4.

---

## Estructura obligatoria del informe final

El entregable es **un solo PDF**. Estructura de secciones exacta pedida
por `hw02-2026-2.docx` (cada sección inicia en hoja nueva):

1. Título, autor y filiación (apellidos, nombres, fecha)
2. Resumen
3. Introducción
4. El problema (en palabras propias)
5. Metodología de trabajo (expone **cómo** se resolverá, sin resolver aún
   — diagramas, fórmulas, conceptos)
6. Desarrollo de la solución (cada paso del procedimiento de arriba, con
   código, explicación de cada parámetro, resultados y conclusión parcial)
7. Resultados
8. Conclusiones (con indicadores numéricos — **no opiniones**)
9. Recomendaciones (mínimo 3, con análisis de viabilidad, para trabajo
   futuro)
10. Referencias usadas (solo lo efectivamente citado — incluir como mínimo
    Kuhn & Johnson 2019 y He & Ma 2013 si se citan en el texto)
11. Código fuente completo

## Formato IEEE (tipografía y maquetación exacta — no negociable)

El informe se maqueta con el **formato estándar IEEE para artículos de
conferencia/journal** (plantilla oficial IEEE, no un documento libre con
"código en Courier"). Reglas exactas:

**Página y cuerpo del texto:**
- Tamaño de página: Carta (Letter, 8.5"×11") o A4, según la plantilla.
- **Texto en dos columnas** (doble columna — no confundir con doble cara).
- Cuerpo del texto: **Times New Roman, 10 pt**.
- Márgenes ≈ 0.75" (19 mm) a los lados, según la plantilla oficial.
- Título: centrado, **24 pt**.
- Autores y afiliaciones: debajo del título, centrados.

**Tablas** (numeración romana, distinta de las figuras):
- Se numeran con **números romanos**: Tabla I, Tabla II, Tabla III...
- Título **arriba** de la tabla, centrado, en **versalitas** (small caps).
  Ejemplo: `TABLA I. RESULTADOS DEL MODELO`.
- Se referencian en el texto como *"como se muestra en la Tabla I"*.
- Ajustar al ancho de una columna cuando sea posible; si son muy anchas,
  pueden extenderse a las dos columnas.

**Figuras** (numeración arábiga, distinta de las tablas):
- Se numeran con **números arábigos**: Fig. 1, Fig. 2, Fig. 3...
- Título/leyenda **debajo** de la imagen, centrado. Ejemplo:
  `Fig. 1. Distribución de la variable TARGET.`
- Se referencian en el texto como *"como se observa en la Fig. 1"*.
- **Mínimo 300 dpi** — exportar cada gráfico de matplotlib/seaborn con
  `dpi=300` explícito, nunca capturas de pantalla.
- Preferir que quepan en una sola columna; si hace falta, pueden ocupar el
  ancho completo de la página (a doble columna, ej. `figure*` en LaTeX).

**Código fuente — la única sección que rompe el layout de dos columnas:**
- Indentado, legible por humanos, espaciado simple.
- Fuente **Courier New, 8 pt**, **a una sola columna** (ancho completo de
  página, no encajado en una columna IEEE normal).
- Cuidado con el tamaño de los tabs — indentación uniforme.
- **Cada función o método encuadrado en un recuadro independiente.**
- El programa principal debe llamarse **`mainProcess`**.

**Decisión de diseño (confirmada con el usuario): el código es orientado a
objetos, no funciones sueltas.** El enunciado dice literalmente "encuadre
cada función **o método**" — la palabra "método" solo tiene sentido si hay
una clase. Diseño elegido: **una clase orquestadora única**,
`HomeCreditAnalysis`, con **un método por paso** del procedimiento
(`cargar_datos`, `describir_dataset`, `analizar_target`, y los pasos 4-8
como métodos nuevos de esa misma clase a medida que se desarrollan — no
se crean clases separadas por paso). El estado (`self.df` y los
resultados intermedios de cada paso) se guarda como atributos de
instancia para que los métodos posteriores lo reutilicen sin pasarlo por
parámetro. `mainProcess()` instancia la clase y llama a sus métodos en
orden. Esta clase vive en los tres artefactos (notebook, `main.tex`,
`build_docx.py`) — al agregar un método nuevo hay que agregarlo en los
tres (ver más abajo, "tres artefactos separados").

**Otros requisitos (de `hw02-2026-2.docx`, siguen vigentes):**
- Numerar también los *procedimientos* (pasos del método), no solo tablas
  y figuras.
- Incluir `hw02-2026-2.docx` como parte del informe (anexo).
- Nombre del archivo final: **`t2_2026-1_mondragon.contreras.sergio.pdf`**
  (Sergio Mondragon Contreras — solo un nombre de pila dado, el patrón
  `nombre1.nombre2` se reduce a `nombre1` si no hay segundo nombre;
  avisar si en realidad sí hay un segundo nombre para corregirlo).

**Implicación práctica para la herramienta de armado:** un documento libre
en Word con estilos manuales no reproduce de forma confiable la maqueta
IEEE (columnas, numeración romana/arábiga separada, versalitas). La forma
fiable de cumplir esto es partir de la **plantilla oficial IEEE**
(disponible en Word `.docx` y en LaTeX, clase `IEEEtran`) — ver decisión
de herramienta más abajo.

---

## Stack técnico propuesto

| Capa | Herramienta |
|------|-------------|
| Entorno | Python ≥ 3.11, Jupyter Notebook |
| Datos | pandas, numpy |
| Visualización | matplotlib, seaborn |
| Codificación categóricas | scikit-learn (`LabelEncoder`, `OneHotEncoder`, `OrdinalEncoder`), `category_encoders` (Binary/Hash) |
| Balanceo | `imbalanced-learn` (SMOTE), `scikit-learn` (`class_weight`) |
| Ranking / selección de atributos | `sklearn.feature_selection` (`f_classif`, `mutual_info_classif`, `chi2`) |
| Reducción dimensional | `sklearn.decomposition.PCA` |
| Maquetación/exportación a PDF | **Decidido: LaTeX, clase `IEEEtran`** (ver "Formato IEEE" arriba). Esqueleto ya creado en `report/main.tex` (secciones mapeadas 1:1 contra la estructura de `hw02-2026-2.docx`, bloque `strip` + `listings` configurado para el código en Courier 8pt de una sola columna, bibliografía con las 2 referencias exigidas). No hay distribución LaTeX instalada localmente (`pdflatex`/`xelatex` no encontrados) — compilar en **Overleaf** (trae `IEEEtran` en su galería, cero instalación) o instalar MiKTeX/TeX Live local si se prefiere. El notebook produce el contenido (código final, gráficos a 300dpi con `dpi=300`, tablas); `report/main.tex` los ensambla. |

## Estructura de carpetas propuesta

```
tarea2/
├── AGENTS.md                                    # este documento
├── CHANGELOG.md
├── hw02-2026-2.docx                              # enunciado — fuente normativa
├── HomeCreditDefault.docx                        # contexto Kaggle — apoyo
├── home-credit-default-risk-extensive-eda.ipynb   # referencia de estilo EDA — apoyo parcial
├── data/                                          # CSVs originales (ya presentes)
├── notebooks/
│   └── tarea2_home_credit.ipynb                   # desarrollo principal (todo el procedimiento)
├── output/
│   ├── figures/                                   # gráficos exportados y numerados
│   └── tables/                                    # tablas exportadas (ranking, missing_data, etc.)
└── report/
    ├── main.tex                                    # documento IEEEtran — fuente principal (editable a mano)
    ├── main.pdf                                     # PDF compilado desde main.tex — entregable final
    ├── build_docx.py                                # genera main.docx a partir del mismo contenido
    └── main.docx                                    # version Word (formato IEEE aproximado — ver mas abajo)
```

**Sobre `main.docx`:** es una **versión de conveniencia**, no la fuente de
verdad. `main.tex` sigue siendo el documento IEEE fiel (columnas exactas,
numeración automática, versalitas nativas de `IEEEtran`). Word no expone
un layout IEEE nativo, así que `build_docx.py` lo aproxima a mano
(secciones de 2/1 columnas vía XML de bajo nivel, tablas con "TABLE I/II"
tipeado literal, código en Courier 8pt con bordes de párrafo simulando el
recuadro). **`main.docx` no se edita a mano** — es un binario; cualquier
cambio de contenido se hace en `build_docx.py` (reflejando el mismo
contenido que ya se porta a `main.tex`) y se regenera con
`uv run python report/build_docx.py`.

---

## Notas de riesgo / verificar antes de dar por cerrado un paso

- `bureau_balance.csv` (359MB, 27,3M filas) y `installments_payments.csv`
  (690MB) son pesados — como el alcance es solo `application_train.csv`,
  no debieran cargarse nunca en esta tarea; si en algún punto el desarrollo
  los necesita, es señal de que el alcance se salió de lo pedido.
- El valor centinela `365243` en `DAYS_EMPLOYED` (visible en el notebook
  de referencia) representa "no realista" (probablemente jubilados/sin
  empleo codificados así) — tratarlo explícitamente como outlier/valor
  especial en el paso de Limpieza, no dejarlo pasar sin mención.
- Verificar la versión de `scikit-learn` instalada antes de usar
  `mutual_info_classif`/`f_classif` con columnas categóricas sin codificar
  — todas deben estar numéricas para `sklearn.feature_selection`.
- El PCA (paso 8) requiere escalar los atributos primero
  (`StandardScaler`) — el enunciado no lo menciona explícitamente pero es
  un requisito técnico ineludible de PCA, mencionarlo en el informe como
  parte de la metodología.
- **Confirmado en ejecución real:** la versión de pandas instalada en este
  proyecto (3.0.5) reporta las columnas de texto con dtype `str`, no
  `object` como en pandas 1.x/2.x. Código que compare
  `df.dtypes == 'object'` o haga `dtype_counts.get('object', 0)` da
  resultados silenciosamente incorrectos (cuenta 0 categóricas en vez de
  16) — sumar/incluir siempre ambas etiquetas (`object` y `str`), o usar
  `df.select_dtypes(exclude=[np.number])` en vez de comparar por nombre de
  dtype literal.
- **`notebooks/tarea2_home_credit.ipynb`, `report/main.tex` y
  `report/build_docx.py` son TRES artefactos separados — nada se
  sincroniza automáticamente entre ellos.** Terminar un paso en el
  notebook (código ejecutado, tabla y figura exportadas) no actualiza ni
  el informe LaTeX ni el Word por sí solo. Cada vez que se cierre un paso
  del procedimiento en el notebook, hay que **portar manualmente el mismo
  contenido a los otros dos**:
  - `report/main.tex`: texto explicativo, `\begin{table}`/`\begin{figure}`
    con numeración romana/arábiga, función nueva en su propio
    `\lstlisting` dentro de `mainProcess`. Recompilar con `pdflatex` dos
    veces (referencias cruzadas) y **verificar visualmente** rasterizando
    con `pdftoppm -png`.
  - `report/build_docx.py`: los mismos párrafos/tabla/figura/función,
    agregados con los helpers del script (`add_body_paragraph`,
    `add_table_ieee`, `add_figure_ieee`, `add_code_function`). Regenerar
    con `uv run python report/build_docx.py` y **verificar visualmente**
    (ej. convirtiendo a PDF con LibreOffice headless:
    `soffice --headless --convert-to pdf main.docx` y rasterizando con
    `pdftoppm -png`, igual que con el `.tex`).
    **⚠️ Al convertir para verificar, usar siempre `--outdir <ruta fuera
    de `report/`>`** — `soffice --convert-to pdf` sin `--outdir` escribe
    `main.pdf` en el mismo directorio que `main.docx`, que es exactamente
    el nombre del PDF real compilado desde `main.tex` (ya pasó una vez:
    se sobrescribió el PDF bueno con la conversión del Word y hubo que
    recompilar `main.tex` para restaurarlo). Nunca usar `main.docx` en
    `report/` como origen de una conversión sin `--outdir` apuntando
    fuera de esa carpeta.
    **Tablas de `add_table_ieee` requieren `col_widths_in` explícito** —
    sin ancho de columna fijo, Word/LibreOffice autoajustan y pueden
    colapsar u ocultar columnas de contenido corto (bug real observado:
    la Tabla I perdió la columna numérica por completo). El helper ya
    fija `w:tblLayout type="fixed"`, pero cada llamada nueva a
    `add_table_ieee` debe pasar `col_widths_in` sumando ~3.2in (ancho
    disponible por columna en el layout de 2 columnas).

  En ningún caso basta con que la compilación/generación no tire errores
  — hay que confirmar que el contenido nuevo realmente aparece en el
  documento final, en los dos formatos.

---

## Referencias citadas por el enunciado

- Kuhn, M., & Johnson, K. (2019). *Feature Engineering and Selection: A
  Practical Approach for Predictive Models*. Chapman & Hall/CRC Press.
- He, H., & Ma, Y. (2013). *Imbalanced Learning: Foundations, Algorithms
  and Applications*.
