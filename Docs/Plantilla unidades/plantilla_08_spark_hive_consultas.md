# Plantilla 08 · Spark + Hive + Consultas HQL

> **Cuándo usar:** apartados 4 (Spark + Hive) y 5 (Consultas HQL) del enunciado UD3.
> **Requisitos previos:** plantillas 05, 06 (y opcionalmente 07) ya ejecutadas. Datos limpios disponibles en `salida_limpios/part-m-*` y CSVs originales en `input/`.
> **Cómo se inyecta:** el usuario pasa este `.md` por chat cuando el agente la pide.
> **Resultado esperado:** Spark instalado con soporte Hive, base de datos creada, tablas guardadas y validadas, **al menos 2 consultas HQL ejecutadas, una de ellas con JOIN entre 2 tablas**.

> **Cambios respecto a v1:** la celda de instalación de Spark usa `dlcdn.apache.org` (CDN oficial actual) en lugar de `archive.apache.org`, con verificación obligatoria del tamaño del `.tgz` antes de extraer. La versión de Spark se parametriza en una variable. `archive.apache.org` queda como fallback documentado.

---

## Por qué esta plantilla existe

La fase Spark + Hive de UD3 tiene cinco puntos de fallo conocidos que esta plantilla blinda:

1. **Instalación de Spark con `pip install pyspark`** → trae su propio Hadoop y rompe la coherencia con Hadoop 3.4.2 manual. La plantilla usa `wget .tgz + findspark`.
2. **Descarga truncada de `archive.apache.org`** → el mirror histórico aplica throttling y `wget -q` puede dejar un `.tgz` incompleto sin avisar (típicamente ~100 MB en lugar de ~400 MB). La plantilla usa `dlcdn.apache.org` (CDN oficial actual) con verificación de tamaño obligatoria.
3. **Metastore Derby corrupto entre sesiones** → `Failed to start database 'metastore_db'` en el primer `spark.sql` Hive. La plantilla limpia `metastore_db/` y `derby.log` antes del primer uso.
4. **Tablas en `default`** cuando el enunciado pide "base de datos Hive". La plantilla usa `CREATE DATABASE IF NOT EXISTS <base>` + `USE <base>` antes de `saveAsTable`.
5. **Consultas sobre tablas no validadas** → joins vacíos, columnas mal tipadas no detectadas. La plantilla obliga a `DESCRIBE` + `COUNT(*)` + `SELECT * LIMIT 3` por cada tabla antes de consultar.

---

## Celda Markdown 1 · Introducción a Spark + Hive

```markdown
## ⚡ Spark + Hive · Importación de ficheros a base de datos relacional

Aplicamos PySpark con soporte Hive para:

1. **Instalar Spark 3.5.x** desde el CDN oficial (compatible con Java 17 y Hadoop 3.4.2).
2. **Crear una sesión Spark** con `enableHiveSupport()` para usar el metastore Hive embebido.
3. **Limpiar el metastore Derby** anterior si hubiera quedado de una sesión previa.
4. **Crear la base de datos Hive** explícitamente y guardar las tablas dentro.
5. **Validar el contenido** de cada tabla (esquema + número de filas + 3 primeras filas).

Esta secuencia es obligatoria — saltarse cualquier paso provoca errores conocidos en Colab.
```

---

## Celda de código 1 · Instalación de Spark (con verificación)

```python
import os

# Versión de Spark a instalar.
# IMPORTANTE: dlcdn.apache.org sirve solo la VERSIÓN ACTUAL de cada rama 3.5.x.
# Si esta versión da 404, comprobar la disponible con:
#   !curl -s https://dlcdn.apache.org/spark/ | grep -oE 'spark-3\.5\.[0-9]+' | sort -u
# y actualizar SPARK_VERSION abajo.
SPARK_VERSION = "3.5.8"

# 1. Limpiar restos de intentos anteriores (idempotente)
!rm -rf /content/spark-3.5*-bin-hadoop3
!rm -f /content/spark-3.5*-bin-hadoop3.tgz

# 2. Descargar desde CDN oficial (sin throttling, descarga completa garantizada)
!wget https://dlcdn.apache.org/spark/spark-{SPARK_VERSION}/spark-{SPARK_VERSION}-bin-hadoop3.tgz

# 3. Verificar tamaño antes de extraer (Spark 3.5.x completo pesa ~400 MB)
size_mb = os.path.getsize(f"/content/spark-{SPARK_VERSION}-bin-hadoop3.tgz") / 1024 / 1024
print(f"\nTamaño descargado: {size_mb:.1f} MB")
assert size_mb > 350, f"Descarga truncada ({size_mb:.1f} MB). Reintentar."

# 4. Extraer al directorio /content/ explícitamente
!tar xf /content/spark-{SPARK_VERSION}-bin-hadoop3.tgz -C /content/

# 5. Verificar que el binario está donde esperamos
assert os.path.exists(f"/content/spark-{SPARK_VERSION}-bin-hadoop3/bin/spark-submit"), \
    "Spark no se extrajo correctamente"

# 6. Instalar findspark
!pip install -q findspark

# 7. Variables de entorno
os.environ["JAVA_HOME"]  = "/usr/lib/jvm/java-17-openjdk-amd64"
os.environ["SPARK_HOME"] = f"/content/spark-{SPARK_VERSION}-bin-hadoop3"

import findspark
findspark.init()

print(f"\n✅ Spark {SPARK_VERSION} instalado correctamente")
```

> **Si `dlcdn.apache.org` devuelve 404 con la versión indicada**, significa que esa versión ya no es la actual de la rama 3.5.x (el CDN solo sirve la última de cada rama). Dos opciones:
> - **Opción A (preferida):** ejecutar `!curl -s https://dlcdn.apache.org/spark/ | grep -oE 'spark-3\.5\.[0-9]+' | sort -u` y actualizar `SPARK_VERSION` con la que aparezca.
> - **Opción B (fallback):** usar `archive.apache.org` con `wget --tries=5 --timeout=120 --continue` (mirror histórico, más lento pero conserva versiones antiguas):
>   ```python
>   !wget --tries=5 --timeout=120 --continue \
>     https://archive.apache.org/dist/spark/spark-3.5.4/spark-3.5.4-bin-hadoop3.tgz \
>     -O /content/spark-3.5.4-bin-hadoop3.tgz
>   ```

---

## Celda de código 2 · Crear SparkSession con Hive

```python
from pyspark.sql import SparkSession

spark = (SparkSession.builder
    .appName("BDA_UD3")
    .config("spark.sql.warehouse.dir", "/content/spark-warehouse")
    .enableHiveSupport()
    .getOrCreate())

sc = spark.sparkContext  # útil si el cuaderno también usa RDDs

print("SparkSession iniciada correctamente.")
print("Versión de Spark:", spark.version)
```

---

## Celda Markdown 2 · Crear tablas en Hive

```markdown
### Crear base de datos y guardar tablas

Limpiamos el metastore Derby (puede haber quedado corrupto de una sesión anterior), creamos la base `[ADAPTAR_NOMBRE_BASE]` explícitamente y guardamos cada DataFrame como tabla Hive.

Los CSVs originales con cabecera (`input/<archivo>.csv`) se cargan con `header=True, inferSchema=True`. Los archivos limpiados por Pig (`salida_limpios/part-m-*`) **no tienen cabecera** y necesitan **schema explícito** porque `inferSchema` no funciona bien sobre los `part-m-*` (son CSVs sin metadatos de columnas).
```

---

## Celda de código 3 · Crear base, cargar tablas, guardar y validar

> **Adaptación obligatoria:**
> - `[ADAPTAR_NOMBRE_BASE]`: nombre de la base de datos Hive (ej: `bda`, `bda_<iniciales>`).
> - Lista de DataFrames a cargar: uno por cada tabla. Diferenciar **CSV original con cabecera** (`header=True, inferSchema=True`) vs **salida de Pig sin cabecera** (`header=False, schema=schema_X`).
> - Schema explícito de la salida de Pig: usar el formato string de Spark (`"col1 TIPO, col2 TIPO, ..."`).

```python
import os
import shutil

# 1. Limpiar metastore Derby (puede estar corrupto de sesiones anteriores)
#    Idempotente: si no existen los ficheros, no pasa nada
for p in ("metastore_db", "derby.log"):
    if os.path.exists(p):
        shutil.rmtree(p) if os.path.isdir(p) else os.remove(p)

# 2. Crear y usar la base de datos Hive explícita
spark.sql("CREATE DATABASE IF NOT EXISTS [ADAPTAR_NOMBRE_BASE]")
spark.sql("USE [ADAPTAR_NOMBRE_BASE]")

# 3. Cargar CSVs originales (CON cabecera)
[ADAPTAR_DF_1] = spark.read.csv("input/[ADAPTAR_ARCHIVO_1].csv", header=True, inferSchema=True)
[ADAPTAR_DF_2] = spark.read.csv("input/[ADAPTAR_ARCHIVO_2].csv", header=True, inferSchema=True)

# 4. Cargar salida de Pig (SIN cabecera, con schema explícito)
#    El schema usa formato Spark: "col1 TIPO, col2 TIPO, ..."
schema_[ADAPTAR_NOMBRE_TABLA_PIG] = "[ADAPTAR_SCHEMA_DE_LA_SALIDA_PIG]"
[ADAPTAR_DF_PIG] = spark.read.csv(
    "salida_limpios/part-m-*",
    header=False,
    schema=schema_[ADAPTAR_NOMBRE_TABLA_PIG]
)

# 5. Guardar como tablas en Hive (ya estamos en la base correcta tras el USE)
[ADAPTAR_DF_1].write.mode("overwrite").saveAsTable("[ADAPTAR_TABLA_1]")
[ADAPTAR_DF_2].write.mode("overwrite").saveAsTable("[ADAPTAR_TABLA_2]")
[ADAPTAR_DF_PIG].write.mode("overwrite").saveAsTable("[ADAPTAR_TABLA_PIG]")

# 6. Comprobar bases y tablas
print("--- Bases de datos Hive ---")
spark.sql("SHOW DATABASES").show()

print(f"--- Tablas en la base [ADAPTAR_NOMBRE_BASE] ---")
spark.sql("SHOW TABLES IN [ADAPTAR_NOMBRE_BASE]").show()
```

---

## Celda de código 4 · Validación obligatoria del contenido

> **Esta celda no se omite.** Sin validación, las consultas HQL pueden fallar por columnas mal tipadas, joins vacíos o tablas sin datos.

```python
# Validación del contenido de cada tabla
for tabla in ["[ADAPTAR_TABLA_1]", "[ADAPTAR_TABLA_2]", "[ADAPTAR_TABLA_PIG]"]:
    print(f"\n=== Tabla {tabla} ===")
    print("Esquema:")
    spark.sql(f"DESCRIBE {tabla}").show(truncate=False)
    print("Número de filas:")
    spark.sql(f"SELECT COUNT(*) AS total FROM {tabla}").show()
    print("Primeras 3 filas:")
    spark.sql(f"SELECT * FROM {tabla} LIMIT 3").show(truncate=False)
```

---

## Celda Markdown 3 · Consultas HQL

```markdown
## 🔍 Consultas HQL con dos tablas

Lanzamos **al menos 2 consultas HQL** sobre las tablas Hive creadas. **Al menos una debe involucrar 2 tablas con JOIN** (requisito explícito del enunciado).
```

---

## Celda de código 5 · Consulta HQL 1 (con JOIN entre 2 tablas)

> **Adaptación:** sustituir tablas, columnas y agregación por las que tenga sentido en el dataset real.

```python
# Consulta 1: [ADAPTAR_DESCRIPCION_CONSULTA_1] (Tablas: [ADAPTAR_TABLA_A] + [ADAPTAR_TABLA_B])
print("--- Consulta 1: [ADAPTAR_DESCRIPCION_CONSULTA_1] ---")
query1 = """
SELECT
    [ADAPTAR_ALIAS_A].[ADAPTAR_COL_GROUP],
    [ADAPTAR_AGREGACION] AS [ADAPTAR_NOMBRE_RESULTADO]
FROM
    [ADAPTAR_TABLA_A] [ADAPTAR_ALIAS_A]
JOIN
    [ADAPTAR_TABLA_B] [ADAPTAR_ALIAS_B]
ON
    [ADAPTAR_ALIAS_A].[ADAPTAR_CLAVE_A] = [ADAPTAR_ALIAS_B].[ADAPTAR_CLAVE_B]
GROUP BY
    [ADAPTAR_ALIAS_A].[ADAPTAR_COL_GROUP]
"""

spark.sql(query1).show()
```

---

## Celda de código 6 · Consulta HQL 2 (también con JOIN o GROUP BY agregada)

```python
# Consulta 2: [ADAPTAR_DESCRIPCION_CONSULTA_2]
print("--- Consulta 2: [ADAPTAR_DESCRIPCION_CONSULTA_2] ---")
query2 = """
SELECT
    [ADAPTAR_ALIAS_C].[ADAPTAR_COL_GROUP_2],
    COUNT([ADAPTAR_ALIAS_D].[ADAPTAR_COL_CONTAR]) AS num_eventos
FROM
    [ADAPTAR_TABLA_C] [ADAPTAR_ALIAS_C]
JOIN
    [ADAPTAR_TABLA_D] [ADAPTAR_ALIAS_D]
ON
    [ADAPTAR_ALIAS_C].[ADAPTAR_CLAVE_C] = [ADAPTAR_ALIAS_D].[ADAPTAR_CLAVE_D]
GROUP BY
    [ADAPTAR_ALIAS_C].[ADAPTAR_COL_GROUP_2]
ORDER BY
    num_eventos DESC
"""

spark.sql(query2).show()
```

---

## Reglas de uso

### Reglas duras
- **Prohibido `!pip install pyspark`** — trae su propio Hadoop y rompe la coherencia con Hadoop 3.4.2 manual de UD2/UD3. Siempre `wget .tgz + findspark`.
- **Mirror obligatorio:** `dlcdn.apache.org` (CDN oficial actual). Si da 404, comprobar la versión disponible (es la última estable de la rama 3.5) y actualizar `SPARK_VERSION`. **No usar** `archive.apache.org` salvo como fallback documentado, porque trunca descargas silenciosamente con `wget -q`.
- **Verificación obligatoria del tamaño descargado** (`assert size_mb > 350`). Sin este check, una descarga truncada deja el `.tgz` con ~100 MB y `tar xf` extrae carpetas vacías sin error visible.
- **`JAVA_HOME` y `SPARK_HOME` se fijan ANTES de `findspark.init()`**. Sin esto, `findspark` no encuentra Spark correctamente.
- **Limpieza Derby ANTES del primer `spark.sql` Hive**. Idempotente, no daña si los ficheros no existen. **No** ejecutarla después de haber hecho `CREATE DATABASE` o `saveAsTable` (deja Derby inconsistente). El orden correcto es: SparkSession → limpieza Derby → primer `spark.sql`.
- **`CREATE DATABASE IF NOT EXISTS <base>` + `USE <base>` antes de cualquier `saveAsTable`**. El enunciado pide "base de datos Hive", no `default`.
- **Validación obligatoria** entre `saveAsTable` y consultas HQL: `DESCRIBE` + `COUNT(*)` + `SELECT * LIMIT 3`.
- **Al menos una consulta HQL con JOIN entre 2 tablas** (requisito del enunciado). Sin esto, el apartado 5 está incompleto.

### Adaptaciones típicas
- **Versión de Spark:** la rama 3.5.x es la verificada para UD3 con Java 17. La versión patch concreta (3.5.4, 3.5.5, 3.5.8...) depende de cuál sirva `dlcdn.apache.org` el día del examen. **No saltar a Spark 4.x** sin pruebas: cambia Scala a 2.13 y los jars Hive embebidos pueden romper con metastore Derby.
- **Si las tablas vienen 100% de CSVs originales** (sin paso por Pig): usar `header=True, inferSchema=True` en todas. Eliminar el bloque del schema explícito.
- **Si vienen 100% de salida de Pig**: todas con `header=False, schema=...` y schema string específico.
- **Mezcla** (caso típico de UD3): unas con cabecera, otras sin. Diferenciar como hace la plantilla.
- **Schema explícito de salida de Pig:** seguir el orden y tipos del `STORE` de la plantilla 06. Ejemplo: si la plantilla 06 generó `id, date, client_id, card_id, amount, ...` con tipos `INT, STRING, INT, INT, DOUBLE, ...`, el schema string es `"id INT, date STRING, client_id INT, card_id INT, amount DOUBLE, ..."`.
- **Nombre de la base:** convención sugerida `bda` o `bda_<iniciales_alumno>`. Lo importante es que sea explícito.

### Qué NO cambiar
- La secuencia: instalación → SparkSession → limpieza Derby → CREATE DATABASE + USE → cargas → saveAsTable → validación → consultas.
- El uso de `dlcdn.apache.org` con verificación de tamaño.
- La validación intermedia (DESCRIBE + COUNT + LIMIT 3).
- El requisito de al menos una consulta con JOIN entre 2 tablas.

### Si la sesión Spark falla
- **`wget` devuelve 404** → la versión de `SPARK_VERSION` ya no está en `dlcdn`. Ejecutar `!curl -s https://dlcdn.apache.org/spark/ | grep -oE 'spark-3\.5\.[0-9]+' | sort -u` y actualizar.
- **Tamaño descargado < 350 MB** → conexión cortada. Reintentar el `wget` (probablemente glitch puntual). Si persiste, usar fallback de `archive.apache.org` con `--tries=5 --timeout=120 --continue`.
- **`Failed to start database 'metastore_db'`** → ejecutar la limpieza Derby (celda 3 paso 1).
- **`Database '<base>' not found`** al hacer `saveAsTable` → falta `CREATE DATABASE` + `USE`.
- **`Table or view not found`** al consultar → tabla no se guardó en la base correcta. Verificar con `SHOW TABLES IN <base>`.
- **`inferSchema` infiere todo como string** en la salida de Pig → no se usa schema explícito. Esta plantilla ya lo gestiona.

Si nada de esto resuelve, **avisar al usuario y detenerse**. No reintentar a ciegas (regla M015 de Pig también aplica aquí).
