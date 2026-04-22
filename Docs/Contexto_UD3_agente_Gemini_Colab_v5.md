# Contexto_UD3_agente_Gemini_Colab
## UD3 · Ecosistema Hadoop · contexto operativo v5

## 1. Objetivo
Guiar al agente para resolver correctamente **cualquier** variante de tarea o examen práctico de UD3 dentro de Google Colab, independientemente del dataset concreto.

Este documento no es teoría general ni una solución cerrada.
Es una guía operativa para que el agente:

- entienda qué se evalúa realmente en UD3,
- siga el pipeline correcto,
- adapte los bloques al dataset y al enunciado reales,
- no copie una solución anterior sin revisar,
- no cambie el sentido de los apartados por comodidad,
- no se bloquee con Pig por mezclar rutas o modos de ejecución,
- no castee columnas "sucias" dentro de Pig cuando Spark lo hará después,
- y no invente sintaxis Pig por intuición.

Todas las plantillas de este documento están escritas con placeholders (`<archivo>`, `<esquema_del_mapeo>`, `<columna_texto>`, `<tabla_A>`…). Los ejemplos concretos son **ilustrativos**: no asumir sus columnas ni su dataset como propios del examen.

## Cambios respecto a v4
- Se fija el **mapeo Pandas → Pig obligatorio** antes de escribir cualquier LOAD.
- Se añade la **estrategia conservadora para columnas sucias** (chararray en Pig, limpieza numérica en Spark).
- Se añade **regla de fidelidad al enunciado** (no sustituir el análisis pedido por otro).
- Se endurece **nombres de salida explícitos** (`salida_<qué_es>`).
- Se añade **regla anti-sintaxis Pig inventada**.
- Se añade **patrón general de análisis de texto con stopwords inline** (válido para cualquier enunciado que pida tokenizar).
- Se refuerza la regla anti-reintento: también para warnings silenciosos masivos (`FIELD_DISCARDED_TYPE_CONVERSION_FAILED`).
- Todas las plantillas pasan a usar placeholders. Los schemas concretos de tareas anteriores se retiran del contexto general.

### Ajustes post-auditoría UD03_PIG02 (2026-04-22)
- Se añade el **patrón seguro de limpieza de símbolos en Pig** (`REPLACE(col, '[^0-9.-]', '')`) para los casos excepcionales en que el enunciado obliga a dejar la columna numérica tras Pig. Se listan las formas **rotas** detectadas en la práctica (`REPLACE(col, '

', '')` y `REGEX_EXTRACT` no anclado), que dejan la columna vacía sin error visible.
- Se aclara el **patrón determinístico de TOP N**: `ORDER BY → STORE → !head -n N part-*`. El patrón `LOAD + LIMIT` tras el STORE intermedio devuelve las N tuplas correctas pero sin orden garantizado, así que no vale para presentar un ranking DESC en el informe.

---

## 2. Qué manda en UD3
Cuando haya dudas, manda este orden:

1. **enunciado oficial de UD3**,
2. **este contexto de UD3**,
3. **tarea resuelta de UD3 revisada**,
4. **cuadernos de Pig / Spark / Hive / HQL**,
5. **programación oficial del módulo**,
6. **teoría y resumen**.

## Regla crítica
Si una definición general contradice el patrón operativo de UD3, **manda el patrón operativo de UD3**.

No reinterpretar un apartado por intuición.

---

## 3. Qué se evalúa realmente en UD3
UD3 evalúa resolver un pipeline completo con varias herramientas integradas.

Lo que cuenta de verdad es:

1. importar o cargar el dataset de forma reproducible,
2. revisar calidad con Pandas,
3. corregir al menos **2 problemas** con Pig,
4. hacer una transformación útil con Pig,
5. cargar tablas con Spark en Hive,
6. lanzar al menos **2 consultas HQL** con **2 tablas**,
7. validar resultados e interpretarlos.

## Herramientas núcleo
- **Pandas**: inspección rápida y detección de problemas; generación del esquema Pig.
- **Pig**: limpieza y transformación ETL.
- **PySpark**: lectura, tipado y carga a tablas.
- **Hive / HQL**: consultas finales.

---

## 4. Regla semántica crítica de UD3
### El pipeline obligatorio de UD3 es:
**Pandas → Pig → Spark → Hive → HQL**

Esto significa:
- Pandas sirve para mirar, decidir y preparar el esquema Pig,
- Pig sirve para limpiar y transformar (conservador con lo sucio),
- Spark sirve para leer, tipar lo que quedó como texto y cargar,
- Hive/HQL sirve para consultar.

### Prohibido como sustitución
No debe:
- saltarse Pig y hacer toda la limpieza en Pandas o Spark si el enunciado exige Pig,
- cargar a Hive sin haber pasado por el análisis previo,
- hacer consultas sin tener claro qué tablas y claves usa,
- usar teoría general para cambiar el orden real de trabajo,
- **castear dentro de Pig lo que Spark va a tipar después**.

---

## 5. Regla de entrada de datos
La entrada del dataset depende del caso real.

El agente debe usar el método que mande el enunciado o el que ya esté preparado en el cuaderno:
- CSV ya subido a Colab,
- URL directa,
- Google Drive,
- Kaggle,
- ZIP descargado,
- o ruta ya existente.

## Regla
No debe asumir que siempre toca Kaggle.

### Orden correcto
1. mirar cómo llega el dataset en este caso,
2. comprobar dónde queda,
3. verificar qué archivos reales hay,
4. y solo entonces cargar con Pandas o seguir con Pig/Spark.

---

## 6. Pipeline canónico obligatorio de UD3

### Paso 1 · Entender el dataset
Objetivo:
- saber cuántos archivos hay,
- qué representa cada uno,
- qué columnas unen tablas,
- y qué preguntas analíticas tienen sentido.

### Paso 2 · Importar o cargar los datos
Objetivo:
- dejar el dataset accesible en rutas claras y reproducibles,
- carpeta de entrada siempre `input/` (relativa al CWD de Colab),
- listar el contenido con `!ls input/` y ver cabecera con `!head -1 input/<archivo>` antes de seguir.

### Paso 3 · Revisar con Pandas
Objetivo:
- detectar problemas antes de limpiar.

Debe mirar:
- `head()`,
- `info()`,
- `shape`,
- `isnull().sum()`,
- tipos,
- fechas raras,
- importes como string,
- vacíos anómalos,
- y claves sospechosas.

### Paso 4 · Mapear dtypes de Pandas a tipos Pig
Objetivo:
- obtener el esquema Pig real de cada CSV antes de escribir cualquier `LOAD`.

Se detalla en el apartado **7**. Es obligatorio. No se escribe ningún `LOAD` sin haberlo ejecutado.

### Paso 5 · Detectar problemas de calidad
Objetivo:
- convertir la observación en lista de acciones.

La lista mínima debería identificar:
- columna con nulos,
- columna con vacíos,
- fecha mal formateada,
- importe o cantidad guardado como texto (se deja como texto en Pig; ver apartado **8**),
- columnas clave de unión.

### Paso 6 · Limpiar y transformar con Pig
Objetivo:
- corregir al menos **2 problemas**,
- y hacer la transformación que pida el enunciado,
- siguiendo la estrategia conservadora (apartado **8**).

### Paso 7 · Preparar Spark
Objetivo:
- arrancar Spark con soporte Hive.

### Paso 8 · Cargar y tratar datos con PySpark
Objetivo:
- leer archivos limpios o finales,
- revisar schema,
- renombrar y tipar si hace falta (es aquí donde se pasan a `double` los importes dejados como texto en Pig),
- preparar DataFrames coherentes.

### Paso 9 · Crear o poblar tablas en Hive
Objetivo:
- pasar de DataFrame a tabla consultable.

### Paso 10 · Consultas HQL
Objetivo:
- demostrar explotación real del dato.

### Paso 11 · Validar resultados
Objetivo:
- comprobar que el pipeline no se ha roto.

### Paso 12 · Interpretar y cerrar
Objetivo:
- dejar una conclusión breve y conectada con el resultado.

---

## 7. Regla de mapeo Pandas → Pig obligatorio

### 7.1. Por qué existe esta regla
El patrón de la tarea resuelta que SÍ funciona genera el esquema Pig a partir de los dtypes reales de Pandas. Si el agente se salta este paso, tiende a inventarse tipos (`id:int` cuando en Pandas es int64, tipos numéricos en columnas que en Pandas son `object` con símbolos) y el script falla de forma silenciosa con warnings como `FIELD_DISCARDED_TYPE_CONVERSION_FAILED`.

### 7.2. Mapeo exacto
Antes de escribir el primer `LOAD` en Pig, el agente debe ejecutar un bloque Python que:

1. lea cada CSV con `pandas.read_csv`,
2. imprima `df.dtypes`,
3. traduzca cada dtype a su tipo Pig con este mapeo **cerrado**:

```
int64            → long
float64          → double
object           → chararray
bool             → boolean
datetime64[ns]   → datetime
```

4. imprima el esquema Pig listo para pegar en el `LOAD`.

### 7.3. Patrón Python de generación de esquema (genérico, N archivos)
Este bloque descubre todos los CSV de `input/` y genera el esquema de cada uno. No depende del dataset:

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

La salida se pega tal cual dentro del `AS (...)` del `LOAD` Pig correspondiente a cada CSV.

### 7.4. Regla dura
- El agente **no escribe** un `LOAD` sin haber ejecutado antes el bloque de mapeo sobre ese CSV.
- El esquema del `LOAD` debe **coincidir exactamente** con la salida del mapeo.
- No se inventan tipos, ni se "aproximan" por intuición.
- Si una columna sale como `object` en el mapeo, en Pig es `chararray`. No se "corrige" a numérico dentro de Pig (ver apartado **8**).

---

## 8. Regla de estrategia conservadora para columnas sucias

### 8.1. Qué es una columna sucia
Una columna que en Pandas aparece como `object` (texto) aunque conceptualmente sea numérica o fecha. Típicamente:
- importes con símbolos (`$`, `€`) delante,
- cantidades con comas de miles o puntos como separador de miles,
- valores con espacios internos,
- columnas con mezcla de números y `NaN` textual (`"NaN"`, `"null"`, `"None"`),
- fechas en formatos mixtos.

### 8.2. Estrategia
**En Pig la columna se declara `chararray` y se propaga tal cual.**
**La limpieza numérica o de fechas (quitar símbolos, pasar a `double`, parsear fecha) se hace en Spark.**

Motivo operativo:
- Pig 0.17 no tiene funciones de limpieza robustas para estos casos.
- Intentar `(double) REPLACE(TRIM(col), ',', '')` falla silenciosamente sobre miles de filas y deja la columna vacía.
- Spark tiene `regexp_replace`, `to_date`, `cast(...)` y hace el mismo trabajo sin warnings.

### 8.3. Prohibiciones
Dentro de scripts Pig, **prohibido** (como norma general — ver §8.4 para la excepción):
- `(float) REPLACE(...)` sobre columnas con símbolos,
- `CASE WHEN ... THEN (float)... END` para limpiar columnas sucias,
- cualquier cast numérico sobre columnas que Pandas reportó como `object` con símbolos o mezcla de formatos,
- **prohibido siempre** `REPLACE(col, '

', '')` — en regex Pig, `

 significa end-of-string, no el carácter literal `$`; no elimina nada y deja la columna **vacía** sobre miles de filas sin warning que lo delate,
- **prohibido siempre** `REGEX_EXTRACT(col, 'patrón no anclado', 1)` con patrones tipo `([0-9]+)` sobre cadenas que empiezan con `$` u otros prefijos. Pig exige match de cadena completa: si el patrón no cubre `$`, devuelve `null` silenciosamente, el cast posterior a `double` no falla (cast de null = null), y la columna sale vacía. El contador `FIELD_DISCARDED_TYPE_CONVERSION_FAILED` solo refleja los nulos de origen, no los rotos por el match fallido.

### 8.4. Cuándo sí convertir en Pig
Conversiones sencillas y sin riesgo sí valen en Pig:
- `(long)` sobre una columna que Pandas reporta como `int64` pero que por alguna razón entra como `chararray` (raro).
- `(double)` sobre una columna que Pandas reporta como `float64` limpia.
- Reemplazos categóricos sin cast (`(col IS NULL) ? 'U' : col`).

### 8.4 bis. Excepción controlada: limpieza de símbolos en Pig
Si el enunciado obliga a dejar la columna numérica **ya tras Pig** (no se admite dejarla como `chararray` hasta Spark), el único patrón seguro verificado en Pig 0.17 es:

```pig
(double) REPLACE(<col_sucia>, '[^0-9.-]', '') AS <col_sucia>
```

Por qué este funciona:
- `[^0-9.-]` = clase negada: "cualquier carácter que NO sea dígito, punto o menos".
- Dentro de `[...]`, el `

 pierde su significado de end-of-string y se trata como un carácter más que cae bajo "no dígito/punto/menos", y se elimina.
- No hay `

 literal ni `\` en el patrón, así que no hay problemas de escape entre `%%writefile`, Jupyter, Colab y Pig.
- Cubre los casos habituales: `$-77.00` → `-77.00`, `$14.57` → `14.57`, `$1,234.56` → `1234.56`.

Comprobación obligatoria tras ejecutar:
1. `!head -n 10 salida_<qué_es>/part-*` — verificar visualmente que la columna numérica sale con valores reales (no vacía).
2. Contador `FIELD_DISCARDED_TYPE_CONVERSION_FAILED` — aceptable si es un número pequeño correspondiente a nulos de origen; inaceptable si se acerca al total de filas.

Regla sencilla: **si dudas de si la columna es limpia, déjala `chararray` y arregla en Spark**. Solo usar §8.4 bis cuando el enunciado obligue a tener la columna tipada tras Pig.

### 8.5. Ejemplo ilustrativo
*Ejemplo — no asumir estas columnas como propias del examen.*

Una columna `<col_sucia>` que en Pandas sale como `object`:

```pig
-- en Pig: se deja como texto
<col_sucia>:chararray
```

```python
# en Spark: limpieza numérica
from pyspark.sql.functions import regexp_replace, col

sdf = sdf.withColumn(
    "<col_sucia>",
    regexp_replace(col("<col_sucia>"), r"[\$€,]", "").cast("double")
)
```

### 8.6. Ejemplo incorrecto (nunca hacer dentro de Pig)
```pig
-- NUNCA en UD3: falla silenciosamente y corrompe miles de filas
limpio = FOREACH datos GENERATE
    (float) REPLACE(TRIM(<col_sucia>), ',', '') AS <col_sucia>;
```

---

## 9. Regla de fidelidad al enunciado

### 9.1. Contrato del agente
El agente resuelve **literalmente** lo que el enunciado pide. No sustituye apartados por otros que le resulten más cómodos o más interesantes.

### 9.2. Casos a vigilar
- Si el enunciado pide un análisis concreto (ranking, top N de algo, cruce entre archivos), se resuelve ese análisis, no uno parecido.
- Si el apartado se subdivide en sub-puntos (p. ej. "1. corregir fallos; 2. análisis interesante"), debe abordarlos **todos** y en el orden del enunciado.
- Si el enunciado sugiere un análisis con la fórmula "por ejemplo", **es un ejemplo**, no una orden — pero tampoco una invitación libre: si el agente quiere proponer otro, lo declara en chat, justifica por qué y pide OK antes de codificar.
- Si el enunciado no es claro, se pregunta, no se adivina.

### 9.3. Regla dura
- El agente lee el apartado **entero** antes de escribir código.
- Nunca da una tarea por cumplida si solo ha resuelto parte del apartado.
- Si detecta que se está desviando del literal del enunciado, para y vuelve al chat.

---

## 10. Regla crítica de Pig en UD3 (modo, rutas y nombres)

### 10.1. Modo Pig de referencia en Colab
**En Colab, el modo Pig de referencia es `!pig script.pig` sin `-x local`**, igual que la tarea resuelta.

Motivo operativo: en Colab con Hadoop recién instalado y sin NameNode formateado, `fs.defaultFS` vale `file:///`. Tanto `hdfs dfs -put` como `pig` (sin `-x local`) operan sobre el filesystem local. Las rutas relativas (`'input/<archivo>'`, `'salida_<qué_es>'`) se resuelven contra el CWD (`/content/`).

### 10.2. Rutas obligatorias
En todos los scripts Pig de UD3, por defecto:

- **LOAD** con ruta relativa: `LOAD 'input/<archivo>.csv'`
- **STORE** con ruta relativa y nombre explícito: `STORE ... INTO 'salida_<qué_es>'`
- **NO** usar rutas con `/` inicial (`/input/...`, `/output/...`).
- **NO** usar `/content/...` salvo que haya razón concreta.
- **NO** usar nombres genéricos como `output`, `tmp`, `data`. Deben describir el contenido: `salida_limpios`, `salida_filtrados`, `salida_top_<algo>`, `salida_<algo>_tmp`.

### 10.3. Regla dura anti-mezcla
Está prohibido:

- ejecutar `pig -x local` con rutas HDFS absolutas (`/input/...`, `/output/...`),
- ejecutar `!pig` (sin `-x local`) y luego leer la salida con `!hdfs dfs -cat` si antes has hecho `hdfs dfs -put` sobre un Hadoop sin NameNode arrancado,
- mezclar rutas absolutas y relativas en el mismo script,
- decidir el modo Pig por criterio propio si la tarea resuelta de referencia ya fija uno.

### 10.4. Comando de carga a HDFS coherente
Si el flujo anterior ha cargado los archivos con `!hdfs dfs -put`, la ruta de destino debe ser relativa (`input`) o coincidir con lo que luego carga el script Pig. Ejemplo válido:

```bash
!hdfs dfs -mkdir input
!hdfs dfs -put datasets/*.csv input
!hdfs dfs -ls input
```

Y el script Pig carga con `LOAD 'input/<archivo>.csv'`.

### 10.5. Lectura de salida Pig
Cuando Pig ha escrito en una carpeta relativa (p. ej. `salida_limpios`, `salida_<algo>_tmp`), la salida queda en `/content/<carpeta>/`. Por tanto:

- listar: `!ls <carpeta>`
- ver primeras líneas: `!head <carpeta>/part-*`
- leer completo: `!cat <carpeta>/part-*`
- en Pandas: `pd.read_csv('<carpeta>/part-m-00000', header=None, ...)`

No usar `!hdfs dfs -cat` sobre esas rutas. No existe un HDFS real detrás.

### 10.6. Borrado previo de salidas
Pig no sobrescribe. Antes de cada `STORE` la carpeta de salida no debe existir.

- si la salida es relativa: `!rm -rf <carpeta>`

---

## 11. Regla de compatibilidad con Pig antiguo
En los cuadernos se está usando una versión antigua de Pig (0.17).

### Por tanto el agente debe:
- preferir sintaxis simple,
- evitar construcciones innecesariamente modernas,
- usar `chararray` si hay dudas de tipos,
- y no complicar el script con funciones raras si una versión básica sirve.

### Patrón seguro
- `LOAD`
- `FOREACH ... GENERATE`
- `FILTER`
- `FLATTEN(TOKENIZE(...))`
- `GROUP`
- `COUNT`
- `ORDER`
- `LIMIT`
- `STORE`
- `DUMP`

### Regla
Si una operación puede resolverse con sintaxis Pig sencilla, el agente debe elegir esa versión.

---

## 12. Regla anti-sintaxis Pig inventada

### 12.1. Problema
Gemini tiende a inventar sintaxis Pig que parece lógica pero no existe. Ejemplos detectados en tareas reales:

- `DEFINE STOPWORDS_EN (group ('a','an','the') as (word:chararray))` → Pig lanza `mismatched input 'group' expecting RIGHT_PAREN`. La sintaxis de `DEFINE` no acepta eso.
- `LOAD 'archivo.txt' USING InlineStorage(...)` → no existe `InlineStorage`.
- `FILTER x BY col IN ('a','b','c')` → en Pig 0.17 no hay `IN`; hay que encadenar `col != 'a' AND col != 'b' AND col != 'c'` o usar JOIN/COGROUP contra una relación.

### 12.2. Regla dura
- Si el agente no está 100% seguro de que una construcción existe en Pig 0.17, **no la usa**.
- Antes de usar una función, la busca en las plantillas de este documento o en la tarea resuelta. Si no está, vuelve al chat y pregunta.
- No se inventan UDF ni `DEFINE` con sintaxis no estándar.
- No se asumen archivos externos (`stopwords.txt`, `config.json`, etc.) si no han sido creados explícitamente en una celda previa del cuaderno (típicamente con `%%writefile`).

### 12.3. Alternativa segura para listas cerradas
Para filtrar contra una lista cerrada de valores (stopwords, categorías a excluir, etc.) en Pig 0.17, se encadenan desigualdades:

```pig
-- patrón genérico para lista cerrada
filtrada = FILTER <relacion> BY
    <col> != '<valor_1>' AND
    <col> != '<valor_2>' AND
    <col> != '<valor_3>' AND
    <col> != '<valor_4>';
```

Si la lista crece mucho, se vuelca a un archivo local con `%%writefile` y se hace JOIN/COGROUP contra esa relación.

---

## 13. Patrón obligatorio TOP N (ORDER BY + LIMIT)

### 13.1. Bug conocido de Pig 0.17
`ORDER BY ... DESC` seguido directamente de `LIMIT` y `STORE`/`DUMP` puede fallar en Pig 0.17 sobre MapReduce con `ERROR 2244: Job failed, hadoop does not return any error message`. Es un error sin stacktrace útil.

### 13.2. Patrón preferido — determinístico y en orden DESC
El TOP N con **orden garantizado** se resuelve partiendo el trabajo en dos: Pig ordena y materializa, bash extrae las N primeras.

```pig
-- en el script Pig
ordenado = ORDER <relacion_contada> BY <campo_conteo> DESC;
STORE ordenado INTO 'salida_<algo>_ordenado';
```

```bash
# en la celda de ejecución
!rm -rf salida_<algo>_ordenado
!pig <script>.pig

# TOP N en orden DESC garantizado (head es secuencial)
!head -n <N> salida_<algo>_ordenado/part-*
```

Por qué este es el patrón preferido:
- El `STORE` tras `ORDER BY` materializa el fichero físicamente ordenado en disco.
- `head` lee secuencialmente desde el inicio del `part-*`, así que las N primeras líneas son las N primeras filas ordenadas DESC.
- Sin riesgo de reordenación por paralelismo.
- Patrón reutilizable para cualquier ranking: top ciudades, top categorías, top usuarios, top palabras, top cualquier cosa.

### 13.3. Patrón alternativo 100 % Pig (solo si el enunciado exige que el TOP N salga desde Pig)
Si por cualquier razón se necesita que el TOP N quede persistido como relación Pig (p. ej. para encadenar con un JOIN posterior dentro del mismo flujo Pig), el patrón es:

```pig
ordenado = ORDER <relacion_contada> BY <campo_conteo> DESC;
STORE ordenado INTO 'salida_<algo>_tmp';

reloaded = LOAD 'salida_<algo>_tmp' USING PigStorage()
           AS (<col1>:<tipo1>, <col2>:<tipo2>);

topN = LIMIT reloaded <N>;
STORE topN INTO 'salida_top_<N>_<algo>';
```

Limitación conocida: `LIMIT` sobre el `LOAD` de un STORE ordenado **no garantiza** devolver las N tuplas en orden DESC. Pig puede paralelizar la lectura del `part-*` y el orden de las N tuplas resultantes es impredecible. Las N palabras/categorías son las correctas (las del top), pero el fichero de salida puede contener, por ejemplo, `visa,2326 / debit,4089 / mastercard,3209` en vez de `debit,4089 / mastercard,3209 / visa,2326`.

Si el enunciado o el informe exigen orden DESC en la salida, **no usar este patrón**: usar el §13.2.

### 13.4. Regla dura
- No usar `LIMIT` directamente sobre el resultado de `ORDER BY` en el mismo script sin pasar por STORE intermedio. Si se hace y falla, no reintentar por inercia: aplicar §13.2 o §13.3.
- Por defecto, **§13.2 (ORDER → STORE → `!head`)**. Solo cambiar a §13.3 si el flujo posterior es 100 % Pig y nadie va a mirar la salida visualmente.

---

## 14. Regla de plantillas Pig
El agente no debe improvisar desde cero si el caso encaja con una plantilla típica.

### Casos tratables como plantillas adaptables
- corrección de nulos,
- filtrado de filas inválidas,
- limpieza de texto (mayúsculas, trim),
- top categorías,
- top ciudades,
- top palabras con stopwords inline,
- conteo por grupo,
- ranking simple,
- wordcount o tokenización,
- JOIN entre dos relaciones,
- transformaciones derivadas (columna nueva a partir de otras).

### Qué debe adaptar
- `LOAD` (esquema real del mapeo),
- columnas,
- filtros,
- agrupación,
- orden,
- `STORE` con nombre descriptivo,
- rutas relativas.

### Qué no debe rehacer por completo
- la secuencia lógica del script,
- el patrón `LOAD → FILTER/FOREACH → GROUP → COUNT → ORDER → STORE`,
- el patrón TOP N con STORE intermedio,
- la estrategia conservadora para columnas sucias.

---

## 15. Regla anti-reintento ante error o warning Pig

### 15.1. Qué hacer si un script Pig falla o avisa
1. **no reintentar la misma celda**,
2. **no relanzar con `-v` sin consultar**,
3. **no abrir el log y seguir ejecutando**,
4. volver al chat,
5. describir: qué script, qué comando, qué línea del log o qué warning parece relevante,
6. proponer una corrección concreta,
7. esperar confirmación.

### 15.2. Ojo con los "éxitos silenciosos"
Un script Pig puede "terminar OK" pero con miles de warnings tipo:
- `FIELD_DISCARDED_TYPE_CONVERSION_FAILED N time(s)`
- `ACCESSING_NON_EXISTENT_FIELD`

Eso **no es éxito**. Es corrupción silenciosa de datos. Si el contador de warnings es alto, o si la columna de salida está vacía al inspeccionarla (`!head salida_*/part-*`), hay que parar y volver al chat aunque el job haya terminado.

### 15.3. Regla dura
Máximo **una** ejecución por celda Pig. Si falla **o** si termina con warnings masivos, se para y se consulta.

No llenar el cuaderno de celdas de reintento, volcado de logs, `pig -v`, relanzamientos y ejecuciones repetidas. Esas celdas son ruido y violan el protocolo.

---

## 16. Regla anti-DUMP masivo

### Problema
`DUMP` sobre una relación con miles de filas:
- genera una salida enorme,
- queda truncada por Colab,
- no aporta comprobación útil,
- ensucia el cuaderno.

### Regla
Para comprobar un dataset limpio o transformado:

1. primero limitar: `muestra = LIMIT <relacion> 10;`
2. luego volcar: `DUMP muestra;`

o bien:

1. `STORE <relacion> INTO 'salida_<qué_es>';`
2. desde shell: `!head salida_<qué_es>/part-*`

No hacer `DUMP` de la relación completa salvo que el resultado final sea pequeño por diseño (p. ej. ya viene de un `LIMIT N`).

---

## 17. Regla de outputs en Pig

### Antes de usar `STORE`
El agente debe comprobar si la carpeta de salida ya existe.

Si existe, debe borrarla antes:
- en local (modo por defecto en Colab): `!rm -rf <carpeta_salida>`
- si de verdad hay HDFS real: `!hdfs dfs -rm -r -f /<ruta_salida>`

### Regla dura
No debe asumir que `STORE` sobrescribe.
En Pig, normalmente la salida debe no existir.

---

## 18. Regla de lectura de resultados Pig

### Si el script usa `DUMP`
Sirve para ver el resultado por pantalla, pero no sustituye una salida persistida. Además, DUMP masivo es ruido (ver sección 16).

### Si el script usa `STORE`
Después debe:
- listar la salida (`!ls <carpeta>`),
- abrir el `part-*` con `!head` o `!cat`,
- y comprobar que realmente contiene datos (columnas no vacías, número de filas razonable).

### Regla
No dar por buena la transformación solo porque el script "ha corrido".
Hay que comprobar la salida real.

---

## 19. Qué cambiar y qué no cambiar
### Cambia siempre
- rutas,
- nombres de archivo,
- nombres de columnas,
- esquema Pig (según mapeo real),
- reglas de limpieza,
- claves de join,
- nombre de base de datos,
- nombre de tablas,
- filtros y agregaciones.

### Intenta no cambiar salvo necesidad
- el orden del pipeline,
- Pandas antes de Pig,
- el paso de mapeo Pandas → Pig antes del primer LOAD,
- Pig antes de Spark/Hive,
- `enableHiveSupport()` si luego vas a guardar tablas,
- validación final,
- el requisito de corregir 2 problemas con Pig,
- la estrategia conservadora para columnas sucias,
- la secuencia simple de Pig cuando ya encaja con el problema,
- el modo Pig de referencia (`!pig` sin `-x local`, rutas relativas, nombres `salida_...`),
- el patrón TOP N con STORE intermedio,
- el literal del enunciado (fidelidad).

---

## 20. Regla de adaptación por herramienta

### 20.1. Pandas
Pandas no resuelve UD3 por sí solo. Sirve para:
- ver qué está mal,
- justificar la limpieza,
- revisar el dataset rápido,
- **generar el esquema Pig a partir de los dtypes reales**.

#### Bloques núcleo
```python
import pandas as pd

df = pd.read_csv("input/<archivo>.csv")
df.head()
df.info()
df.isnull().sum()
print(df.shape)
```

#### Comprobaciones útiles (adaptar columnas al dataset real)
```python
pd.to_datetime(df["<col_fecha>"], errors="coerce")
pd.to_numeric(df["<col_importe>"], errors="coerce")
(df.astype(str)
   .apply(lambda s: s.str.strip())
   .isin(["", "null", "None", "nan"])
 ).sum()
```

#### Generación de esquema Pig (obligatorio antes del primer LOAD)
Ver §7.3. Bloque genérico para N archivos.

### 20.2. Pig
Pig es obligatorio para la fase de limpieza/transformación si el enunciado lo pide.

Todas las plantillas van con placeholders. Sustituir por los nombres y el esquema reales de cada caso.

#### 20.2.1. Plantilla de limpieza de nulos
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

Ejecución:
```bash
!rm -rf salida_limpios
!pig procesar_nulos.pig
!head salida_limpios/part-*
```

#### 20.2.2. Plantilla de filtrado por nulos o por condición
```pig
-- filtrar.pig
datos = LOAD 'input/<archivo>.csv' USING PigStorage(',') AS (
    <esquema_del_mapeo>
);

filtrado = FILTER datos BY NOT (<col_a> IS NULL OR <col_b> IS NULL);
-- o cualquier otra condición: <col> > <umbral>, <col> MATCHES '<regex>', etc.

STORE filtrado INTO 'salida_filtrados' USING PigStorage(',');
```

#### 20.2.3. Plantilla TOP N por grupo (patrón preferido §13.2)
Patrón general para cualquier ranking por conteo o suma sobre un campo categórico, con orden DESC garantizado en la salida.

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

Ejecución + TOP N en orden DESC garantizado:
```bash
!rm -rf salida_<campo>_ordenado
!pig top_<campo>.pig

# TOP N en orden DESC con head secuencial
!head -n <N> salida_<campo>_ordenado/part-*
```

Si el enunciado obliga a encadenar el TOP N con otro paso Pig posterior, ver §13.3 y asumir que la salida del TOP puede quedar desordenada.

#### 20.2.4. Plantilla de análisis de texto (tokenización + stopwords inline + TOP N)
Patrón general para cualquier enunciado que pida palabras más repetidas sobre una o varias columnas de texto. Vale para 1 archivo, 2 archivos, o N.

Paso previo: haber ejecutado el mapeo Pandas → Pig sobre cada CSV y saber qué columnas de texto tienen sentido tokenizar.

```pig
-- analisis_texto.pig

-- 1. Cargar cada CSV con su esquema real (salida del mapeo)
rel_1 = LOAD 'input/<archivo_1>.csv' USING PigStorage(',') AS (
    <esquema_1>
);

rel_2 = LOAD 'input/<archivo_2>.csv' USING PigStorage(',') AS (
    <esquema_2>
);

-- 2. Proyectar solo las columnas de texto a analizar
--    Todas deben tener el mismo alias de campo para poder UNIR
texto_1 = FOREACH rel_1 GENERATE <col_texto_1a> AS texto;
texto_2 = FOREACH rel_2 GENERATE <col_texto_2a> AS texto;

-- 3. Unir todos los textos
texto = UNION texto_1, texto_2;

-- 4. Tokenizar en palabras (en minúsculas)
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

-- 7. ORDER BY + STORE (el TOP N se extrae con !head fuera de Pig — §13.2)
ordenado = ORDER conteos BY cnt DESC;
STORE ordenado INTO 'salida_palabras_ordenado';
```

Ejecución:
```bash
!rm -rf salida_palabras_ordenado
!pig analisis_texto.pig

# TOP N en orden DESC garantizado
!head -n <N> salida_palabras_ordenado/part-*
```

Notas de adaptación:
- Con 1 archivo: se suprime `rel_2` / `texto_2` / `UNION` y se tokeniza directamente sobre la proyección.
- Con N > 2 archivos: se añaden proyecciones `texto_i` y se encadena `UNION texto_1, texto_2, ..., texto_N`.
- Si se quieren varias columnas del mismo archivo: se concatenan con `CONCAT` antes de `TOKENIZE` (p. ej. `TOKENIZE(LOWER(CONCAT(<col_a>, ' ', <col_b>)))`).
- Idioma(s) de la lista de stopwords: se decide según los datos (castellano, inglés o mezcla). La lista se mantiene **inline** en el script — sin `DEFINE` raro ni archivo externo a menos que se cree explícitamente con `%%writefile`.
- `<N>`: lo que pida el enunciado (3, 10, etc.).

#### 20.2.5. Plantilla de JOIN entre dos relaciones
```pig
-- join.pig
A = LOAD 'input/<archivo_A>.csv' USING PigStorage(',') AS (<esquema_A>);
B = LOAD 'input/<archivo_B>.csv' USING PigStorage(',') AS (<esquema_B>);

AB = JOIN A BY <clave_A>, B BY <clave_B>;

proj = FOREACH AB GENERATE
    A::<col_x>, A::<col_y>, B::<col_z>;

STORE proj INTO 'salida_join' USING PigStorage(',');
```

#### 20.2.6. Limpieza numérica de columnas sucias — **por defecto, no hacer en Pig**
En UD3, por defecto no se limpia numéricamente una columna sucia dentro de Pig. Ver §8. El trabajo lo hace Spark después.

Si el enunciado obliga a dejar la columna numérica tras Pig (excepción §8.4 bis), el único patrón verificado es con clase negada:

```pig
limpio = FOREACH <relacion> GENERATE
    <resto_columnas>,
    (double) REPLACE(<col_sucia>, '[^0-9.-]', '') AS <col_sucia>;
```

Por qué funciona:
- `[^0-9.-]` elimina cualquier carácter que no sea dígito, punto o menos.
- `

, `€`, comas, espacios, letras → fuera. `-77.00`, `14.57`, `1234.56` → intactos.
- Sin `

 literal en el patrón, sin `\` de escape → no hay conflicto entre `%%writefile` y Pig.

Comprobación obligatoria tras el STORE:
```bash
!head -n 10 salida_<qué_es>/part-*
```
La columna debe salir con valores reales. Si aparece vacía (doble coma), el script está roto aunque el log diga "Successfully stored".

**Prohibido** aunque parezca similar:
```pig
-- NUNCA: ' significa end-of-string, no elimina el símbolo → columna vacía
(double) REPLACE(<col_sucia>, '

', '') AS <col_sucia>

-- NUNCA: patrón no anclado → null silencioso → columna vacía sin warning
(double) REGEX_EXTRACT(<col_sucia>, '([0-9.]+)', 1) AS <col_sucia>
```

Ante la mínima duda, dejar `chararray` en Pig y arreglar en Spark.

### 20.3. Spark / PySpark
Spark no sustituye Pig. Su papel principal en UD3 es:
- leer los archivos que ha producido Pig,
- revisar schema,
- **tipar columnas que Pig dejó como `chararray` por estrategia conservadora**,
- guardar tablas.

#### Bloque núcleo
```python
from pyspark.sql import SparkSession

spark = (SparkSession.builder
    .appName("BDA_UD3")
    .enableHiveSupport()
    .getOrCreate())
```

#### Leer salida de Pig y revisar
```python
sdf = spark.read.csv(
    "salida_<qué_es>/part-*",
    header=False,
    inferSchema=False
)
sdf.printSchema()
sdf.show(5, truncate=False)
```

#### Limpieza numérica / fechas de columnas "sucias" (aquí, no en Pig)
```python
from pyspark.sql.functions import col, regexp_replace, to_date

# Ejemplo: columna sucia con símbolos y separadores
sdf = sdf.withColumn(
    "<col_sucia>",
    regexp_replace(col("<col_sucia>"), r"[\$€,]", "").cast("double")
)

# Ejemplo: fecha en formato ISO
sdf = sdf.withColumn(
    "<col_fecha>",
    to_date(col("<col_fecha>"), "yyyy-MM-dd")
)
```

### 20.4. Hive / HQL
Hive/HQL es la fase de explotación final.

#### Crear base y guardar tablas
```python
spark.sql("CREATE DATABASE IF NOT EXISTS <base>")
spark.sql("USE <base>")

sdf_A.write.mode("overwrite").saveAsTable("<tabla_A>")
sdf_B.write.mode("overwrite").saveAsTable("<tabla_B>")

spark.sql("SHOW TABLES").show(truncate=False)
```

#### Consulta simple
```python
spark.sql("""
SELECT <col_grupo>, COUNT(*) AS total
FROM <tabla_A>
GROUP BY <col_grupo>
ORDER BY total DESC
LIMIT 10
""").show()
```

#### Consulta con 2 tablas
```python
spark.sql("""
SELECT A.<col_x>, A.<col_y>, B.<col_z>
FROM <tabla_A> A
JOIN <tabla_B> B
  ON A.<clave_A> = B.<clave_B>
LIMIT 20
""").show()
```

---

## 21. Núcleos mínimos para principiante
### Pandas
- `read_csv`
- `head`
- `info`
- `shape`
- `isnull().sum()`
- `dtypes` (para el mapeo Pig)
- `to_datetime(..., errors='coerce')`
- `to_numeric(..., errors='coerce')`

### Pig
- `LOAD` (con esquema del mapeo)
- `FOREACH ... GENERATE`
- `FILTER`
- `FLATTEN(TOKENIZE(...))`
- `GROUP`
- `JOIN`
- `STORE` con nombre descriptivo
- `DUMP`
- `COUNT`
- `ORDER`
- `LIMIT` (con STORE intermedio si va tras ORDER)

### Spark
- `SparkSession`
- `read.csv`
- `printSchema`
- `withColumn` + `regexp_replace` + `cast("double")` (columnas sucias)
- `withColumn` + `to_date` (fechas)
- `select`
- `saveAsTable`

### Hive / HQL
- `CREATE DATABASE`
- `USE`
- `SELECT`
- `WHERE`
- `GROUP BY`
- `JOIN`

---

## 22. Traducción rápida de cada fase
- **Pandas**: "Miro qué está mal y genero el esquema Pig".
- **Pig**: "Arreglo y transformo archivos, dejando las columnas sucias como texto".
- **Spark**: "Leo esos archivos, tipo lo sucio a numérico o fecha, y los convierto en tablas".
- **Hive/HQL**: "Consulto las tablas como si fuera SQL".

---

## 23. Errores frecuentes que el agente debe vigilar

### Importación
- ruta mala,
- archivo no encontrado,
- ZIP descomprimido en otra carpeta.

### Pandas → Pig
- saltarse el mapeo y escribir el LOAD con tipos inventados,
- usar `int` en Pig cuando Pandas dice `int64` (Pig usa `long`),
- forzar `double` en Pig sobre una columna que Pandas reporta `object`.

### Pig
- schema incorrecto,
- separador mal,
- columnas desplazadas,
- salida vacía,
- usar nombres que no existen,
- mezclar modo local y rutas HDFS,
- rutas absolutas con `/` inicial en modo local,
- ORDER BY + LIMIT directo sin STORE intermedio,
- `STORE` sobre una carpeta existente,
- reintentar la misma celda tras un error,
- ignorar warnings masivos (`FIELD_DISCARDED_TYPE_CONVERSION_FAILED`),
- DUMP masivo sin LIMIT previo,
- castear columnas `object` a numérico dentro de Pig,
- inventar sintaxis (`DEFINE ... group(...)`, `IN (...)` inexistente en 0.17),
- depender de archivos externos no creados en el cuaderno.

### Spark
- schema mal inferido,
- confiar en `inferSchema` sin revisar,
- no activar soporte Hive,
- olvidarse de tipar las columnas que Pig dejó como texto.

### Hive
- `saveAsTable` falla por catálogo o por no usar la base correcta,
- tablas vacías.

### HQL
- join vacío,
- columnas ambiguas,
- claves que no tienen el mismo significado.

---

## 24. Qué debe comprobar antes de cada bloque
Antes de proponer cualquier bloque, el agente debe revisar:

- si el dataset entra por Kaggle, Drive, URL o local,
- si las rutas son reales,
- si los nombres de columnas existen,
- si ha ejecutado el **mapeo Pandas → Pig** sobre ese CSV,
- si el esquema del `LOAD` coincide con el mapeo,
- si la limpieza en Pig está justificada por Pandas,
- si se está ejecutando `!pig` sin `-x local` (modo de referencia),
- si todas las rutas del script son relativas y sin `/` inicial,
- si los nombres de salida son explícitos (`salida_...`),
- si la salida Pig ya existe y hay que borrarla,
- si el script hace TOP N y aplica el workaround,
- si está respetando el literal del enunciado (fidelidad),
- si Spark está leyendo el archivo correcto,
- si Spark está tipando las columnas que Pig dejó como `chararray`,
- y si las claves de join son realmente válidas.

---

## 25. Qué no puede hacer el agente en UD3
### Prohibido
- escribir un `LOAD` sin haber hecho antes el mapeo Pandas → Pig,
- inventar tipos en el esquema del `LOAD`,
- castear dentro de Pig columnas que Pandas reportó como `object` con símbolos,
- sustituir el apartado pedido por otro que considere "más interesante",
- dar un apartado por cumplido habiendo resuelto solo parte,
- usar Kaggle como vía obligatoria si el enunciado no lo pide,
- copiar una versión fija de Java/Spark como si fuera universal,
- saltarse Pig si el punto exige Pig,
- usar `pig -x local` como modo por defecto sin justificarlo,
- usar rutas HDFS absolutas (`/input/...`, `/output/...`) en scripts Pig,
- mezclar rutas locales y HDFS en el mismo bloque,
- hacer `LIMIT` tras `ORDER BY` sin STORE intermedio en el mismo script,
- hacer `DUMP` masivo sin LIMIT previo,
- reintentar una celda Pig tras un error o warning masivo sin consultar,
- llenar el cuaderno de celdas de log, `pig -v`, reintentos,
- inventar sintaxis Pig (`DEFINE ... group(...)`, `IN (...)`, funciones que no existen en 0.17),
- depender de archivos externos (`stopwords.txt`, `config.json`) no creados explícitamente en el cuaderno,
- hacer limpieza fuerte sin haberla justificado con Pandas,
- consultar tablas que no ha validado,
- hacer joins por columnas con nombres parecidos pero sentido distinto,
- cargar una tabla y consultar otra,
- hablar del pipeline como teoría sin ejecutarlo realmente.

---

## 26. Señales de que UD3 está bien resuelto
El proceso está bien resuelto si:

- el dataset está bien identificado,
- la entrada es reproducible,
- Pandas ha detectado problemas reales,
- el esquema Pig se ha generado a partir del mapeo real de dtypes,
- Pig corrige al menos 2 problemas,
- Pig hace exactamente la transformación pedida por el enunciado (no otra),
- Pig se ejecuta con `!pig` sin `-x local`,
- las rutas Pig son relativas y coherentes,
- los nombres de salida son explícitos (`salida_...`),
- las columnas "sucias" siguen siendo `chararray` al salir de Pig,
- el TOP N usa STORE intermedio,
- la salida Pig se comprueba con `!head`/`!cat` sobre la carpeta relativa y tiene datos,
- no hay warnings masivos de conversión,
- Spark carga tablas coherentes y tipa las columnas sucias,
- Hive muestra las tablas creadas,
- hay al menos 2 consultas útiles,
- al menos una usa 2 tablas,
- y los resultados se validan.

---

## 27. Señales de que el agente se está equivocando
El agente está fallando si:

- quiere usar una sola herramienta para todo,
- no distingue inspección, limpieza, carga y consulta,
- copia bloques sin adaptar rutas y columnas,
- no revisa schemas,
- salta el mapeo Pandas → Pig,
- inventa tipos en el `LOAD`,
- casteo `(float)` o `(double)` dentro de Pig sobre columnas con símbolos,
- ignora warnings masivos porque "el job ha acabado",
- cambia el análisis pedido por otro que le resulta más cómodo,
- usa `pig -x local` con rutas `/input/...`,
- pone rutas absolutas con `/` inicial en LOAD/STORE,
- usa nombres de salida genéricos (`output`, `data`, `tmp`),
- hace `LIMIT` justo después de `ORDER BY` sin STORE intermedio,
- reintenta la misma celda Pig tras un error,
- llena el cuaderno de logs y reintentos,
- inventa sintaxis Pig (`DEFINE ... group(...)`, `IN (...)`),
- depende de archivos que no existen,
- hace DUMP masivo,
- da por buena una salida sin leerla,
- o consulta sin haber validado la carga.

---

## 28. Criterio final
En UD3 el agente debe actuar así:

- primero entiende el dataset,
- luego lo hace accesible,
- después lo revisa con Pandas,
- **genera el esquema Pig a partir de los dtypes reales**,
- limpia y transforma con Pig siguiendo el patrón de la tarea resuelta y la **estrategia conservadora** para columnas sucias,
- respeta el **literal del enunciado** (no sustituye análisis),
- carga con Spark en Hive y tipa allí lo que dejó como texto en Pig,
- consulta con HQL,
- valida,
- e interpreta.

## Regla final
En UD3, el flujo correcto es:

**entrada reproducible → Pandas (con mapeo) → Pig (conservador, fiel al enunciado) → Spark (tipado) → Hive → HQL → validación**.

Y en Pig, el patrón operativo obligatorio en Colab es:

**`!pig script.pig` sin `-x local` + rutas relativas + nombres `salida_...` + LOAD con esquema del mapeo + chararray para columnas sucias + TOP N con `ORDER BY → STORE → !head` + una sola ejecución por celda + sin DUMP masivo + sin sintaxis inventada + si hay que limpiar símbolos en Pig solo `(double) REPLACE(col, '[^0-9.-]', '')`**.

Si el agente no está siguiendo esa cadena, hay que corregir el punto antes de seguir.
