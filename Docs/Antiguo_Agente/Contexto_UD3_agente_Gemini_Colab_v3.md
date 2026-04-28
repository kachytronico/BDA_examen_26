# Contexto_UD3_agente_Gemini_Colab
## UD3 · Ecosistema Hadoop · contexto operativo v3

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

## 7. Regla crítica de Pig en UD3
### Antes de ejecutar Pig hay que fijar el modo
El agente debe decidir explícitamente si trabajará en:

- **modo local**: `pig -x local script.pig`
- o **modo HDFS / MapReduce**: `pig script.pig` con rutas HDFS coherentes y entorno preparado.

## Regla dura
No puede mezclar dentro del mismo bloque:
- `pig -x local` con salidas que luego intenta leer con `hdfs dfs`,
- rutas locales tipo `/content/...` con lectura posterior como si fueran HDFS,
- ni rutas HDFS en un script que se está ejecutando en local si luego el entorno realmente usa `file:///`.

### Si usa modo local
Debe usar rutas locales coherentes, por ejemplo:
- entrada: `/content/input/archivo.csv`
- salida: `top_cities_local`

Y leer la salida con:
- `!ls`
- `!cat`
- `!head`

### Si usa modo HDFS
Debe usar rutas HDFS coherentes, por ejemplo:
- entrada: `/input/archivo.csv`
- salida: `/output/top_cities`

Y leer la salida con:
- `!hdfs dfs -ls`
- `!hdfs dfs -cat`

## Regla práctica
En Colab, si Pig está viejo o da guerra, suele ser más seguro:
- **hacer Pig en modo local**,
- y dejar HDFS/Hive para otras fases si el cuaderno lo requiere.

---

## 8. Regla de compatibilidad con Pig antiguo
En tus cuadernos se está usando una versión antigua de Pig.

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

## 9. Regla de plantillas Pig
Para la parte de Pig, el agente no debe improvisar desde cero si el caso encaja con una plantilla típica.

### Casos que debe tratar como plantillas adaptables
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
- y rutas.

### Qué no debe rehacer por completo
- la secuencia lógica del script,
- el patrón `LOAD → FILTER/FOREACH → GROUP → COUNT → ORDER → STORE`.

---

## 10. Regla de outputs en Pig
### Antes de usar `STORE`
El agente debe comprobar si la carpeta de salida ya existe.

Si existe, debe borrarla antes:
- en local: `!rm -rf carpeta_salida`
- en HDFS: `!hdfs dfs -rm -r -f /ruta_salida`

## Regla dura
No debe asumir que `STORE` sobrescribe.  
En Pig, normalmente la salida debe no existir.

---

## 11. Regla de lectura de resultados Pig
### Si el script usa `DUMP`
Sirve para ver el resultado por pantalla, pero no sustituye una salida persistida.

### Si el script usa `STORE`
Después debe:
- listar la salida,
- abrir el `part-*`,
- y comprobar que realmente contiene datos.

## Regla
No dar por buena la transformación solo porque el script “ha corrido”.  
Hay que comprobar la salida real.

---

## 12. Qué cambiar y qué no cambiar
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
- la secuencia simple de Pig cuando ya encaja con el problema.

---

## 13. Regla de adaptación por herramienta

### 13.1. Pandas
Pandas no resuelve UD3 por sí solo.  
Solo sirve para:
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

### 13.2. Pig
Pig es obligatorio para la fase de limpieza/transformación si el enunciado lo pide.

#### Plantilla base de limpieza
```pig
raw = LOAD 'ruta/archivo.csv' USING PigStorage(',')
AS (id:chararray, fecha:chararray, categoria:chararray, importe:chararray);

clean = FOREACH raw GENERATE
    (id IS NULL OR TRIM(id) == '' ? 'SIN_ID' : TRIM(id)) AS id,
    (fecha IS NULL OR TRIM(fecha) == '' ? '1900-01-01' : TRIM(fecha)) AS fecha,
    (categoria IS NULL OR TRIM(categoria) == '' ? 'SIN_CATEGORIA' : TRIM(categoria)) AS categoria,
    (importe IS NULL OR TRIM(importe) == '' ? '0' : TRIM(importe)) AS importe;

filtered = FILTER clean BY id != 'SIN_ID';

STORE filtered INTO 'out/archivo_clean' USING PigStorage(',');
```

#### Transformación interesante mínima
```pig
raw2 = LOAD 'ruta/archivo2.csv' USING PigStorage(',')
AS (grupo:chararray, valor:chararray);

valid = FILTER raw2 BY grupo IS NOT NULL AND TRIM(grupo) != '';

grp = GROUP valid BY grupo;
res = FOREACH grp GENERATE group AS grupo, COUNT(valid) AS total;
ordered = ORDER res BY total DESC;

DUMP ordered;
```

#### Regla
No copiar el schema sin revisar el orden real del CSV.

### 13.3. Spark / PySpark
Spark no sustituye Pig.  
Su papel principal en UD3 es:
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

### 13.4. Hive / HQL
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

## 14. Núcleos mínimos para principiante
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
- `LIMIT`

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

## 15. Traducción rápida de cada fase
- **Pandas**: “Miro qué está mal”.
- **Pig**: “Arreglo y transformo archivos”.
- **Spark**: “Convierto esos archivos en tablas”.
- **Hive/HQL**: “Consulto las tablas como si fuera SQL”.

---

## 16. Errores frecuentes que el agente debe vigilar

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
- mezclar modo local y lectura HDFS,
- `STORE` sobre una carpeta existente.

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

## 17. Qué debe comprobar antes de cada bloque
Antes de proponer cualquier bloque, el agente debe revisar:

- si el dataset entra por Kaggle, Drive, URL o local,
- si las rutas son reales,
- si los nombres de columnas existen,
- si la limpieza en Pig está justificada por Pandas,
- si Pig se va a ejecutar en local o HDFS,
- si la salida Pig ya existe,
- si Spark está leyendo el archivo correcto,
- y si las claves de join son realmente válidas.

---

## 18. Qué no puede hacer el agente en UD3
### Prohibido
- usar Kaggle como vía obligatoria si el enunciado no lo pide,
- copiar una versión fija de Java/Spark como si fuera universal,
- saltarse Pig si el punto exige Pig,
- mezclar Pig local con lectura posterior en HDFS,
- hacer limpieza fuerte sin haberla justificado con Pandas,
- consultar tablas que no ha validado,
- hacer joins por columnas con nombres parecidos pero sentido distinto,
- cargar una tabla y consultar otra,
- hablar del pipeline como teoría sin ejecutarlo realmente.

---

## 19. Señales de que UD3 está bien resuelto
El proceso está bien resuelto si:

- el dataset está bien identificado,
- la entrada es reproducible,
- Pandas ha detectado problemas reales,
- Pig corrige al menos 2 problemas,
- Pig hace una transformación útil,
- Pig usa un modo coherente (local o HDFS),
- la salida Pig se comprueba realmente,
- Spark carga tablas coherentes,
- Hive muestra las tablas creadas,
- hay al menos 2 consultas útiles,
- al menos una usa 2 tablas,
- y los resultados se validan.

---

## 20. Señales de que el agente se está equivocando
El agente está fallando si:

- quiere usar una sola herramienta para todo,
- no distingue inspección, limpieza, carga y consulta,
- copia bloques sin adaptar rutas y columnas,
- no revisa schemas,
- mezcla local y HDFS en Pig,
- da por buena una salida sin leerla,
- o consulta sin haber validado la carga.

---

## 21. Criterio final
En UD3 el agente debe actuar así:

- primero entiende el dataset,
- luego lo hace accesible,
- después lo revisa con Pandas,
- limpia y transforma con Pig,
- carga con Spark en Hive,
- consulta con HQL,
- valida,
- e interpreta.

## Regla final
En UD3, el flujo correcto es:

**entrada reproducible → Pandas → Pig → Spark → Hive → HQL → validación**.

Y en Pig:
**primero decidir modo (local o HDFS), luego ejecutar con rutas coherentes, luego comprobar la salida real**.
