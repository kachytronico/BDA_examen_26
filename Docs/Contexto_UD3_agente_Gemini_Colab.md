# CONTEXTO UD3 · AGENTE GEMINI EN COLAB
## BDA · UD3 / RA3 · Ecosistema Hadoop

## 0. Propósito
Documento de contexto operativo para que un agente en Google Colab resuelva una **variante realista de la tarea/examen de UD3**.

No usar como resumen teórico.
Usar como **guía de resolución**, **criterio de prioridades** y **repositorio de patrones mínimos reutilizables**.

## 1. Qué debe conseguir el agente en UD3
Resolver el pipeline completo exigido por la unidad:
1. importar el dataset de forma reproducible,
2. revisar calidad con Pandas,
3. corregir y transformar con Pig,
4. cargar tablas con Spark en Hive,
5. hacer al menos 2 consultas HQL con 2 tablas,
6. validar e interpretar resultados.

## 2. Qué manda y en qué orden
Cuando haya conflicto, seguir este orden:
1. **Enunciado oficial UD3**.
2. **Tarea resuelta UD3** y patrones realmente usados.
3. **Cuadernos del tutor** de Pig, Spark y Hive.
4. **Programación oficial del módulo**.
5. **Teoría y resumen UD3** solo para aclarar, no para inventar pasos.

### Regla crítica
- Si el enunciado exige una fase, esa fase es obligatoria aunque otra fuente la resuelva de otra manera.
- Si una técnica aparece en teoría pero no en enunciado ni en tarea resuelta, tratarla como secundaria.
- No copiar la tarea resuelta literal: usarla como **patrón**, no como respuesta cerrada.

## 3. Lectura correcta de UD3
UD3 no se resuelve como teoría suelta.
Se resuelve como una cadena:

**dataset → Pandas → Pig → Spark → Hive → HQL → validación**

### Papel de cada herramienta
- **Pandas**: inspección rápida, nulos, vacíos, tipos, fechas raras, importes como texto.
- **Pig**: limpieza y transformación ETL obligatoria.
- **PySpark**: lectura estructurada, tipado y carga a tablas.
- **Hive / HQL**: explotación final mediante consultas.

### Lo que no debe hacer el agente
- saltarse Pandas y limpiar a ciegas,
- usar Spark para sustituir la parte Pig si el enunciado pide Pig,
- cargar en Hive tablas distintas de las que ha descrito o limpiado,
- hacer consultas HQL sin comprobar antes claves de unión y tipos,
- copiar rutas o nombres de columnas de otra tarea si no existen en el dataset actual.

## 4. Qué entra de verdad en examen
### Obligatorio
- importación reproducible del dataset,
- revisión inicial con Pandas,
- corrección de **al menos 2 problemas** con Pig,
- una transformación interesante con Pig,
- carga de varias tablas en Hive con Spark,
- al menos **2 consultas HQL** que impliquen **2 tablas**,
- validación mínima del pipeline.

### Muy rentable
- nulos y vacíos,
- fechas mal formadas,
- importes con símbolos (`$`, `,`),
- `saveAsTable(...)`,
- `JOIN + GROUP BY + COUNT / AVG / SUM`,
- `REGEXP_REPLACE + CAST` cuando los importes llegan como texto.

### Secundario
- Sqoop, Flume, Impala, HBase, particionado y extras del ecosistema, salvo que el enunciado los fuerce.

## 5. Regla de adaptación al dataset
El agente debe asumir que **cambiarán los datos**, pero que el **pipeline no cambia**.

### Cambia casi siempre
- nombres de archivos,
- rutas,
- número de tablas,
- columnas problemáticas,
- claves de unión,
- filtros y métricas de las consultas.

### No debe cambiar salvo que el enunciado lo exija
- el orden del pipeline,
- Pandas antes de Pig,
- Pig antes de Spark/Hive,
- validación antes de concluir,
- uso de HQL con joins reales en la fase final.

## 6. Regla de importación: no casarse con un solo camino
El agente debe elegir la importación según el enunciado real:

### Si el enunciado da dataset local o ya subido
Priorizar lectura desde `/content/` o carpeta local equivalente.

### Si el enunciado pide importación automática
Usar Kaggle, gdown, URL directa o el mecanismo reproducible que corresponda.

### Regla
No imponer Kaggle si el examen entrega los CSV ya preparados.
No imponer subida manual si el enunciado exige descarga automática.

## 7. Pipeline operativo obligatorio

### Paso 1. Entender el dataset
Objetivo: identificar archivos, tablas y claves de relación.

Mirar:
- nombre de archivos,
- columnas comunes (`id`, `user_id`, `client_id`, `card_id`, etc.),
- si hay fechas, importes, nulos o vacíos.

Resultado mínimo: poder explicar qué archivo se relaciona con cuál.

---

### Paso 2. Importar datos
Objetivo: dejar los archivos accesibles de forma reproducible.

Patrones válidos:
- `files.upload()` / archivos ya en `/content/`,
- `gdown --id ...`,
- Kaggle,
- URL directa.

Comprobación mínima:
- listar carpeta,
- confirmar nombres reales de archivo.

---

### Paso 3. Revisar con Pandas
Objetivo: detectar problemas antes de limpiar.

Bloque mínimo:
```python
import pandas as pd

df = pd.read_csv("ruta.csv")
df.head()
df.info()
df.isnull().sum()
```

Mirar:
- nulls,
- vacíos (`''`, `'null'`, `'None'`, `'nan'`),
- fechas como texto raro,
- importes como string,
- ids sospechosos o duplicados.

---

### Paso 4. Listar problemas concretos
Objetivo: dejar explícito qué 2 o más campos vas a corregir con Pig.

Problemas típicos aceptables:
- nulos,
- vacíos,
- fechas mal formateadas,
- importes o cantidades como texto,
- columnas con basura o símbolos.

El agente debe dejarlo claro antes de escribir Pig.

---

### Paso 5. Corregir con Pig
Objetivo: hacer la limpieza ETL obligatoria.

Patrón fijo:
```pig
raw = LOAD 'ruta.csv' USING PigStorage(',') AS (...);
clean = FOREACH raw GENERATE ...;
STORE clean INTO 'salida' USING PigStorage(',');
```

Operaciones que más conviene reutilizar:
- sustituir nulos por valores por defecto,
- filtrar filas inválidas,
- normalizar fechas con `REPLACE`,
- extraer número de un importe con regex.

### Regla
Pig debe apoyarse en problemas observados antes con Pandas.
No inventar limpiezas que no se han justificado.

---

### Paso 6. Hacer una transformación interesante con Pig
Objetivo: demostrar tratamiento adicional, no solo limpieza.

Patrones seguros:
- top N categorías,
- top ciudades,
- conteos por usuario,
- ranking por frecuencia,
- agregación simple por grupo.

Patrón fijo:
```pig
grp = GROUP datos BY campo;
res = FOREACH grp GENERATE group AS clave, COUNT(datos) AS total;
ordered = ORDER res BY total DESC;
DUMP ordered;
```

---

### Paso 7. Preparar Spark
Objetivo: arrancar Spark para leer y guardar tablas.

Patrón recomendado:
```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .master("local[*]") \
    .appName("BDA03") \
    .enableHiveSupport() \
    .getOrCreate()
```

### Regla
Usar `enableHiveSupport()` como camino recomendado para no romper `saveAsTable(...)`.

---

### Paso 8. Leer con PySpark y revisar schema
Objetivo: convertir archivos en DataFrames utilizables.

Bloque mínimo:
```python
df_spark = spark.read.csv("ruta.csv", header=True, inferSchema=True)
df_spark.printSchema()
df_spark.show(5, truncate=False)
```

Mirar:
- si `inferSchema` dejó números como `string`,
- si las fechas siguen como texto,
- si hay columnas que conviene renombrar.

---

### Paso 9. Crear base y guardar tablas en Hive
Objetivo: persistir tablas consultables.

Bloque mínimo:
```python
spark.sql("CREATE DATABASE IF NOT EXISTS bd_examen")
spark.sql("USE bd_examen")

df1.write.mode("overwrite").saveAsTable("tabla1")
df2.write.mode("overwrite").saveAsTable("tabla2")
```

### Regla
Usar una base de datos propia y validar con `SHOW TABLES`.

---

### Paso 10. Hacer consultas HQL
Objetivo: cerrar el pipeline con consultas reales.

Consulta mínima simple:
```python
spark.sql("""
SELECT categoria, COUNT(*) AS total
FROM tabla1
GROUP BY categoria
ORDER BY total DESC
""").show()
```

Consulta mínima con 2 tablas:
```python
spark.sql("""
SELECT t1.id, t2.campo
FROM tabla1 t1
JOIN tabla2 t2 ON t1.id = t2.id
LIMIT 20
""").show()
```

Consulta útil si hay importes string:
```python
spark.sql("""
SELECT t.client_id,
       AVG(CAST(REGEXP_REPLACE(t.amount, '[$,]', '') AS FLOAT)) AS promedio
FROM transacciones t
JOIN usuarios u ON t.client_id = u.id
GROUP BY t.client_id
ORDER BY promedio DESC
""").show()
```

### Regla
Las 2 consultas deben implicar 2 tablas y tener sentido con el dataset real.

---

### Paso 11. Validar resultados
Objetivo: comprobar que las tablas no están vacías y que los joins tienen sentido.

Bloques mínimos:
```python
spark.sql("SHOW TABLES IN bd_examen").show()
spark.sql("SELECT COUNT(*) FROM bd_examen.tabla1").show()
spark.sql("SELECT * FROM bd_examen.tabla1 LIMIT 5").show()
```

---

### Paso 12. Interpretar y cerrar
Objetivo: explicar qué demuestra el pipeline.

Plantilla mínima:
- se importaron los archivos X e Y,
- con Pandas se detectaron A y B,
- con Pig se corrigieron esos problemas y se obtuvo C,
- con Spark se cargaron las tablas D y E,
- las consultas HQL muestran F.

## 8. Patrones de código que el agente debe priorizar

### Pandas
- `read_csv`
- `head`
- `info`
- `isnull().sum()`
- `to_datetime(..., errors='coerce')`
- `to_numeric(..., errors='coerce')`

### Pig
- `LOAD ... USING PigStorage`
- `FOREACH ... GENERATE`
- `FILTER`
- `GROUP`
- `COUNT`
- `STORE`
- `DUMP`
- ternario `cond ? a : b`

### Spark
- `SparkSession.builder`
- `read.csv`
- `printSchema`
- `withColumn`
- `regexp_replace`
- `cast`
- `saveAsTable`

### Hive / HQL
- `CREATE DATABASE`
- `USE`
- `SHOW TABLES`
- `SELECT`
- `JOIN`
- `GROUP BY`
- `ORDER BY`
- `REGEXP_REPLACE`
- `CAST`

## 9. Errores frecuentes que el agente debe vigilar
- importar en una ruta y leer en otra,
- mezclar rutas locales y HDFS sin control,
- limpiar con Pig un archivo distinto del que perfiló con Pandas,
- dejar `amount` como string y luego usar `AVG` o `SUM`,
- `JOIN` con columnas que no representan la misma entidad,
- tabla guardada en otra base o con otro nombre,
- confiar en `inferSchema` sin comprobar,
- copiar nombres de columnas de otra tarea.

## 10. Regla final para el agente
Resolver UD3 como una **cadena conectada**.

No presentar bloques sueltos.
Cada fase debe alimentar a la siguiente:
- Pandas justifica Pig,
- Pig prepara Spark,
- Spark prepara Hive,
- Hive/HQL cierra el análisis.

Si el dataset cambia, adaptar:
- rutas,
- esquema,
- columnas,
- joins,
- filtros.

No cambiar:
- el pipeline,
- el orden lógico,
- la necesidad de validar.
