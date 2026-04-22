# Contexto_UD3_agente_Gemini_Colab
## UD3 · Ecosistema Hadoop · contexto operativo v4

## 1. Objetivo
Guiar al agente para resolver correctamente una variante de la tarea o del examen práctico de UD3 dentro de Google Colab.

Este documento no es teoría general ni una solución cerrada.
Es una guía operativa para que el agente:

- entienda qué se evalúa realmente en UD3,
- siga el pipeline correcto,
- adapte los bloques al dataset real,
- no copie una solución anterior sin revisar,
- no cambie el sentido de los apartados por comodidad,
- y no se bloquee con Pig por mezclar rutas o modos de ejecución.

## Cambios respecto a v3
- Se fija el **modo Pig de referencia en Colab**: `!pig script.pig` sin `-x local`, rutas relativas.
- Se endurece la regla anti-mezcla de modos y rutas.
- Se añade **patrón obligatorio TOP N** (workaround ORDER BY + LIMIT con STORE intermedio).
- Se añade **regla anti-reintentos** ante error Pig.
- Se añade **regla anti-DUMP masivo**.
- Se incorporan plantillas Pig basadas en la tarea resuelta real que funciona en Colab.

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
- **Pandas**: inspección rápida y detección de problemas.
- **Pig**: limpieza y transformación ETL.
- **PySpark**: lectura, tipado y carga a tablas.
- **Hive / HQL**: consultas finales.

---

## 4. Regla semántica crítica de UD3
### El pipeline obligatorio de UD3 es:
**Pandas → Pig → Spark → Hive → HQL**

Esto significa:
- Pandas sirve para mirar y decidir,
- Pig sirve para limpiar y transformar,
- Spark sirve para leer y cargar,
- Hive/HQL sirve para consultar.

### Prohibido como sustitución
No debe:
- saltarse Pig y hacer toda la limpieza en Pandas o Spark si el enunciado exige Pig,
- cargar a Hive sin haber pasado por el análisis previo,
- hacer consultas sin tener claro qué tablas y claves usa,
- usar teoría general para cambiar el orden real de trabajo.

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
- dejar el dataset accesible en rutas claras y reproducibles.

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

### Paso 4 · Detectar problemas de calidad
Objetivo:
- convertir la observación en lista de acciones.

La lista mínima debería identificar:
- columna con nulos,
- columna con vacíos,
- fecha mal formateada,
- importe o cantidad guardado como texto,
- columnas clave de unión.

### Paso 5 · Limpiar y transformar con Pig
Objetivo:
- corregir al menos **2 problemas**,
- y hacer una transformación interesante.

### Paso 6 · Preparar Spark
Objetivo:
- arrancar Spark con soporte Hive.

### Paso 7 · Cargar y tratar datos con PySpark
Objetivo:
- leer archivos limpios o finales,
- revisar schema,
- renombrar y tipar si hace falta,
- preparar DataFrames coherentes.

### Paso 8 · Crear o poblar tablas en Hive
Objetivo:
- pasar de DataFrame a tabla consultable.

### Paso 9 · Consultas HQL
Objetivo:
- demostrar explotación real del dato.

### Paso 10 · Validar resultados
Objetivo:
- comprobar que el pipeline no se ha roto.

### Paso 11 · Interpretar y cerrar
Objetivo:
- dejar una conclusión breve y conectada con el resultado.

---

## 7. Regla crítica de Pig en UD3 (modo y rutas)

### 7.1. Modo Pig de referencia en Colab
**En Colab, el modo Pig de referencia es `!pig script.pig` sin `-x local`**, igual que la tarea resuelta.

Motivo operativo: en Colab con Hadoop recién instalado y sin NameNode formateado, `fs.defaultFS` vale `file:///`. Tanto `hdfs dfs -put` como `pig` (sin `-x local`) operan sobre el filesystem local. Las rutas relativas (`'input/archivo.csv'`, `'nulos'`) se resuelven contra el CWD (`/content/`).

### 7.2. Rutas obligatorias
En todos los scripts Pig de UD3, por defecto:

- **LOAD** con ruta relativa: `LOAD 'input/archivo.csv'`
- **STORE** con ruta relativa: `STORE ... INTO 'nombre_salida'`
- **NO** usar rutas con `/` inicial (`/input/...`, `/output/...`).
- **NO** usar `/content/...` salvo que haya razón concreta.

### 7.3. Regla dura anti-mezcla
Está prohibido:

- ejecutar `pig -x local` con rutas HDFS absolutas (`/input/...`, `/output/...`),
- ejecutar `!pig` (sin `-x local`) y luego leer la salida con `!hdfs dfs -cat` si antes has hecho `hdfs dfs -put` sobre un Hadoop sin NameNode arrancado,
- mezclar rutas absolutas y relativas en el mismo script,
- decidir el modo Pig por criterio propio si la tarea resuelta de referencia ya fija uno.

### 7.4. Comando de carga a HDFS coherente
Si el flujo anterior ha cargado los archivos con `!hdfs dfs -put`, la ruta de destino debe ser relativa (`input`) o coincidir con lo que luego carga el script Pig. Ejemplo válido:

```bash
!hdfs dfs -mkdir input
!hdfs dfs -put datasets/*.csv input
!hdfs dfs -ls input
```

Y el script Pig carga con `LOAD 'input/archivo.csv'`.

### 7.5. Lectura de salida Pig
Cuando Pig ha escrito en una carpeta relativa (ej. `nulos`, `city_counts_temp`), la salida queda en `/content/<carpeta>/`. Por tanto:

- listar: `!ls <carpeta>`
- ver primeras líneas: `!head <carpeta>/part-*`
- leer completo: `!cat <carpeta>/part-*`
- en Pandas: `pd.read_csv('<carpeta>/part-m-00000', ...)`

No usar `!hdfs dfs -cat` sobre esas rutas. No existe un HDFS real detrás.

### 7.6. Borrado previo de salidas
Pig no sobrescribe. Antes de cada `STORE` la carpeta de salida no debe existir.

- si la salida es relativa: `!rm -rf <carpeta>`

### 7.7. CRÍTICO: Rutas en Colab (no confundir con HDFS real)

**Esto es causa del 90% de atascos en Pig.**

En Colab, **NO existe HDFS real formateado**. Por tanto:

#### ❌ INCORRECTO (causará atasco):
```pig
transactions = LOAD '/input/archivo.csv' ...
STORE ... INTO '/output/resultado' ...
```

**¿Por qué?** `/input/` y `/output/` son rutas HDFS que no existen en Colab. Pig intenta encontrarlas en HDFS, el proceso se congela o falla silenciosamente.

#### ✅ CORRECTO (funciona en Colab):
```pig
transactions = LOAD 'input/archivo.csv' ...
STORE ... INTO 'salida_limpio' ...
```

#### Pasos previos (OBLIGATORIO):
Antes de crear script Pig, SIEMPRE haz esto:

```python
# 1. Crear carpeta relativa en Colab
!mkdir -p input

# 2. Copiar archivos desde donde estén a la carpeta relativa
!cp /content/datasets/*.csv input/
# O desde HDFS (si ya los pusiste):
!hdfs dfs -get /input/archivo.csv input/

# 3. Verificar que existen
!ls -la input/
!head input/archivo.csv
```

#### Regla de oro:
- **Entrada**: Carpeta relativa `'input/...'` (creada en Colab)
- **Salida**: Carpeta relativa `'salida_xxx'` (sin `/`)
- **Nunca**: Rutas con `/` inicial en LOAD o STORE
- **Lectura posterior**: `!head salida_xxx/part-m-00000` o pandas

#### Signo de alerta:
Si ves en un script:
- `LOAD '/input/...'`
- `STORE ... INTO '/output/...'`
- `!hdfs dfs -rm -r /output/...`

→ **ES UN ERROR**, debe corregirse a rutas relativas ANTES de ejecutar.

---

## 8. Regla de compatibilidad con Pig antiguo
En tus cuadernos se está usando una versión antigua de Pig (0.17).

### Por tanto el agente debe:
- preferir sintaxis simple,
- evitar construcciones innecesariamente modernas,
- usar `chararray` si hay dudas de tipos,
- y no complicar el script con funciones raras si una versión básica sirve.

### Patrón seguro
- `LOAD`
- `FOREACH ... GENERATE`
- `FILTER`
- `GROUP`
- `COUNT`
- `ORDER`
- `LIMIT`
- `STORE`
- `DUMP`

## Regla
Si una operación puede resolverse con sintaxis Pig sencilla, el agente debe elegir esa versión.

---

## 9. Patrón obligatorio TOP N (ORDER BY + LIMIT)

### 9.1. Bug conocido de Pig 0.17
`ORDER BY ... DESC` seguido directamente de `LIMIT` y `STORE`/`DUMP` puede fallar en Pig 0.17 sobre MapReduce con `ERROR 2244: Job failed, hadoop does not return any error message`. Es un error sin stacktrace útil.

### 9.2. Workaround obligatorio
Siempre que se haga un TOP N (top ciudades, top categorías, top usuarios, top palabras, ranking…) el script debe seguir esta secuencia:

```pig
ordered = ORDER datos BY campo DESC;
STORE ordered INTO 'tmp_ordered';
reloaded = LOAD 'tmp_ordered' USING PigStorage() AS (col1:chararray, col2:long);
topN = LIMIT reloaded 10;
DUMP topN;
```

### 9.3. Regla dura
No usar `LIMIT` directamente sobre el resultado de `ORDER BY` en el mismo script sin pasar por STORE intermedio. Si se hace y falla, no reintentar por inercia: aplicar el workaround.

---

## 10. Regla de plantillas Pig
El agente no debe improvisar desde cero si el caso encaja con una plantilla típica.

### Casos tratables como plantillas adaptables
- corrección de nulos,
- filtrado de filas inválidas,
- corrección de fechas,
- limpieza de importes,
- top categorías,
- top ciudades,
- conteo por grupo,
- ranking simple,
- wordcount o tokenización.

### Qué debe adaptar
- `LOAD`,
- columnas,
- tipos,
- filtros,
- agrupación,
- orden,
- `STORE`,
- rutas relativas.

### Qué no debe rehacer por completo
- la secuencia lógica del script,
- el patrón `LOAD → FILTER/FOREACH → GROUP → COUNT → ORDER → STORE`,
- el patrón TOP N con STORE intermedio.

---

## 11. Regla anti-reintento ante error Pig

### Qué hacer si un script Pig falla
1. **no reintentar la misma celda**,
2. **no relanzar con `-v` sin consultar**,
3. **no abrir el log y seguir ejecutando**,
4. volver al chat,
5. describir: qué script, qué comando, qué línea del log parece relevante,
6. proponer una corrección concreta,
7. esperar confirmación.

### Regla dura
Máximo **una** ejecución por celda Pig. Si falla, se para y se consulta.

No llenar el cuaderno de celdas de reintento, volcado de logs, `pig -v`, relanzamientos y ejecuciones repetidas. Esas celdas son ruido y violan el protocolo.

---

## 12. Regla anti-DUMP masivo

### Problema
`DUMP` sobre una relación con miles de filas:
- genera una salida enorme,
- queda truncada por Colab,
- no aporta comprobación útil,
- ensucia el cuaderno.

### Regla
Para comprobar un dataset limpio o transformado:

1. primero limitar: `muestra = LIMIT relacion 10;`
2. luego volcar: `DUMP muestra;`

o bien:

1. `STORE relacion INTO 'nombre_salida';`
2. desde shell: `!head nombre_salida/part-*`

No hacer `DUMP` de la relación completa salvo que el resultado final sea pequeño por diseño (p. ej. ya viene de un LIMIT N).

---

## 13. Regla de outputs en Pig

### Antes de usar `STORE`
El agente debe comprobar si la carpeta de salida ya existe.

Si existe, debe borrarla antes:
- en local (modo por defecto en Colab): `!rm -rf carpeta_salida`
- si de verdad hay HDFS real: `!hdfs dfs -rm -r -f /ruta_salida`

## Regla dura
No debe asumir que `STORE` sobrescribe.
En Pig, normalmente la salida debe no existir.

---

## 14. Regla de lectura de resultados Pig

### Si el script usa `DUMP`
Sirve para ver el resultado por pantalla, pero no sustituye una salida persistida. Además, DUMP masivo es ruido (ver sección 12).

### Si el script usa `STORE`
Después debe:
- listar la salida (`!ls <carpeta>`),
- abrir el `part-*` con `!head` o `!cat`,
- y comprobar que realmente contiene datos.

## Regla
No dar por buena la transformación solo porque el script “ha corrido”.
Hay que comprobar la salida real.

---

## 15. Qué cambiar y qué no cambiar
### Cambia siempre
- rutas,
- nombres de archivo,
- nombres de columnas,
- reglas de limpieza,
- claves de join,
- nombre de base de datos,
- nombre de tablas,
- filtros y agregaciones.

### Intenta no cambiar salvo necesidad
- el orden del pipeline,
- Pandas antes de Pig,
- Pig antes de Spark/Hive,
- `enableHiveSupport()` si luego vas a guardar tablas,
- validación final,
- el requisito de corregir 2 problemas con Pig,
- la secuencia simple de Pig cuando ya encaja con el problema,
- el modo Pig de referencia (`!pig` sin `-x local`, rutas relativas),
- el patrón TOP N con STORE intermedio.

---

## 16. Regla de adaptación por herramienta

### 16.1. Pandas
Pandas no resuelve UD3 por sí solo. Solo sirve para:
- ver qué está mal,
- justificar la limpieza,
- y revisar el dataset rápido.

#### Bloques núcleo
```python
import pandas as pd

df = pd.read_csv("ruta.csv")
df.head()
df.info()
df.isnull().sum()
print(df.shape)
```

#### Comprobaciones útiles
```python
pd.to_datetime(df["fecha"], errors="coerce")
pd.to_numeric(df["importe"], errors="coerce")
(df.astype(str).apply(lambda s: s.str.strip()).isin(["", "null", "None", "nan"])).sum()
```

### 16.2. Pig
Pig es obligatorio para la fase de limpieza/transformación si el enunciado lo pide.

#### 16.2.1. Plantilla de limpieza de nulos (patrón tarea resuelta)
```pig
-- procesar_nulos.pig
transactions = LOAD 'input/transactions_data.csv' USING PigStorage(',') AS (
   id:long,
   date:chararray,
   client_id:long,
   card_id:long,
   amount:chararray,
   use_chip:chararray,
   merchant_id:long,
   merchant_city:chararray,
   merchant_state:chararray,
   zip:double,
   mcc:long,
   errors:chararray
);

transactions_clean = FOREACH transactions GENERATE
    id..merchant_city,
    ((merchant_state IS NULL) ? 'U' : merchant_state),
    ((zip IS NULL) ? 0 : zip),
    mcc,
    ((errors IS NULL) ? 'N' : errors);

STORE transactions_clean INTO 'nulos' USING PigStorage(',');
```

Ejecución:
```bash
!rm -rf nulos
!pig procesar_nulos.pig
!head nulos/part-*
```

#### 16.2.2. Plantilla de filtrado por nulos
```pig
-- filtrar_nulos.pig
transactions2 = LOAD 'input/transactions_data.csv' USING PigStorage(',') AS (
    id:long, date:chararray, client_id:long, card_id:long,
    amount:chararray, use_chip:chararray, merchant_id:long,
    merchant_city:chararray, merchant_state:chararray,
    zip:double, mcc:long, errors:chararray
);

clean = FILTER transactions2 BY NOT (merchant_state IS NULL OR zip IS NULL OR errors IS NULL);

STORE clean INTO 'limpios' USING PigStorage(',');
```

#### 16.2.3. Plantilla TOP N (con workaround obligatorio)
```pig
-- top_cities.pig
transactions = LOAD 'input/transactions_data.csv' USING PigStorage(',') AS (
    id:long, date:chararray, client_id:long, card_id:long,
    amount:chararray, use_chip:chararray, merchant_id:long,
    merchant_city:chararray, merchant_state:chararray,
    zip:double, mcc:long, errors:chararray
);

city_group = GROUP transactions BY merchant_city;
city_counts = FOREACH city_group GENERATE group AS city, COUNT(transactions) AS cnt;
city_counts_ordered = ORDER city_counts BY cnt DESC;

STORE city_counts_ordered INTO 'city_counts_temp';

reloaded = LOAD 'city_counts_temp' USING PigStorage() AS (city:chararray, cnt:long);
top10 = LIMIT reloaded 10;

DUMP top10;
```

Ejecución:
```bash
!rm -rf city_counts_temp
!pig top_cities.pig
```

#### 16.2.4. Plantilla de limpieza de importes (quitar símbolos)
```pig
-- limpiar_dolar.pig
cards_data = LOAD 'input/cards_data.csv' USING PigStorage(',') AS (
    id:int, client_id:int, card_brand:chararray, card_type:chararray,
    card_number:chararray, expires:chararray, cvv:int, has_chip:chararray,
    num_cards_issued:int, credit_limit:chararray, acct_open_date:chararray,
    year_pin_last_changed:int, card_on_dark_web:chararray
);

cards_data_limpio = FOREACH cards_data GENERATE
    id, client_id, card_brand, card_type, card_number, expires, cvv,
    has_chip, num_cards_issued,
    (double) REGEX_EXTRACT(credit_limit, '([0-9.]+)', 1) AS credit_limit,
    acct_open_date, year_pin_last_changed, card_on_dark_web;

muestra = LIMIT cards_data_limpio 10;
DUMP muestra;
```

### 16.3. Spark / PySpark
Spark no sustituye Pig. Su papel principal en UD3 es:
- leer,
- revisar schema,
- ajustar tipos si hace falta,
- y guardar tablas.

#### Bloque núcleo
```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("BDA_UD3") \
    .enableHiveSupport() \
    .getOrCreate()
```

#### Leer y revisar
```python
sdf = spark.read.csv("ruta.csv", header=True, inferSchema=True)
sdf.printSchema()
sdf.show(5, truncate=False)
```

#### Ajuste de tipos típico
```python
from pyspark.sql.functions import col, regexp_replace, to_date

sdf = sdf.withColumn("importe", regexp_replace(col("importe"), ",", ".").cast("double"))
sdf = sdf.withColumn("fecha", to_date(col("fecha"), "yyyy-MM-dd"))
```

### 16.4. Hive / HQL
Hive/HQL es la fase de explotación final.

#### Crear base y guardar tablas
```python
spark.sql("CREATE DATABASE IF NOT EXISTS bda_ud3")
spark.sql("USE bda_ud3")

sdf1.write.mode("overwrite").saveAsTable("tabla1")
sdf2.write.mode("overwrite").saveAsTable("tabla2")

spark.sql("SHOW TABLES").show(truncate=False)
```

#### Consulta simple
```python
spark.sql("""
SELECT categoria, COUNT(*) AS total
FROM tabla1
GROUP BY categoria
ORDER BY total DESC
""").show()
```

#### Consulta con 2 tablas
```python
spark.sql("""
SELECT t1.id, t1.categoria, t2.otra_columna
FROM tabla1 t1
JOIN tabla2 t2
  ON t1.id = t2.id
LIMIT 20
""").show()
```

---

## 17. Núcleos mínimos para principiante
### Pandas
- `read_csv`
- `head`
- `info`
- `shape`
- `isnull().sum()`
- `to_datetime(..., errors='coerce')`
- `to_numeric(..., errors='coerce')`

### Pig
- `LOAD`
- `FOREACH ... GENERATE`
- `FILTER`
- `GROUP`
- `STORE`
- `DUMP`
- `COUNT`
- `ORDER`
- `LIMIT` (con STORE intermedio si va tras ORDER)

### Spark
- `SparkSession`
- `read.csv`
- `printSchema`
- `withColumn`
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

## 18. Traducción rápida de cada fase
- **Pandas**: “Miro qué está mal”.
- **Pig**: “Arreglo y transformo archivos”.
- **Spark**: “Convierto esos archivos en tablas”.
- **Hive/HQL**: “Consulto las tablas como si fuera SQL”.

---

## 19. Errores frecuentes que el agente debe vigilar

### Importación
- ruta mala,
- archivo no encontrado,
- ZIP descomprimido en otra carpeta.

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
- DUMP masivo sin LIMIT previo.

### Spark
- schema mal inferido,
- confiar en `inferSchema` sin revisar,
- no activar soporte Hive.

### Hive
- `saveAsTable` falla por catálogo o por no usar la base correcta,
- tablas vacías.

### HQL
- join vacío,
- columnas ambiguas,
- claves que no tienen el mismo significado.

---

## 20. Qué debe comprobar antes de cada bloque
Antes de proponer cualquier bloque, el agente debe revisar:

- si el dataset entra por Kaggle, Drive, URL o local,
- si las rutas son reales,
- si los nombres de columnas existen,
- si la limpieza en Pig está justificada por Pandas,
- si se está ejecutando `!pig` sin `-x local` (modo de referencia),
- si todas las rutas del script son relativas y sin `/` inicial,
- si la salida Pig ya existe y hay que borrarla,
- si el script hace TOP N y aplica el workaround,
- si Spark está leyendo el archivo correcto,
- y si las claves de join son realmente válidas.

---

## 21. Qué no puede hacer el agente en UD3
### Prohibido
- usar Kaggle como vía obligatoria si el enunciado no lo pide,
- copiar una versión fija de Java/Spark como si fuera universal,
- saltarse Pig si el punto exige Pig,
- usar `pig -x local` como modo por defecto sin justificarlo,
- usar rutas HDFS absolutas (`/input/...`, `/output/...`) en scripts Pig,
- mezclar rutas locales y HDFS en el mismo bloque,
- hacer `LIMIT` tras `ORDER BY` sin STORE intermedio en el mismo script,
- hacer `DUMP` masivo sin LIMIT previo,
- reintentar una celda Pig tras un error sin consultar,
- llenar el cuaderno de celdas de log, `pig -v`, reintentos,
- hacer limpieza fuerte sin haberla justificado con Pandas,
- consultar tablas que no ha validado,
- hacer joins por columnas con nombres parecidos pero sentido distinto,
- cargar una tabla y consultar otra,
- hablar del pipeline como teoría sin ejecutarlo realmente.

---

## 22. Señales de que UD3 está bien resuelto
El proceso está bien resuelto si:

- el dataset está bien identificado,
- la entrada es reproducible,
- Pandas ha detectado problemas reales,
- Pig corrige al menos 2 problemas,
- Pig hace una transformación útil,
- Pig se ejecuta con `!pig` sin `-x local`,
- las rutas Pig son relativas y coherentes,
- el TOP N usa STORE intermedio,
- la salida Pig se comprueba con `!head`/`!cat` sobre la carpeta relativa,
- Spark carga tablas coherentes,
- Hive muestra las tablas creadas,
- hay al menos 2 consultas útiles,
- al menos una usa 2 tablas,
- y los resultados se validan.

---

## 23. Señales de que el agente se está equivocando
El agente está fallando si:

- quiere usar una sola herramienta para todo,
- no distingue inspección, limpieza, carga y consulta,
- copia bloques sin adaptar rutas y columnas,
- no revisa schemas,
- usa `pig -x local` con rutas `/input/...`,
- pone rutas absolutas con `/` inicial en LOAD/STORE,
- hace `LIMIT` justo después de `ORDER BY` sin STORE intermedio,
- reintenta la misma celda Pig tras un error,
- llena el cuaderno de logs y reintentos,
- hace DUMP masivo,
- da por buena una salida sin leerla,
- o consulta sin haber validado la carga.

---

## 24. Criterio final
En UD3 el agente debe actuar así:

- primero entiende el dataset,
- luego lo hace accesible,
- después lo revisa con Pandas,
- limpia y transforma con Pig siguiendo el patrón de la tarea resuelta,
- carga con Spark en Hive,
- consulta con HQL,
- valida,
- e interpreta.

## Regla final
En UD3, el flujo correcto es:

**entrada reproducible → Pandas → Pig → Spark → Hive → HQL → validación**.

Y en Pig, el patrón operativo obligatorio en Colab es:

**`!pig script.pig` sin `-x local` + rutas relativas + STORE intermedio para TOP N + una sola ejecución por celda + sin DUMP masivo**.

Si el agente no está siguiendo esa cadena, hay que corregir el punto antes de seguir.
