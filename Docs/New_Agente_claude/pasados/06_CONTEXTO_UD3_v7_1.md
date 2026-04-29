# CONTEXTO_UD3 · Ecosistema Hadoop (Pig + Spark + Hive)

> **Versión:** v7.1 (4 plantillas operativas para UD3 + bloque 7.7 actualizado a `dlcdn.apache.org` con verificación de descarga)
> **Cómo usar este documento:** pégalo en una celda markdown del cuaderno. Quédate en el cuaderno como referencia permanente para el agente. **No la borres ni la escondas.**

> **Cambios respecto a v6:**
> - **Sección 0 ampliada:** ahora hay **4 plantillas operativas** para UD3 (instalación Pig, corrección Pig, top palabras Pig, Spark+Hive+HQL).
> - Todas las plantillas Pig nuevas siguen las reglas duras del v6 (clase negada `[^0-9.-]`, `ORDER → STORE → !head`, stopwords inline con `!=`, `chararray` por defecto, no `LIMIT` directo tras `ORDER BY`).

> **Cambios v6 respecto a v5 (mantenidos):**
> - Reestructurado a 10 secciones canónicas del sistema v9.
> - Jerarquía actualizada con PLAN_TAREA, CONTEXTO_UD1/UD2.
> - Convención `input/` (Pig local) vs `dataset/` (HDFS UD2) aclarada.

> **Información del enunciado concreto** (qué dataset usar, qué palabra buscar, qué columnas concretas, qué consultas HQL específicas) **vive en PLAN_TAREA**, no aquí.

---

## ÍNDICE

0. [Plantillas operativas disponibles](#0-plantillas-operativas-disponibles)
1. [Qué se evalúa en UD3](#1-qué-se-evalúa-en-ud3)
2. [Qué manda en caso de duda](#2-qué-manda-en-caso-de-duda)
3. [Mapa conceptual de los apartados típicos de UD3](#3-mapa-conceptual-de-los-apartados-típicos-de-ud3)
4. [Reglas semánticas críticas por apartado](#4-reglas-semánticas-críticas-por-apartado)
5. [Decisiones que el agente NO puede tomar solo](#5-decisiones-que-el-agente-no-puede-tomar-solo)
6. [Pipeline técnico obligatorio de UD3](#6-pipeline-técnico-obligatorio-de-ud3)
7. [Bloques operativos mínimos de referencia](#7-bloques-operativos-mínimos-de-referencia)
8. [Señales de que un apartado está bien resuelto](#8-señales-de-que-un-apartado-está-bien-resuelto)
9. [Señales de que el agente se está equivocando](#9-señales-de-que-el-agente-se-está-equivocando)

---

## 0. Plantillas operativas disponibles

UD3 tiene **cuatro plantillas operativas** disponibles como archivos `.md`. **No están pre-cargadas en el cuaderno**: el usuario las inyecta por chat cuando el agente las pide.

| Nombre del archivo | Para qué apartado | Qué contiene |
|---|---|---|
| `plantilla_05_instalacion_pig.md` | Antes de empezar la fase Pig (apartado 3) | Instala Pig 0.17.0 y configura `PIG_HOME`, `PATH`, `PIG_CLASSPATH` |
| `plantilla_06_pig_correccion.md` | Apartado 3.1 (corrección de fallos con Pig) | Esquema chararray + filtro de cabecera + `(double) REPLACE(col, '[^0-9.-]', '')` para importes + imputación de nulos. STORE en `salida_limpios/` |
| `plantilla_07_pig_top_palabras.md` | Apartado 3.2 (análisis con Pig) | TOKENIZE + LOWER + UNION + stopwords inline + GROUP + COUNT + ORDER → STORE → `!head` |
| `plantilla_08_spark_hive_consultas.md` | Apartados 4 y 5 (Spark + Hive + HQL) | Instalación Spark + SparkSession con Hive + limpieza Derby + CREATE DATABASE + saveAsTable + validación + 2 consultas HQL con JOIN |

**Nota:** la instalación de Hadoop (`plantilla_01_instalacion_hadoop.md`, de UD2) puede ser requisito previo si el examen mezcla UD2+UD3.

### Cómo trabaja el agente con cada plantilla

Al llegar a un apartado de UD3:

1. **Identifica** qué plantilla corresponde según la tabla anterior.
2. **Comprueba** si la plantilla ya se la pasaron por chat en esta sesión o si ya está ejecutada en el cuaderno.
3. **Si no la tiene, la pide al usuario explícitamente** antes de proponer el bloque:
   > *"Para este apartado necesito la `plantilla_06_pig_correccion.md`. ¿Me la pasas por chat?"*
4. **Una vez recibida**, sigue las reglas de adaptación específicas de cada plantilla. Las plantillas Pig (06, 07) usan `%%writefile` para crear scripts `.pig` adaptados; la 08 (Spark+Hive) crea celdas Python con código adaptado.

### Reglas duras sobre las plantillas

- **No regenerar.** Si el usuario pasa una plantilla y el agente cree que está incompleta o tiene errores, **avisa al usuario y se detiene**.
- **No duplicar.** Si una plantilla ya se inyectó al cuaderno, no la vuelve a escribir; modifica las celdas existentes si hace falta.
- **Respetar las reglas duras de cada plantilla** (no aplicar adaptaciones en zonas marcadas como "qué NO cambiar"). Si una adaptación parece necesaria pero rompe esas reglas, consultar al usuario antes.
- **No mezclar** con instalaciones alternativas (`!apt install pig`, `!pip install pyspark`). Solo los métodos de las plantillas.
- **Las plantillas Pig (06, 07) requieren ejecutar antes el bloque 7.2** (mapeo Pandas → Pig) para conocer los esquemas reales de los CSVs.
- **La plantilla 08 (Spark) requiere ejecutar antes la limpieza Derby** (incluida en su celda de código 3) **antes** del primer `spark.sql` Hive.

---

## 1. Qué se evalúa en UD3

UD3 evalúa resolver un **pipeline completo con varias herramientas integradas**:

**Pandas → Pig → Spark → Hive → HQL**

Lo que cuenta de verdad:

1. importar o cargar el dataset de forma reproducible,
2. revisar calidad con Pandas,
3. corregir al menos **2 problemas** con Pig,
4. hacer una transformación útil con Pig (la que pida el enunciado),
5. cargar tablas con Spark en Hive,
6. lanzar al menos **2 consultas HQL** que impliquen **2 tablas**,
7. validar resultados e interpretarlos.

### Herramientas núcleo y su papel

- **Pandas**: inspección rápida, detección de problemas, generación del esquema Pig.
- **Pig**: limpieza y transformación ETL (conservador con columnas sucias).
- **PySpark**: lectura, tipado de lo que quedó como texto en Pig, carga a tablas Hive.
- **Hive / HQL**: consultas finales.

UD3 **no es ETL en pandas**, es trabajo distribuido real con Pig + Spark + Hive.

---

## 2. Qué manda en caso de duda

1. **enunciado oficial** del ejercicio actual,
2. **PLAN_TAREA** (lectura concreta del enunciado y estado de avance),
3. **este contexto de unidad** (reglas conceptuales de UD3),
4. **CONTEXTO_UD1 / CONTEXTO_UD2** si el examen mezcla unidades,
5. **plantillas operativas** (sección 0, inyectadas por chat),
6. **tareas resueltas y aprobadas** (referencia, no copia),
7. cuadernos de ejemplo del profesor,
8. teoría general.

Si una definición genérica contradice el patrón operativo de UD3, **manda el patrón de UD3**.

No reinterpretar un apartado por intuición.

---

## 3. Mapa conceptual de los apartados típicos de UD3

| Apartado típico | Tema | Lo crítico |
|---|---|---|
| Explicación del dataset | Contexto + temática | Volumen, campos, justificación analítica (suele ser el dataset de UD1) |
| Importación reproducible | Carga automática | Kaggle, URL, Drive, ZIP — lo que pida el enunciado, no inventar |
| Visualización con Pandas | Detección de problemas | head/info/shape/isnull/dtypes; salida como insumo para mapeo Pig |
| Pig · Corrección de problemas | Limpieza | Al menos 2 problemas corregidos. Conservador con columnas sucias |
| Pig · Transformación analítica | Análisis útil | Ranking, conteo, top N, JOIN — lo que pida literalmente el enunciado |
| Spark · Carga a Hive | Tipar y guardar | `findspark` + `enableHiveSupport()` + `CREATE DATABASE` + `saveAsTable` |
| HQL · Consultas finales | Explotación | ≥ 2 consultas, al menos una con 2 tablas. Validación previa obligatoria |

> **Numeración real del enunciado:** los apartados 1-5 del enunciado oficial de UD3 agrupan estos temas (1: explicación, 2: importación + Pandas, 3: Pig (subdividido en corrección y transformación), 4: Spark+Hive, 5: HQL). En examen multi-unidad, la numeración real la fija PLAN_TAREA.

---

## 4. Reglas semánticas críticas por apartado

Estas son las interpretaciones obligatorias en UD3. Si el agente quiere desviarse de alguna, debe consultar antes.

### Convención común a todos los apartados de UD3

- **Modo Pig en Colab:** `!pig script.pig` **sin** `-x local`. Las rutas son **relativas** al CWD de Colab.
- **Carpeta de entrada local:** `input/` (relativa al CWD). No confundir con `dataset/` de UD2 (que es HDFS de Hadoop): son contextos distintos.
- **Carpetas de salida Pig:** nombre **descriptivo**, prefijo `salida_`. Ejemplos: `salida_limpios`, `salida_filtrados`, `salida_top_palabras`, `salida_join`. **Nunca** nombres genéricos (`output`, `tmp`, `data`).
- **Pig no sobrescribe.** Antes de cada `STORE`, borrar la carpeta de salida con `!rm -rf <carpeta>`.

### Apartado: Importación reproducible 🚨
**Significa:** dejar el dataset accesible en `input/` de forma reproducible, usando el método que **mande el enunciado** (Kaggle, Drive, URL, ZIP, etc.).

**No es:**
- asumir Kaggle por defecto si el enunciado no lo pide,
- copiar a mano archivos sin código que lo reproduzca,
- saltarse la verificación con `!ls input/` y `!head -1 input/<archivo>`.

**Patrón típico:**
1. detectar cómo llega el dataset (PLAN_TAREA),
2. dejarlo en `input/`,
3. verificar con `!ls input/` y `!head -1 input/<archivo>`.

### Apartado: Visualización con Pandas 🚨
**Significa:** revisar calidad con `head`, `info`, `shape`, `isnull`, `dtypes`. **El output sirve como insumo del mapeo Pandas → Pig** (ver 4 / Mapeo).

**No es:**
- explorar sin documentar problemas detectados,
- limpiar en Pandas lo que toca limpiar en Pig.

### Apartado: Mapeo Pandas → Pig 🚨 obligatorio
**Significa:** antes de escribir el primer `LOAD` Pig, generar el esquema a partir de los `dtypes` reales de Pandas. Ver bloque 7.2.

**Mapeo cerrado:**
```
int64            → long
float64          → double
object           → chararray
bool             → boolean
datetime64[ns]   → datetime
```

**Regla dura:**
- **Nunca** escribir un `LOAD` sin haber ejecutado antes el bloque de mapeo sobre ese CSV.
- El esquema del `LOAD` debe **coincidir exactamente** con la salida del mapeo.
- Si una columna sale `object` en el mapeo, en Pig es `chararray`. **No se "corrige" a numérico dentro de Pig.**

### Apartado: Pig · Corrección de problemas 🚨
**Significa:** corregir al menos **2 problemas** detectados en Pandas (nulos, vacíos, fechas mal formateadas, etc.) usando Pig.

**No es:**
- limpiar en Pandas y luego usar Pig "para cumplir el expediente",
- castear columnas sucias a numérico dentro de Pig (eso es Spark).

**Estrategia conservadora con columnas sucias:**

Una columna **"sucia"** es la que en Pandas aparece como `object` (texto) aunque conceptualmente sea numérica o fecha. Típicamente: importes con símbolos (`$`, `€`), separadores de miles, mezcla con `NaN` textual, fechas en formatos mixtos.

**Por defecto:** la columna se declara `chararray` en Pig y se propaga tal cual. La limpieza numérica o de fechas se hace **en Spark**.

Motivo operativo:
- Pig 0.17 no tiene funciones de limpieza robustas para estos casos.
- `(double) REPLACE(TRIM(col), ',', '')` falla silenciosamente sobre miles de filas.
- Spark tiene `regexp_replace`, `to_date`, `cast(...)` y hace el mismo trabajo sin warnings.

**Prohibido como norma general dentro de Pig:**
- `(float) REPLACE(...)` o `(double) REPLACE(...)` sobre columnas con símbolos,
- `CASE WHEN ... THEN (float)... END` para limpiar columnas sucias,
- `REPLACE(col, '$', '')` — en regex Pig, `$` significa end-of-string, no el carácter literal; **deja la columna vacía sobre miles de filas sin warning**.
- `REGEX_EXTRACT(col, '([0-9]+)', 1)` con patrones **no anclados** sobre cadenas con prefijo (`$`, `€`): Pig exige match completo; si falla, devuelve `null` silencioso, el cast a `double` no falla (cast de null = null), y la columna sale vacía.

**Excepción controlada (solo si el enunciado obliga a tener la columna numérica ya tras Pig):** el único patrón verificado en Pig 0.17 es la **clase negada**:

```pig
(double) REPLACE(<col_sucia>, '[^0-9.-]', '') AS <col_sucia>
```

Por qué funciona:
- `[^0-9.-]` = clase negada: "cualquier carácter que NO sea dígito, punto o menos".
- Dentro de `[...]`, el `$` pierde su significado especial.
- Sin `$` literal ni `\` en el patrón → no hay problemas de escape entre `%%writefile`, Jupyter, Colab y Pig.
- Cubre `$-77.00` → `-77.00`, `$14.57` → `14.57`, `$1,234.56` → `1234.56`.

Comprobación obligatoria tras ejecutar:
1. `!head -n 10 salida_<qué_es>/part-*` — verificar visualmente que la columna sale con valores reales (no vacía).
2. Contador `FIELD_DISCARDED_TYPE_CONVERSION_FAILED` — aceptable si es pequeño; inaceptable si se acerca al total de filas.

**Regla simple:** si dudas de si la columna es limpia, déjala `chararray` y arregla en Spark.

### Apartado: Pig · Transformación analítica 🚨
**Significa:** hacer **literalmente** el análisis que pida el enunciado (top N palabras, top N categorías, ranking, JOIN, etc.).

**No es:**
- sustituir el análisis pedido por otro "más interesante" sin consultar,
- dar el apartado por hecho con un análisis parcial,
- improvisar sintaxis Pig que parece lógica pero no existe en 0.17.

**Patrón TOP N (Pig 0.17 — bug conocido):** `ORDER BY ... DESC` seguido directamente de `LIMIT` y `STORE`/`DUMP` puede fallar en Pig 0.17 sobre MapReduce con `ERROR 2244: Job failed, hadoop does not return any error message`.

**Patrón preferido (orden DESC garantizado):**
```pig
ordenado = ORDER <relacion_contada> BY <campo_conteo> DESC;
STORE ordenado INTO 'salida_<algo>_ordenado';
```

```bash
!head -n <N> salida_<algo>_ordenado/part-*
```

`head` lee secuencialmente desde el inicio del `part-*`, así que las N primeras líneas son las N primeras filas ordenadas DESC.

**Patrón alternativo 100% Pig** (solo si se necesita encadenar con otro paso Pig posterior): ver bloque 7.6. **Pero la salida puede quedar desordenada** — no usarlo si se exige orden DESC en el informe.

**Sintaxis Pig prohibida (no existe en 0.17):**
- `DEFINE STOPWORDS_EN (group ('a','an','the') as (word:chararray))` → error de parser.
- `LOAD 'archivo.txt' USING InlineStorage(...)` → no existe.
- `FILTER x BY col IN ('a','b','c')` → no hay `IN` en 0.17. Usar encadenamiento de desigualdades o JOIN/COGROUP contra una relación.

**Regla dura anti-sintaxis inventada:**
- Si el agente no está 100% seguro de que una construcción existe en Pig 0.17, **no la usa**.
- No se inventan UDF ni `DEFINE` con sintaxis no estándar.
- No se asumen archivos externos (`stopwords.txt`, etc.) si no se han creado explícitamente con `%%writefile`.

### Apartado: Spark · Carga a Hive 🚨
**Significa:** instalar Spark, leer los archivos producidos por Pig, tipar las columnas que quedaron como `chararray`, y guardar como tablas Hive.

**Instalación obligatoria de Spark:**
- **Prohibido** `!pip install pyspark` (trae su propio Hadoop y rompe la coherencia con el Hadoop 3.4.2 manual de UD2/UD3).
- Patrón obligatorio: descarga del binario `spark-3.5.x-bin-hadoop3.tgz` desde `dlcdn.apache.org` (CDN oficial actual) + verificación de tamaño + `findspark` + variables de entorno (ver bloque 7.7).

**Limpieza obligatoria del metastore Derby antes del primer `spark.sql` Hive:**

Spark con `enableHiveSupport()` en Colab usa un metastore embebido **Derby** en el CWD. Si una sesión anterior dejó `metastore_db/` con locks, `CREATE DATABASE` o `saveAsTable` fallan con `Failed to start database 'metastore_db'`.

```python
import os, shutil
for p in ("metastore_db", "derby.log"):
    if os.path.exists(p):
        shutil.rmtree(p) if os.path.isdir(p) else os.remove(p)
```

Es **idempotente** y se ejecuta **antes** del primer `spark.sql` Hive.

**`CREATE DATABASE` + `USE <base>` explícitos:** el enunciado pide "base de datos Hive". No vale guardar tablas en `default`.

**Validación obligatoria entre `saveAsTable` y consultas HQL** (ver bloque 7.9): `DESCRIBE` + `COUNT(*)` + `SELECT * LIMIT 3` por cada tabla.

### Apartado: HQL · Consultas finales 🚨
**Significa:** lanzar al menos **2 consultas HQL**, donde al menos una use **2 tablas**.

**No es:**
- consultas triviales que no responden a una pregunta analítica real,
- consultas sobre tablas no validadas,
- joins por columnas con nombres parecidos pero sentido distinto.

**Patrón:**
- una consulta simple (GROUP BY + ORDER BY + LIMIT),
- una consulta con JOIN entre 2 tablas.

---

## 5. Decisiones que el agente NO puede tomar solo

Detente y consulta antes de:

### Sobre el flujo general
- saltarse Pig si el enunciado lo pide,
- cargar a Hive sin haber pasado por Pandas + Pig,
- sustituir un apartado por otro "equivalente",
- saltarse o reordenar pasos del pipeline.

### Sobre el mapeo Pandas → Pig
- escribir un `LOAD` Pig sin haber generado antes el esquema desde los dtypes reales,
- inventar tipos en el `LOAD`,
- "aproximar" el tipo de una columna por intuición.

### Sobre Pig
- castear dentro de Pig columnas que Pandas reportó como `object` con símbolos (es Spark, ver §8 estrategia conservadora),
- usar `pig -x local` cuando el modo de referencia es `!pig` sin `-x local`,
- usar rutas absolutas (`/input/...`) en scripts Pig,
- mezclar rutas locales y HDFS en el mismo bloque,
- hacer `LIMIT` directo tras `ORDER BY` sin STORE intermedio,
- hacer `DUMP` masivo sin `LIMIT` previo,
- reintentar una celda Pig tras un error o warning masivo,
- inventar sintaxis Pig (`DEFINE ... group(...)`, `IN (...)`, funciones que no existen),
- usar archivos externos no creados explícitamente con `%%writefile`,
- aplicar el patrón de limpieza con clase negada (excepción §4 corrección) por iniciativa: solo se usa si el enunciado obliga.

### Sobre Spark / Hive
- instalar Spark con `!pip install pyspark`,
- arrancar Spark sin fijar `JAVA_HOME` y `SPARK_HOME`,
- saltar la limpieza de `metastore_db/` + `derby.log` antes del primer `spark.sql` Hive,
- guardar tablas sin crear antes la base de datos (`CREATE DATABASE` + `USE <base>`),
- pasar de `saveAsTable` a consultas HQL sin validación intermedia (`DESCRIBE` + `COUNT` + `LIMIT 3`).

### Sobre HQL
- lanzar consultas sobre tablas no validadas,
- hacer joins por columnas con nombres parecidos pero sentido distinto,
- dar el apartado por cumplido sin haber lanzado al menos una consulta con 2 tablas.

### Sobre fidelidad al enunciado
- sustituir un análisis pedido (top 3 palabras, ranking) por otro parecido,
- considerar un sub-punto resuelto si solo se ha hecho parte,
- interpretar "por ejemplo, encontrar las 3 palabras más repetidas" como invitación libre a inventar otro análisis: **es un ejemplo, no una invitación libre**. Si el agente quiere proponer otro análisis distinto, lo declara en chat y pide OK antes de codificar.

---

## 6. Pipeline técnico obligatorio de UD3

Este orden no se puede saltar. Cada paso debe ejecutarse y verificarse antes del siguiente.

| Paso | Qué se hace | Plantilla / bloque | Cómo se verifica |
|---|---|---|---|
| 1 | Entender el dataset (cuántos archivos, qué representa cada uno, claves entre tablas) | — | celda markdown explicativa |
| 2 | Importar y dejar archivos en `input/` | (depende del enunciado) | `!ls input/` + `!head -1 input/<archivo>` |
| 3 | Revisar con Pandas | bloque 7.1 | head/info/shape/isnull |
| 4 | Mapear dtypes Pandas → tipos Pig | bloque 7.2 | esquema impreso para cada CSV |
| 5 | Detectar problemas concretos a corregir | — | lista en celda markdown o chat |
| 6 | Instalar Pig | `plantilla_05_instalacion_pig.md` | `!pig -h -version` |
| 7a | Pig · Corrección de fallos (apartado 3.1) | `plantilla_06_pig_correccion.md` | `!head salida_limpios/part-m-*` con datos reales |
| 7b | Pig · Tratamiento de datos / análisis (apartado 3.2) | `plantilla_07_pig_top_palabras.md` o bloques 7.5/7.6 | `!head -n <N> salida_palabras_ordenado/part-*` |
| 8-11 | Spark + Hive + HQL (apartados 4 y 5) | `plantilla_08_spark_hive_consultas.md` | base creada, tablas validadas, 2+ consultas con JOIN |
| 12 | Interpretar y cerrar | — | conclusión breve por apartado |

**Regla:** si un paso falla, no se sigue al siguiente sin haberlo resuelto. El agente no debe "intentar continuar" con un paso roto.

**Regla anti-reintento:** si un script Pig falla o termina con warnings masivos (`FIELD_DISCARDED_TYPE_CONVERSION_FAILED N time(s)`, `ACCESSING_NON_EXISTENT_FIELD`):
1. **no reintentar la misma celda**,
2. **no relanzar con `-v` sin consultar**,
3. volver al chat,
4. describir el error y proponer corrección,
5. esperar confirmación.

Máximo **una** ejecución por celda Pig. No llenar el cuaderno de celdas de reintento, volcado de logs, `pig -v`, relanzamientos.

---

## 7. Bloques operativos mínimos de referencia

> **Importante:** estos bloques son **referencia conceptual con placeholders**. El agente los adapta al dataset y al enunciado reales (consultando PLAN_TAREA), no copiarlos a ciegas. Los nombres concretos de columnas, archivos, claves y consultas vienen siempre de PLAN_TAREA.

### 7.1 Revisar con Pandas
```python
import pandas as pd

df = pd.read_csv("input/<archivo>.csv")
df.head()
df.info()
df.isnull().sum()
print(df.shape)

# Comprobaciones útiles (adaptar columnas)
pd.to_datetime(df["<col_fecha>"], errors="coerce")
pd.to_numeric(df["<col_importe>"], errors="coerce")
```

### 7.2 Mapeo Pandas → Pig (genérico para N archivos)
**Obligatorio antes del primer `LOAD` Pig.**

```python
import os
import pandas as pd

dtype_mapping = {
    "int64":          "long",
    "float64":        "double",
    "object":         "chararray",
    "bool":           "boolean",
    "datetime64[ns]": "datetime",
}

for archivo in sorted(os.listdir("input")):
    if not archivo.lower().endswith(".csv"):
        continue
    df = pd.read_csv(f"input/{archivo}")
    print(f"=== {archivo} ===")
    print(df.dtypes)
    pig_schema = ",\n    ".join(
        f"{col}:{dtype_mapping[str(dt)]}"
        for col, dt in df.dtypes.items()
    )
    print("Esquema Pig:")
    print(pig_schema)
    print()
```

La salida se pega tal cual dentro del `AS (...)` del `LOAD` Pig correspondiente.

### 7.3 Pig · Limpieza de nulos
```pig
-- procesar_nulos.pig
datos = LOAD 'input/<archivo>.csv' USING PigStorage(',') AS (
    <esquema_del_mapeo>
);

datos_clean = FOREACH datos GENERATE
    <col_id>,
    ((<col_texto_1> IS NULL) ? '<valor_por_defecto>' : <col_texto_1>) AS <col_texto_1>,
    ((<col_num_limpia> IS NULL) ? 0 : <col_num_limpia>) AS <col_num_limpia>,
    <resto_columnas>;

STORE datos_clean INTO 'salida_limpios' USING PigStorage(',');
```

```bash
!rm -rf salida_limpios
!pig procesar_nulos.pig
!head salida_limpios/part-*
```

### 7.4 Pig · Filtrado por nulos o por condición
```pig
-- filtrar.pig
datos = LOAD 'input/<archivo>.csv' USING PigStorage(',') AS (
    <esquema_del_mapeo>
);

filtrado = FILTER datos BY NOT (<col_a> IS NULL OR <col_b> IS NULL);
-- o cualquier otra condición: <col> > <umbral>, <col> MATCHES '<regex>', etc.

STORE filtrado INTO 'salida_filtrados' USING PigStorage(',');
```

### 7.5 Pig · TOP N por grupo (patrón preferido)
Patrón general para cualquier ranking por conteo o suma sobre un campo categórico, con orden DESC garantizado.

```pig
-- top_<campo>.pig
datos = LOAD 'input/<archivo>.csv' USING PigStorage(',') AS (
    <esquema_del_mapeo>
);

grupo = GROUP datos BY <campo_agrupacion>;
conteos = FOREACH grupo GENERATE
    group AS <campo_agrupacion>,
    COUNT(datos) AS cnt;

ordenado = ORDER conteos BY cnt DESC;
STORE ordenado INTO 'salida_<campo>_ordenado';
```

```bash
!rm -rf salida_<campo>_ordenado
!pig top_<campo>.pig
!head -n <N> salida_<campo>_ordenado/part-*
```

### 7.6 Pig · Análisis de texto (tokenización + stopwords inline + TOP N)
Patrón general para enunciados que pidan palabras más repetidas sobre una o varias columnas de texto. Vale para 1, 2 o N archivos.

```pig
-- analisis_texto.pig

-- 1. Cargar cada CSV con su esquema real (salida del mapeo)
rel_1 = LOAD 'input/<archivo_1>.csv' USING PigStorage(',') AS (<esquema_1>);
rel_2 = LOAD 'input/<archivo_2>.csv' USING PigStorage(',') AS (<esquema_2>);

-- 2. Proyectar solo las columnas de texto a analizar (mismo alias)
texto_1 = FOREACH rel_1 GENERATE <col_texto_1a> AS texto;
texto_2 = FOREACH rel_2 GENERATE <col_texto_2a> AS texto;

-- 3. Unir todos los textos
texto = UNION texto_1, texto_2;

-- 4. Tokenizar (en minúsculas)
palabras = FOREACH texto GENERATE FLATTEN(TOKENIZE(LOWER(texto))) AS palabra;

-- 5. Filtrar vacíos y stopwords (lista cerrada inline)
palabras_filtradas = FILTER palabras BY
    palabra != ''       AND
    palabra != '<sw_1>' AND palabra != '<sw_2>' AND
    palabra != '<sw_3>' AND palabra != '<sw_4>' AND
    palabra != '<sw_5>' AND palabra != '<sw_6>';

-- 6. Contar
grupo = GROUP palabras_filtradas BY palabra;
conteos = FOREACH grupo GENERATE
    group AS palabra,
    COUNT(palabras_filtradas) AS cnt;

-- 7. ORDER + STORE (TOP N con !head fuera de Pig)
ordenado = ORDER conteos BY cnt DESC;
STORE ordenado INTO 'salida_palabras_ordenado';
```

```bash
!rm -rf salida_palabras_ordenado
!pig analisis_texto.pig
!head -n <N> salida_palabras_ordenado/part-*
```

**Adaptaciones:**
- Con 1 archivo: suprimir `rel_2` / `texto_2` / `UNION`.
- Con N > 2 archivos: añadir `texto_i` y encadenar `UNION texto_1, texto_2, ..., texto_N`.
- Para varias columnas del mismo archivo: concatenar con `CONCAT` antes de `TOKENIZE` (`TOKENIZE(LOWER(CONCAT(<col_a>, ' ', <col_b>)))`).
- Lista de stopwords: inline, sin `DEFINE` raro, sin archivo externo a menos que se cree con `%%writefile`.
- `<N>`: lo que pida el enunciado.

### 7.7 Spark · Instalación + SparkSession con Hive
```python
import os

# Versión de Spark (rama 3.5.x verificada con Java 17).
# Si dlcdn devuelve 404, la versión patch ha avanzado: comprobar con
#   !curl -s https://dlcdn.apache.org/spark/ | grep -oE 'spark-3\.5\.[0-9]+' | sort -u
SPARK_VERSION = "3.5.8"

# Limpiar restos previos
!rm -rf /content/spark-3.5*-bin-hadoop3
!rm -f /content/spark-3.5*-bin-hadoop3.tgz

# Descargar desde CDN oficial (descarga completa garantizada)
!wget https://dlcdn.apache.org/spark/spark-{SPARK_VERSION}/spark-{SPARK_VERSION}-bin-hadoop3.tgz

# Verificación obligatoria: el .tgz pesa ~400 MB
size_mb = os.path.getsize(f"/content/spark-{SPARK_VERSION}-bin-hadoop3.tgz") / 1024 / 1024
assert size_mb > 350, f"Descarga truncada ({size_mb:.1f} MB). Reintentar."

# Extraer y verificar binario
!tar xf /content/spark-{SPARK_VERSION}-bin-hadoop3.tgz -C /content/
assert os.path.exists(f"/content/spark-{SPARK_VERSION}-bin-hadoop3/bin/spark-submit")

!pip install -q findspark

os.environ["JAVA_HOME"]  = "/usr/lib/jvm/java-17-openjdk-amd64"
os.environ["SPARK_HOME"] = f"/content/spark-{SPARK_VERSION}-bin-hadoop3"

import findspark
findspark.init()

from pyspark.sql import SparkSession

spark = (SparkSession.builder
    .appName("BDA_UD3")
    .config("spark.sql.warehouse.dir", "/content/spark-warehouse")
    .enableHiveSupport()
    .getOrCreate())

sc = spark.sparkContext  # útil si el cuaderno del tutor usa sc para RDDs
print("Spark:", spark.version)
```

> **Mirror:** `dlcdn.apache.org` (CDN oficial). Sirve solo la versión actual de cada rama. **No usar `archive.apache.org`** salvo como fallback documentado: trunca descargas con `wget -q` sin avisar (deja `.tgz` con ~100 MB en lugar de ~400 MB → `tar` extrae carpetas vacías sin error visible).
> **Fallback** si `dlcdn` da 404 con la versión actual: `wget --tries=5 --timeout=120 --continue https://archive.apache.org/dist/spark/spark-X.Y.Z/spark-X.Y.Z-bin-hadoop3.tgz`.

### 7.8 Spark · Limpieza del metastore Derby
**Antes** del primer `spark.sql` Hive de la sesión:

```python
import os, shutil

for p in ("metastore_db", "derby.log"):
    if os.path.exists(p):
        shutil.rmtree(p) if os.path.isdir(p) else os.remove(p)
```

Idempotente. Si los ficheros no existen, no pasa nada.

### 7.9 Spark · Leer salida Pig, tipar, crear base y guardar tablas
```python
from pyspark.sql.functions import col, regexp_replace, to_date

# Leer salida de Pig
sdf = spark.read.csv(
    "salida_<qué_es>/part-*",
    header=False,
    inferSchema=False
)
sdf.printSchema()
sdf.show(5, truncate=False)

# Tipar columnas sucias (en Spark, no en Pig)
sdf = sdf.withColumn(
    "<col_sucia>",
    regexp_replace(col("<col_sucia>"), r"[\$€,]", "").cast("double")
)
sdf = sdf.withColumn(
    "<col_fecha>",
    to_date(col("<col_fecha>"), "yyyy-MM-dd")
)

# Crear base de datos explícita (el enunciado pide "base de datos Hive")
spark.sql("CREATE DATABASE IF NOT EXISTS <base>")
spark.sql("USE <base>")

# Guardar tablas
sdf_A.write.mode("overwrite").saveAsTable("<tabla_A>")
sdf_B.write.mode("overwrite").saveAsTable("<tabla_B>")

# Validar
spark.sql("SHOW TABLES IN <base>").show()
```

### 7.10 Validación obligatoria entre carga y consultas HQL
```python
for tabla in ["<tabla_A>", "<tabla_B>", "<tabla_C>"]:
    print(f"\n=== Tabla {tabla} ===")
    print("Esquema:")
    spark.sql(f"DESCRIBE {tabla}").show(truncate=False)
    print("Número de filas:")
    spark.sql(f"SELECT COUNT(*) AS total FROM {tabla}").show()
    print("Primeras 3 filas:")
    spark.sql(f"SELECT * FROM {tabla} LIMIT 3").show(truncate=False)
```

### 7.11 Consultas HQL (1 simple + 1 con JOIN)
```python
# Consulta simple
spark.sql("""
SELECT <col_grupo>, COUNT(*) AS total
FROM <tabla_A>
GROUP BY <col_grupo>
ORDER BY total DESC
LIMIT 10
""").show()

# Consulta con 2 tablas
spark.sql("""
SELECT A.<col_x>, A.<col_y>, B.<col_z>
FROM <tabla_A> A
JOIN <tabla_B> B
  ON A.<clave_A> = B.<clave_B>
LIMIT 20
""").show()
```

### 7.12 Pig · JOIN entre dos relaciones
```pig
-- join.pig
A = LOAD 'input/<archivo_A>.csv' USING PigStorage(',') AS (<esquema_A>);
B = LOAD 'input/<archivo_B>.csv' USING PigStorage(',') AS (<esquema_B>);

AB = JOIN A BY <clave_A>, B BY <clave_B>;

proj = FOREACH AB GENERATE
    A::<col_x>, A::<col_y>, B::<col_z>;

STORE proj INTO 'salida_join' USING PigStorage(',');
```

### 7.13 Pig · TOP N alternativo 100% Pig (solo si hay que encadenar)
Solo si se necesita que el TOP N quede persistido como relación Pig para encadenar con un JOIN posterior dentro del mismo flujo Pig. **La salida puede quedar desordenada.**

```pig
ordenado = ORDER <relacion_contada> BY <campo_conteo> DESC;
STORE ordenado INTO 'salida_<algo>_tmp';

reloaded = LOAD 'salida_<algo>_tmp' USING PigStorage()
           AS (<col1>:<tipo1>, <col2>:<tipo2>);

topN = LIMIT reloaded <N>;
STORE topN INTO 'salida_top_<N>_<algo>';
```

Si el informe exige orden DESC visible, **no usar este patrón**: usar 7.5.

---

## 8. Señales de que un apartado está bien resuelto

- El dataset está bien identificado y la entrada es reproducible.
- Pandas detecta problemas reales (no solo `head` decorativo).
- El esquema Pig se ha generado **con el bloque 7.2**, no inventado.
- Pig se ejecuta con `!pig` sin `-x local`.
- Las rutas Pig son **relativas** y coherentes (`input/`, `salida_...`).
- Los nombres de salida son **explícitos** (`salida_limpios`, `salida_top_palabras`, no `output` ni `tmp`).
- Pig corrige al menos **2 problemas** detectados en Pandas.
- Pig hace **literalmente** la transformación pedida por el enunciado.
- Las columnas "sucias" siguen siendo `chararray` al salir de Pig (excepto excepción §4 controlada).
- El TOP N usa STORE intermedio + `!head` (no `LIMIT` directo tras `ORDER BY`).
- La salida Pig se comprueba con `!head`/`!cat` y tiene datos reales.
- No hay warnings masivos (`FIELD_DISCARDED_TYPE_CONVERSION_FAILED`).
- Spark se instala con `wget .tgz + findspark`, no con `pip install pyspark`.
- `SPARK_HOME` y `JAVA_HOME` están fijados antes de `findspark.init()`.
- Se limpia `metastore_db/` + `derby.log` antes del primer `spark.sql` Hive.
- `CREATE DATABASE` + `USE <base>` antes de `saveAsTable`.
- Hay celda de validación (`DESCRIBE` + `COUNT` + `LIMIT 3`) entre carga y consultas.
- Hay al menos **2 consultas HQL** y al menos una usa **2 tablas**.
- Los resultados se interpretan, no solo se imprimen.
- El agente cita CONTEXTO_UD3 + PLAN_TAREA en cada apartado antes de proponer código.

---

## 9. Señales de que el agente se está equivocando

- Quiere usar una sola herramienta para todo.
- No distingue inspección, limpieza, carga y consulta.
- Salta el mapeo Pandas → Pig y escribe `LOAD` con tipos inventados.
- Usa `int` en Pig cuando Pandas dice `int64` (Pig usa `long`).
- Castea `(double)` o `(float)` dentro de Pig sobre columnas con símbolos.
- Usa `REPLACE(col, '$', '')` (no elimina nada — `$` es end-of-string).
- Usa `REGEX_EXTRACT` no anclado sobre cadenas con prefijos.
- Ignora warnings masivos porque "el job ha acabado".
- Cambia el análisis pedido por otro que le resulta más cómodo.
- Usa `pig -x local` con rutas `/input/...`.
- Pone rutas absolutas con `/` inicial en LOAD/STORE.
- Usa nombres de salida genéricos (`output`, `data`, `tmp`).
- Hace `LIMIT` directo tras `ORDER BY` sin STORE intermedio.
- Reintenta la misma celda Pig tras un error sin consultar.
- Llena el cuaderno de logs y reintentos.
- Inventa sintaxis Pig (`DEFINE ... group(...)`, `IN (...)`, `InlineStorage`).
- Depende de archivos externos (`stopwords.txt`) no creados con `%%writefile` en una celda previa.
- Hace `DUMP` masivo sin `LIMIT` previo.
- Da por buena una salida sin leerla.
- Instala Spark con `!pip install pyspark`.
- Arranca SparkSession sin fijar `JAVA_HOME` y `SPARK_HOME`.
- Mezcla versiones de Spark entre celdas.
- Salta la limpieza del metastore Derby y luego falla con `Failed to start database`.
- Guarda tablas en `default` cuando el enunciado pide "base de datos Hive".
- Pasa de `saveAsTable` a consultas HQL sin validación intermedia.
- Hace joins por columnas con nombres parecidos pero sentido distinto.
- Cargar una tabla y consultar otra.
- Habla del pipeline como teoría sin ejecutarlo realmente.
- Conclusiones con frases tipo *"asegurando la consistencia..."*, *"garantizando la integridad..."* (lo prohíbe la guía de estilo).
