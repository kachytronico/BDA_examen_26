# UD3 · BDA · Chuleta compacta para contexto de agente
## Ecosistema Hadoop · RA3

> Documento operativo para agente. No es resumen teórico. Sirve para ejecutar una variante de la tarea UD3 en Colab con el flujo: **Pandas → Pig → Spark → Hive → HQL**.

---

## 0. Prioridad de fuentes
1. Enunciado oficial UD3
2. Tarea resuelta UD3
3. Cuadernos de Pig / Spark / Hive / HQL
4. Teoría y resumen solo para aclarar

## 1. Qué manda en UD3
### Objetivo evaluable
Resolver un pipeline completo:
1. importar dataset,
2. revisar calidad con Pandas,
3. corregir y transformar con Pig,
4. cargar tablas con Spark en Hive,
5. consultar con HQL,
6. validar resultados.

### Herramientas núcleo
- **Pandas**: inspección rápida y detección de problemas.
- **Pig**: limpieza y transformación ETL.
- **PySpark**: lectura, tipado y carga a tablas.
- **Hive / HQL**: consultas finales.

### Rentable para examen
- detectar nulos, vacíos, fechas raras y tipos,
- corregir al menos 2 problemas con Pig,
- guardar tablas en Hive,
- hacer 2 consultas HQL con 2 tablas,
- explicar qué cambia según dataset.

---

## 2. Mapa mínimo de materiales
### Revisado
- Enunciado oficial UD3
- Teoría oficial UD3
- Resumen UD3

### Revisado parcialmente
- Tarea resuelta UD3

### Localizado / apoyo
- Cuaderno Apache Pig
- Cuadernos Spark
- Cuaderno importación a Hive con PySpark

### Huecos reales
- No todos los cuadernos están auditados completos.
- La solución UD3 mezcla rutas locales y HDFS: adaptar, no copiar a ciegas.

---

## 3. Pipeline obligatorio
### Paso 1 · Entender dataset
**Objetivo**: saber qué archivos hay, qué tabla representa cada uno y qué columnas unen tablas.

**Mirar**:
- nombre de archivos,
- columnas clave (`id`, `user_id`, `client_id`, `card_id`, etc.),
- si hay fechas, importes, nulos o vacíos.

**Señal de que está bien**: puedes explicar en 3-5 líneas qué archivo usarás y cómo se relaciona con otro.

---

### Paso 2 · Importar datos
**Objetivo**: descargar o cargar el dataset de forma reproducible.

**Bloque mínimo Kaggle**
```python
!pip install -q kaggle
from google.colab import files
files.upload()  # subir kaggle.json si hace falta

!mkdir -p ~/.kaggle
!cp kaggle.json ~/.kaggle/
!chmod 600 ~/.kaggle/kaggle.json

!kaggle datasets download -d usuario/dataset
!unzip -o dataset.zip -d data
!ls data
```

**Qué adaptar**:
- `usuario/dataset`
- nombre del zip
- carpeta destino

**No cambiar**:
- estructura de autenticación,
- descarga,
- descompresión,
- comprobación final.

**Error típico**: descargar bien pero leer luego una ruta distinta.

**Esto hace lo siguiente**: autentica Kaggle, baja el dataset, lo descomprime y enseña qué archivos reales tienes.

---

### Paso 3 · Revisar con Pandas
**Objetivo**: detectar problemas antes de limpiar.

**Bloque mínimo**
```python
import pandas as pd

ruta1 = 'data/archivo1.csv'
ruta2 = 'data/archivo2.csv'

df1 = pd.read_csv(ruta1)
df2 = pd.read_csv(ruta2)

df1.head()
df1.info()
df1.isnull().sum()

df2.head()
df2.info()
df2.isnull().sum()
```

**Mirar**:
- columnas vacías o con muchos nulos,
- fechas como texto raro,
- importes como string,
- ids duplicados o sospechosos,
- espacios vacíos `''`, `' '`, `'null'`, `'unknown'`.

**Bloques útiles**
```python
# vacíos raros por columna
(df1.astype(str).apply(lambda s: s.str.strip()).isin(['', 'null', 'None', 'nan'])).sum()

# revisar fechas
pd.to_datetime(df1['fecha'], errors='coerce')

# revisar columna numérica que viene como texto
pd.to_numeric(df1['importe'], errors='coerce')
```

**Error típico**: limpiar sin haber justificado qué está mal.

**Esto hace lo siguiente**: te dice qué columnas fallan y qué tendrás que corregir luego con Pig.

---

### Paso 4 · Detectar problemas de calidad
**Objetivo**: convertir observaciones en lista de acciones.

**Lista mínima que debe salir**
- columna X con nulos,
- columna Y con vacíos,
- fecha Z mal formateada,
- importe o cantidad guardado como texto,
- columnas sobrantes o nombres poco claros.

**Señal de que está bien**: puedes escribir “corregiré A y B con Pig; luego cargaré las tablas limpias en Spark/Hive”.

---

### Paso 5 · Limpiar y transformar con Pig
**Objetivo**: corregir al menos 2 problemas y hacer una transformación útil.

## 5.1. Plantilla base Pig
```pig
raw = LOAD 'data/archivo1.csv' USING PigStorage(',')
AS (id:chararray, fecha:chararray, categoria:chararray, importe:chararray);

clean_nulls = FOREACH raw GENERATE
    (id IS NULL OR TRIM(id) == '' ? 'SIN_ID' : TRIM(id)) AS id,
    (fecha IS NULL OR TRIM(fecha) == '' ? '1900-01-01' : TRIM(fecha)) AS fecha,
    (categoria IS NULL OR TRIM(categoria) == '' ? 'SIN_CATEGORIA' : TRIM(categoria)) AS categoria,
    (importe IS NULL OR TRIM(importe) == '' ? '0' : TRIM(importe)) AS importe;

filtered = FILTER clean_nulls BY id != 'SIN_ID';

STORE filtered INTO 'out/archivo1_clean' USING PigStorage(',');
```

**Qué adaptar**:
- ruta de entrada,
- esquema,
- reglas de sustitución,
- ruta de salida.

**No cambiar**:
- secuencia `LOAD -> FOREACH -> FILTER -> STORE`.

**Error típico**: definir tipos mal o usar nombres de columna que no coinciden con el CSV.

**Esto hace lo siguiente**: lee el CSV, reemplaza nulos y vacíos por valores controlados, filtra registros malos y guarda una versión limpia.

## 5.2. Transformación interesante con Pig
```pig
raw2 = LOAD 'data/archivo2.csv' USING PigStorage(',')
AS (grupo:chararray, valor:chararray);

valid = FILTER raw2 BY grupo IS NOT NULL AND TRIM(grupo) != '';

grp = GROUP valid BY grupo;

res = FOREACH grp GENERATE group AS grupo, COUNT(valid) AS total;

ordered = ORDER res BY total DESC;

DUMP ordered;
```

**Uso típico**:
- top categorías,
- conteo por usuario,
- ranking de estados,
- frecuencia de palabras o etiquetas.

**Error típico**: agrupar sin filtrar basura antes.

**Esto hace lo siguiente**: cuenta cuántas veces aparece cada valor de un campo y devuelve un ranking.

---

### Paso 6 · Preparar Spark
**Objetivo**: arrancar sesión con soporte Hive.

```python
!apt-get install openjdk-8-jdk-headless -qq > /dev/null
!wget -q https://dlcdn.apache.org/spark/spark-3.5.1/spark-3.5.1-bin-hadoop3.tgz
!tar xf spark-3.5.1-bin-hadoop3.tgz

import os
os.environ['JAVA_HOME'] = '/usr/lib/jvm/java-8-openjdk-amd64'
os.environ['SPARK_HOME'] = '/content/spark-3.5.1-bin-hadoop3'

!pip install -q pyspark findspark
import findspark
findspark.init()

from pyspark.sql import SparkSession
spark = SparkSession.builder \
    .appName('BDA_UD3') \
    .enableHiveSupport() \
    .getOrCreate()
```

**Qué adaptar**:
- versión si ya te dan otra base preparada.

**No cambiar**:
- `enableHiveSupport()` si luego vas a guardar tablas.

**Error típico**: arrancar Spark sin soporte Hive y luego fallar en `saveAsTable`.

**Esto hace lo siguiente**: deja preparado Spark para leer archivos y crear tablas consultables.

---

### Paso 7 · Cargar y tratar datos con PySpark
**Objetivo**: leer archivos limpios, revisar schema y preparar tablas.

```python
sdf1 = spark.read.csv('out/archivo1_clean', header=False, inferSchema=True)
sdf2 = spark.read.csv('data/archivo2.csv', header=True, inferSchema=True)

sdf1.printSchema()
sdf1.show(5, truncate=False)
```

## 7.1. Renombrar columnas
```python
sdf1 = sdf1.toDF('id', 'fecha', 'categoria', 'importe')
```

## 7.2. Corregir tipos
```python
from pyspark.sql.functions import col, regexp_replace, to_date

sdf1 = sdf1.withColumn('importe', regexp_replace(col('importe'), ',', '.').cast('double'))
sdf1 = sdf1.withColumn('fecha', to_date(col('fecha'), 'yyyy-MM-dd'))
```

## 7.3. Seleccionar y preparar
```python
sdf1 = sdf1.select('id', 'fecha', 'categoria', 'importe')
sdf2 = sdf2.select('id', 'otra_columna')
```

**Error típico**: confiar en `inferSchema` y no revisar qué tipo dejó realmente.

**Esto hace lo siguiente**: convierte el archivo limpio en DataFrame de Spark, corrige tipos y deja columnas listas para Hive.

---

### Paso 8 · Crear o poblar tablas en Hive
**Objetivo**: pasar de DataFrame a tabla consultable.

```python
spark.sql('CREATE DATABASE IF NOT EXISTS bda_ud3')
spark.sql('USE bda_ud3')

sdf1.write.mode('overwrite').saveAsTable('tabla1')
sdf2.write.mode('overwrite').saveAsTable('tabla2')

spark.sql('SHOW TABLES').show(truncate=False)
```

**Qué adaptar**:
- nombre de base,
- nombre de tablas.

**No cambiar**:
- crear base si no existe,
- usar `mode('overwrite')` si quieres rehacer la carga.

**Error típico**: guardar tablas sin haber elegido base de datos.

**Esto hace lo siguiente**: crea un espacio lógico en Hive y guarda tus DataFrames como tablas SQL.

---

### Paso 9 · Consultas HQL
**Objetivo**: demostrar que el pipeline termina en consulta útil.

## 9.1. Consulta simple
```python
spark.sql("""
SELECT categoria, COUNT(*) AS total
FROM tabla1
GROUP BY categoria
ORDER BY total DESC
""").show()
```

## 9.2. Consulta con 2 tablas
```python
spark.sql("""
SELECT t1.id, t1.categoria, t2.otra_columna
FROM tabla1 t1
JOIN tabla2 t2
  ON t1.id = t2.id
LIMIT 20
""").show()
```

## 9.3. Consulta con filtro + agregación
```python
spark.sql("""
SELECT t2.otra_columna, AVG(t1.importe) AS media_importe
FROM tabla1 t1
JOIN tabla2 t2
  ON t1.id = t2.id
WHERE t1.importe IS NOT NULL
GROUP BY t2.otra_columna
ORDER BY media_importe DESC
""").show()
```

**Error típico**: hacer join con columnas que no tienen el mismo significado.

**Esto hace lo siguiente**: cruza dos tablas y extrae una conclusión medible.

---

### Paso 10 · Validar resultados
**Objetivo**: comprobar que el pipeline no se ha roto.

```python
spark.sql('SELECT COUNT(*) FROM tabla1').show()
spark.sql('SELECT COUNT(*) FROM tabla2').show()
spark.sql('SELECT * FROM tabla1 LIMIT 5').show()
```

**Mirar**:
- que las tablas tengan filas,
- que el join devuelva algo,
- que los tipos finales tengan sentido,
- que no haya fechas o importes absurdos.

**Esto hace lo siguiente**: verifica que no has cargado tablas vacías ni unido columnas equivocadas.

---

### Paso 11 · Interpretar resultados
**Objetivo**: cerrar con una conclusión corta.

**Plantilla**
- Se importaron los archivos X e Y.
- Con Pandas se detectaron los problemas A y B.
- Con Pig se corrigieron esos problemas y se obtuvo la transformación C.
- Con Spark se cargaron las tablas D y E en Hive.
- Las consultas HQL muestran que …

---

### Paso 12 · Cierre del pipeline
**Señal de resolución completa**
- dataset explicado,
- importación reproducible,
- revisión con Pandas,
- limpieza y transformación con Pig,
- carga en Hive con Spark,
- 2 consultas HQL con 2 tablas,
- validación y conclusión.

---

## 4. Bloques núcleo para principiante
### Pandas
- `read_csv`: leer CSV.
- `head()`: ver primeras filas.
- `info()`: ver tipos y nulos.
- `isnull().sum()`: contar nulos.
- `to_datetime(..., errors='coerce')`: probar fechas.
- `to_numeric(..., errors='coerce')`: probar números.

### Pig
- `LOAD`: leer archivo.
- `FOREACH ... GENERATE`: transformar campos.
- `FILTER`: filtrar filas.
- `GROUP`: agrupar registros.
- `STORE`: guardar salida.
- `DUMP`: mostrar resultado.

### Spark
- `SparkSession`: punto de entrada.
- `read.csv`: leer archivo.
- `printSchema()`: ver estructura real.
- `withColumn`: transformar columnas.
- `select`: elegir columnas.
- `saveAsTable`: guardar tabla.

### Hive / HQL
- `CREATE DATABASE`: crear base lógica.
- `USE`: seleccionar base.
- `SELECT`: consultar.
- `WHERE`: filtrar.
- `GROUP BY`: agrupar.
- `JOIN`: unir tablas.

---

## 5. Traducción rápida de cada fase
- **Pandas**: “Miro qué está mal”.
- **Pig**: “Arreglo y transformo archivos”.
- **Spark**: “Convierto esos archivos en tablas”.
- **Hive/HQL**: “Consulto las tablas como si fuera SQL”.

---

## 6. Qué cambiar y qué no
### Cambia siempre
- rutas,
- nombres de archivos,
- nombres de columnas,
- reglas de limpieza,
- claves de join,
- nombres de base y tablas.

### Intenta no cambiar salvo necesidad
- orden del pipeline,
- revisión con Pandas antes de Pig,
- soporte Hive en Spark,
- validación final.

---

## 7. Errores frecuentes
### Importación
- **Error**: ruta mala.
- **Detectar**: archivo no encontrado.
- **Arreglo**: `!ls`, revisar carpeta real.

### Pandas
- **Error**: columna numérica como texto.
- **Detectar**: `info()` la deja como `object`.
- **Arreglo**: `pd.to_numeric(..., errors='coerce')`.

### Pig
- **Error**: esquema incorrecto.
- **Detectar**: resultados vacíos o campos desplazados.
- **Arreglo**: revisar separador y orden de columnas.

### Spark
- **Error**: schema mal inferido.
- **Detectar**: `printSchema()` deja `string` donde esperabas número o fecha.
- **Arreglo**: `cast`, `to_date`, `regexp_replace`.

### Hive
- **Error**: `saveAsTable` falla.
- **Detectar**: error de catálogo o soporte Hive.
- **Arreglo**: arrancar con `enableHiveSupport()` y seleccionar base.

### HQL
- **Error**: join vacío.
- **Detectar**: 0 filas.
- **Arreglo**: comprobar claves y tipos de unión.

### Copiar sin adaptar
- **Error**: el código corre pero no responde a tu dataset.
- **Detectar**: columnas inexistentes o resultados absurdos.
- **Arreglo**: renombrar y revisar todo bloque antes de ejecutar.

---

## 8. Cómo usar esta chuleta en examen
1. Mira archivos y columnas clave.
2. Detecta rápido dos problemas de calidad con Pandas.
3. Decide qué corregirás con Pig.
4. Limpia y guarda salida clara.
5. Carga la salida con Spark.
6. Guarda tablas en Hive.
7. Haz primero una consulta simple y luego una con join.
8. Valida conteos y coherencia.
9. Redacta una conclusión mínima.

### Orden rentable
1. importación,
2. Pandas,
3. Pig,
4. Spark/Hive,
5. HQL,
6. validación,
7. texto final.

---

## 9. Plantilla exprés
1. importar dataset
2. revisar con Pandas
3. detectar problemas
4. limpiar con Pig
5. cargar con Spark
6. guardar en Hive
7. consultar con HQL
8. validar
9. concluir

---

## 10. Estado de confianza
### Confirmado
- estructura oficial de la tarea,
- papel de Pandas, Pig, Spark, Hive y HQL,
- necesidad de 2 consultas con 2 tablas,
- patrón general del pipeline.

### Inferido razonable
- algunas reglas concretas de limpieza y nombres de columnas, porque dependen del dataset.

### Pendiente para afinar más
- auditoría completa de todos los cuadernos prácticos UD3.
