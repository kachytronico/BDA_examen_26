# CONTEXTO DE AGENTE — BDA UD2 / RA2
Versión operativa para Gemini en Google Colab

## 0. Propósito
Este documento sirve como **contexto operativo** para resolver variantes de la **UD2 / RA2** del módulo BDA en Google Colab.

**Objetivo del agente:** ayudar a resolver una tarea o examen práctico de UD2 con un flujo controlado y útil para examen, usando HDFS, `grep`, MapReduce en Java y Hadoop Streaming en Python.

**No usar este documento como resumen teórico general.**
Usarlo como guía de resolución paso a paso, jerarquía de fuentes y repositorio de bloques mínimos adaptables.

---

## 1. Alcance
Unidad:
- **UD2 / RA2**
- **Título:** Almacenamiento y procesamiento en Hadoop

Tipo de ejercicio esperado:
- notebook en Colab,
- tiempo limitado,
- dataset nuevo o reutilizado de UD1,
- resolución práctica con este patrón:
  1. explicar dataset,
  2. decidir qué buscar con grep,
  3. subir a HDFS,
  4. ejecutar grep,
  5. resolver MapReduce en Java,
  6. resolver Hadoop Streaming en Python.

---

## 2. Jerarquía de fuentes obligatoria
Cuando haya que decidir qué manda, seguir este orden:

1. **Enunciado oficial de UD2**
2. **Cuadernos del tutor más útiles para la práctica**
   - `0201_HW_en_Colab_e_instalacion_de_Hadoop.ipynb`
   - `0202_Primer_ejemplo_de_uso_Hadoop.ipynb`
   - `0203_Hadoop_MapReduce.ipynb`
   - `0204_Guia_IntroMapReduce.ipynb`
3. **Tareas resueltas fiables de UD2 revisadas**
   - `BDA02 Tarea resuelta de Evaluación (Santiago).ipynb`
   - `BDA02_tarea_resuelta_DAF02.ipynb`
4. **Programación oficial del módulo**
5. **Teoría oficial y resumen**, solo para aclarar conceptos o justificar una decisión

### Regla fuerte
- Si una idea aparece en el enunciado y en los cuadernos/tareas, tratarla como **patrón prioritario**.
- Si aparece solo en teoría, tratarla como **apoyo secundario**.
- **No inventar pasos** que no estén respaldados por esas fuentes.
- `BBDA02_tarea_resuelta_MP02.ipynb` **no debe usarse como fuente núcleo** hasta revisión fiable por contenido.

---

## 3. Qué debe hacer el agente
El agente debe:

- seguir **exactamente** el orden real de la tarea,
- transformar el enunciado en una secuencia clara de ejecución,
- proponer bloques mínimos de comandos o código listos para copiar y adaptar,
- explicar qué parte del bloque cambia y cuál no,
- priorizar siempre lo que más ayuda a **resolver y terminar**,
- avisar de errores típicos de rutas, HDFS, outputs existentes, classpath, `mapper.py` y `reducer.py`,
- distinguir entre:
  - bloque fijo,
  - bloque adaptable,
  - y decisión que debe consultarse.

---

## 4. Qué no debe hacer el agente
El agente no debe:

- convertir UD2 en teoría decorativa sobre Hadoop,
- desviarse a Spark, Hive, Pig, HBase o herramientas fuera de la unidad,
- meter opciones complejas si existe una versión más simple que cumple,
- asumir que Hadoop ya está listo si no se ha validado,
- dar por buena una salida sin abrir `part-*`,
- mezclar rutas locales de Colab con rutas HDFS,
- usar notebooks o soluciones no verificadas como base principal,
- cambiar por su cuenta el objetivo del conteo.

---

## 5. Estructura real de la tarea (orden fijo)
El agente debe organizar cualquier ayuda de UD2 siguiendo siempre este orden:

1. **Explicación del dataset**
2. **Qué información se quiere extraer con grep**
3. **Operaciones para copiar los archivos a HDFS**
4. **Comandos grep + interpretación de resultados**
5. **Programa MapReduce en Java**
6. **Programa MapReduce en Python con Hadoop Streaming**

### Regla fuerte
Si el usuario pide una guía, solución o adaptación de examen de UD2, el agente debe respetar este orden aunque luego simplifique nombres.

---

## 6. Traducción operativa del flujo
Pipeline base para examen:

1. identificar fichero útil y patrón de búsqueda,
2. preparar Hadoop en Colab,
3. comprobar el archivo local,
4. crear estructura HDFS,
5. subir a HDFS,
6. verificar con `-ls`, `-head` o `-cat`,
7. hacer grep y explicarlo,
8. resolver Java MapReduce,
9. resolver Python Streaming,
10. validar salidas,
11. comparar resultados,
12. cerrar con conclusión breve.

---

## 7. Hipótesis operativa de examen
### Camino principal
Asumir como opción principal que el profesor puede dar el dataset **ya en CSV** y que se sube directamente a Colab (`/content/<nombre>.csv`).

### Regla de prioridad
- Si el enunciado del examen **obliga** a reutilizar el dataset de UD1 o a descargarlo de otra fuente, obedecer el enunciado.
- Si no lo especifica, priorizar la vía más simple y robusta:
  - archivo CSV en Colab,
  - `hdfs dfs -put` a HDFS,
  - trabajo desde HDFS.

---

## 8. Patrones reales que se repiten
Patrones que el agente debe reconocer como núcleo práctico:

- instalación mínima de Hadoop en Colab,
- definición de `JAVA_HOME`, `HADOOP_HOME` y `PATH`,
- creación de carpeta HDFS con `hdfs dfs -mkdir -p`,
- subida de ficheros con `hdfs dfs -put -f`,
- verificación con `hdfs dfs -ls`, `-head`, `-cat`,
- conteos con `grep` o con pipes sobre el contenido del CSV,
- limpieza previa de outputs HDFS antes de relanzar jobs,
- esqueleto Java con `Mapper`, `Reducer` y `main`,
- compilación con classpath de Hadoop,
- empaquetado en `.jar`,
- ejecución con `hadoop jar`,
- `mapper.py` que emite `clave\t1`,
- `reducer.py` que suma por clave,
- job de Hadoop Streaming con `-input`, `-output`, `-file`, `-mapper`, `-reducer`.

### Lo más rentable
- HDFS básico,
- grep bien interpretado,
- Java mínimo funcional,
- Streaming mínimo funcional,
- comprobación de outputs.

---

## 9. Bloques canónicos reutilizables

### 9.1. Subir CSV a Colab y localizarlo
```python
from google.colab import files
subido = files.upload()
nombre_csv = next(iter(subido))
print(nombre_csv)
```

Uso:
- cuando el dataset viene dado por el profesor y hay que dejarlo en `/content/`.

Cambiar:
- nada, salvo el nombre en usos posteriores.

No cambiar:
- la idea de comprobar el nombre real antes de seguir.

---

### 9.2. Preparar Hadoop en Colab
```bash
!wget https://downloads.apache.org/hadoop/common/hadoop-3.4.2/hadoop-3.4.2.tar.gz
!tar -xzf hadoop-3.4.2.tar.gz
!mv hadoop-3.4.2 /usr/local/hadoop
```

```python
import os
os.environ["JAVA_HOME"] = "/usr/lib/jvm/java-17-openjdk-amd64"
os.environ["HADOOP_HOME"] = "/usr/local/hadoop"
os.environ["PATH"] += f":{os.environ['HADOOP_HOME']}/bin:{os.environ['HADOOP_HOME']}/sbin"
```

```bash
!hadoop version
!hdfs --help
```

Cambiar:
- versión de Hadoop si fuera necesario.

No cambiar:
- secuencia instalar → exportar variables → validar.

---

### 9.3. Crear carpeta HDFS y subir archivo
```bash
!hdfs dfs -mkdir -p /input
!hdfs dfs -put -f /content/archivo.csv /input/
!hdfs dfs -ls /input
```

Cambiar:
- `archivo.csv`, carpeta HDFS.

No cambiar:
- validar con `-ls` justo después.

---

### 9.4. Verificar contenido en HDFS
```bash
!hdfs dfs -head /input/archivo.csv
!hdfs dfs -cat /input/archivo.csv | head -20
```

Cambiar:
- nombre del fichero.

No cambiar:
- mirar cabecera y separador antes del grep o del mapper.

---

### 9.5. grep simple
```bash
!hdfs dfs -cat /input/archivo.csv | grep -o 'USA' | wc -l
```

Cambiar:
- patrón y fichero.

No cambiar:
- explicar qué representa el conteo.

---

### 9.6. grep con jar de ejemplos de Hadoop
```bash
!hdfs dfs -rm -r -f /output_grep
!hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar \
  grep /input /output_grep 'USA'
!hdfs dfs -cat /output_grep/part-r-00000
```

Cambiar:
- patrón, input, output.

No cambiar:
- borrar output antes de reejecutar.

---

### 9.7. Compilar y ejecutar Java MapReduce
```bash
!javac -classpath `hadoop classpath` -d wc_classes WordCount.java
!jar -cvf wordcount.jar -C wc_classes/ .
!hdfs dfs -rm -r -f /output_java
!hadoop jar wordcount.jar WordCount /input/archivo.csv /output_java
!hdfs dfs -cat /output_java/part-r-00000 | head
```

Cambiar:
- `WordCount.java`, `WordCount`, input, output.

No cambiar:
- limpiar output y leer `part-r-00000` al final.

---

### 9.8. mapper.py mínimo
```python
#!/usr/bin/env python3
import sys

for line in sys.stdin:
    tokens = line.strip().split(",")
    for token in tokens:
        token = token.strip()
        if token:
            print(f"{token}\t1")
```

Cambiar:
- delimitador,
- tokenización,
- salto de cabecera si hace falta.

No cambiar:
- emitir `clave\t1`.

---

### 9.9. reducer.py mínimo
```python
#!/usr/bin/env python3
import sys

current_key = None
current_sum = 0

for line in sys.stdin:
    key, value = line.strip().split("\t")
    value = int(value)
    if current_key == key:
        current_sum += value
    else:
        if current_key is not None:
            print(f"{current_key}\t{current_sum}")
        current_key = key
        current_sum = value

if current_key is not None:
    print(f"{current_key}\t{current_sum}")
```

Cambiar:
- casi nunca, salvo si la lógica deja de ser una suma.

No cambiar:
- acumulación por cambio de clave.

---

### 9.10. Lanzar Hadoop Streaming
```bash
!chmod +x mapper.py reducer.py
!hdfs dfs -rm -r -f /output_py
!hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*.jar \
  -input /input/archivo.csv \
  -output /output_py \
  -mapper mapper.py \
  -reducer reducer.py \
  -file mapper.py \
  -file reducer.py

!hdfs dfs -cat /output_py/part-00000 | head
```

Cambiar:
- input, output y scripts si cambian de nombre.

No cambiar:
- `-file` y lectura final de `part-00000`.

Si hay problemas de ejecución del script, usar:
```bash
-mapper 'python3 mapper.py' -reducer 'python3 reducer.py'
```

---

## 10. Reglas de adaptación al examen
### Mirar primero
1. qué archivos hay,
2. qué archivo contiene el texto o categoría útil,
3. qué patrón tiene sentido,
4. qué separador real usa el CSV,
5. si hay cabecera que conviene saltar.

### Cambia casi siempre
- nombre del dataset,
- ruta local,
- ruta HDFS,
- patrón de grep,
- delimitador,
- columna o token que se quiere contar,
- outputs.

### No suele cambiar
- secuencia general,
- necesidad de HDFS,
- limpieza de outputs previos,
- estructura `map -> reduce -> output`,
- validación leyendo `part-*`.

### Estrategia de tiempo
- primero dejar Hadoop operativo,
- luego HDFS,
- luego grep,
- luego Java mínimo,
- luego Streaming mínimo,
- al final validar y concluir.

---

## 11. Errores frecuentes
- mezclar `/content/...` con rutas HDFS,
- no crear `/input` antes del `put`,
- output ya existente,
- classpath Java incorrecto,
- clase principal distinta del nombre lanzado,
- `mapper.py` emitiendo mal el tabulador,
- `reducer.py` rompiendo por formato inesperado,
- contar la cabecera como dato,
- validar solo que existe la carpeta y no abrir el `part-*`.

---

## 12. Regla de uso para Gemini
Cuando Gemini use este documento:

1. debe seguir el orden fijo de la tarea,
2. debe reutilizar primero estos bloques canónicos,
3. debe adaptar solo rutas, nombres, patrones y tokenización,
4. debe explicar siempre qué parte del bloque cambia,
5. debe evitar teoría decorativa,
6. debe consultar si hay una decisión delicada sobre:
   - qué archivo usar,
   - qué patrón buscar,
   - qué columna contar,
   - si saltar cabecera,
   - si el delimitador no está claro,
   - si los resultados de Java y Python no coinciden,
7. no debe usar soluciones no verificadas como base,
8. no debe introducir herramientas fuera de UD2.

---

## 13. Idea fuerza final
UD2 debe pensarse como una cadena práctica corta y repetible:

**dataset -> HDFS -> grep -> Java MR -> Streaming Python -> validación**

Si el agente controla bien esa secuencia y evita inventar rutas o lógica innecesaria, tiene el núcleo rentable de examen.
