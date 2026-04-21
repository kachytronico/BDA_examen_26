# UD2 BDA · Chuleta compacta para contexto de agente

## 1. Propósito
Documento de contexto para resolver variantes de la **tarea/examen práctico de UD2** en **Google Colab**.

**Meta operativa**:
- identificar dataset y objetivo,
- preparar Hadoop en Colab,
- copiar archivos a HDFS,
- hacer conteo simple con `grep`,
- ejecutar MapReduce en **Java**,
- ejecutar MapReduce en **Python con Hadoop Streaming**,
- validar y comparar resultados.

**No usar como resumen teórico general.**
Usar como guía lineal de resolución y repositorio de bloques mínimos reutilizables.

---

## 2. Prioridad de fuentes
### Confirmado y prioritario
1. **Enunciado oficial UD2** → define exactamente qué se pide.
2. **Tareas resueltas UD2 revisadas por contenido**:
   - `BDA02 Tarea resuelta de Evaluación (Santiago).ipynb`
   - `BDA02_tarea_resuelta_DAF02.ipynb`
3. **Programación oficial del módulo** → fija RA2 y peso práctico de HDFS/MapReduce.
4. **Teoría oficial UD2** → solo para aclarar HDFS, YARN, MapReduce, comandos y estructura.
5. **Resumen UD2** → apoyo rápido.

### Parcial / localizado
- `0201_HW_en_Colab_e_instalacion_de_Hadoop.ipynb` → localizado.
- `0202_Primer_ejemplo_de_uso_Hadoop.ipynb` → localizado.
- `0203_Hadoop_MapReduce.ipynb` → recomendado explícitamente por el enunciado; función confirmada, contenido no revisado completo.
- `0204_Guia_IntroMapReduce.ipynb` → localizado.
- `BBDA02_tarea_resuelta_MP02.ipynb` → no usar como núcleo hasta revisión adicional.

### Regla de uso
Cuando haya conflicto, mandar:
**enunciado > tareas resueltas reales > teoría/resumen**.

---

## 3. Qué entra de verdad en UD2
### Obligatorio
- HDFS básico: crear carpetas, subir archivos, listar, leer salida.
- Terminal básica en Colab.
- Elegir archivo y patrón de búsqueda.
- Conteo simple con `grep`.
- Job MapReduce en Java.
- Job MapReduce en Python con Hadoop Streaming.
- Validar rutas, outputs y conclusiones.

### Recomendable
- Explicar por qué eliges ese archivo y no otro.
- Explicar qué cuenta exactamente cada bloque.
- Comparar salidas Java vs Streaming.
- Limpiar outputs previos antes de relanzar jobs.

### Secundario
- Historia/origen de Hadoop.
- Ecosistema ampliado no usado en la tarea.
- Detalles teóricos de YARN más allá de saber que ejecuta jobs.

---

## 4. Estructura real de la tarea
La tarea oficial pide, en este orden:
1. **Explicar el dataset**.
2. **Decidir qué extraer con grep**.
3. **Copiar archivos a HDFS**.
4. **Contar con grep e interpretar**.
5. **Resolver MapReduce en Java**.
6. **Resolver MapReduce en Python Streaming**.

### Traducción operativa
- Primero contextualizas.
- Después montas entorno y rutas.
- Luego subes a HDFS.
- Después haces un conteo simple y lo explicas.
- Luego haces el conteo general con Java.
- Luego haces el mismo tipo de lógica con Streaming Python.
- Al final validas y comparas.

---

## 5. Pipeline canónico de resolución
### Paso 1. Identificar dataset y archivo útil
**Objetivo**: elegir el fichero correcto para el análisis.

Mirar:
- nombres de archivos,
- columnas,
- primeras líneas,
- si es texto legible,
- si tiene cabecera.

**Cambia**:
- nombre de archivo,
- palabra/patrón,
- columna útil.

**Error típico**: analizar un fichero donde el patrón no aparece o no tiene sentido.

### Paso 2. Preparar Hadoop en Colab
**Objetivo**: disponer de Java + Hadoop + variables de entorno.

Mirar:
- versión de Hadoop,
- ruta de instalación,
- `PATH`, `JAVA_HOME`, `HADOOP_HOME`.

**Cambia**:
- versión y ruta local.

**No cambia**:
- lógica general de instalación.

### Paso 3. Revisar archivos locales
**Objetivo**: confirmar que el dataset existe antes de subirlo.

Mirar:
- ruta local exacta,
- extensión,
- cabecera,
- delimitador.

### Paso 4. Crear estructura HDFS
**Objetivo**: preparar carpeta de entrada y, si hace falta, limpiar outputs previos.

Mirar:
- si el directorio ya existe,
- si el output anterior sigue en HDFS.

### Paso 5. Subir a HDFS
**Objetivo**: copiar desde local a HDFS.

Mirar:
- fichero origen,
- directorio HDFS destino.

### Paso 6. Verificar HDFS
**Objetivo**: no seguir con rutas falsas.

Mirar:
- `hdfs dfs -ls`,
- `hdfs dfs -cat` o `-head`.

### Paso 7. Hacer conteo simple con grep
**Objetivo**: demostrar extracción rápida por patrón.

Mirar:
- palabra exacta,
- mayúsculas/minúsculas,
- si quieres coincidencia parcial o exacta.

### Paso 8. Interpretar grep
**Objetivo**: convertir un número en una conclusión útil.

Mirar:
- qué representa el número,
- si el patrón estaba bien elegido,
- si el archivo correcto era ese.

### Paso 9. Ejecutar Java MapReduce
**Objetivo**: contar palabras/valores con job Java.

Mirar:
- archivo `.java`,
- compilación,
- jar,
- input y output HDFS.

### Paso 10. Ejecutar Python Streaming
**Objetivo**: resolver el mismo problema con `mapper.py` + `reducer.py`.

Mirar:
- rutas de scripts,
- permisos,
- jar de streaming,
- input/output.

### Paso 11. Validar salidas
**Objetivo**: comprobar que hay datos, no carpetas vacías ni errores silenciosos.

Mirar:
- `part-r-00000` o equivalente,
- logs si no hay salida,
- si el output ya existía.

### Paso 12. Comparar y cerrar
**Objetivo**: dejar constancia de que Java y Python resuelven el objetivo.

Mirar:
- coincidencias básicas,
- diferencias por lógica del mapper,
- si ambos leen el mismo archivo.

---

## 6. Bloques canónicos reutilizables

### 6.1 Revisión inicial del dataset
**Para qué sirve**: ver qué archivos tienes y revisar primeras líneas.

```bash
!ls
!head -n 5 archivo.csv
```

**Esto hace lo siguiente**: lista archivos locales y enseña las primeras 5 líneas del archivo para confirmar contenido y cabecera.

**Qué cambiar**: `archivo.csv`.
**Qué no cambiar**: la lógica de revisar antes de procesar.

---

### 6.2 Preparación mínima de entorno Hadoop
**Para qué sirve**: instalar/descomprimir Hadoop y preparar variables.

```bash
!wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
!tar -xzf hadoop-3.3.6.tar.gz

import os
os.environ["HADOOP_HOME"] = "/content/hadoop-3.3.6"
os.environ["PATH"] += ":/content/hadoop-3.3.6/bin:/content/hadoop-3.3.6/sbin"
```

**Esto hace lo siguiente**: descarga Hadoop, lo descomprime y añade sus ejecutables al entorno de Colab.

**Qué cambiar**: versión/ruta.
**Qué no cambiar**: definir `HADOOP_HOME` y ampliar `PATH`.

---

### 6.3 Crear carpeta en HDFS
**Para qué sirve**: preparar la ruta de entrada.

```bash
!hdfs dfs -mkdir -p /input
```

**Esto hace lo siguiente**: crea la carpeta `/input` en HDFS; `-p` evita fallo si ya existe parte de la ruta.

**Qué cambiar**: `/input`.
**Qué no cambiar**: usar una carpeta clara y estable.

---

### 6.4 Limpiar salida previa en HDFS
**Para qué sirve**: evitar fallo por output existente.

```bash
!hdfs dfs -rm -r -f /output_java
!hdfs dfs -rm -r -f /output_py
```

**Esto hace lo siguiente**: borra resultados anteriores para poder relanzar jobs.

**Qué cambiar**: nombres de directorios de salida.
**Qué no cambiar**: limpiar antes de relanzar.

---

### 6.5 Subir archivo local a HDFS
**Para qué sirve**: copiar el dataset a HDFS.

```bash
!hdfs dfs -put -f archivo.csv /input/
```

**Esto hace lo siguiente**: copia `archivo.csv` desde local a `/input/` en HDFS; `-f` fuerza sobrescritura si ya existía.

**Qué cambiar**: `archivo.csv`, `/input/`.
**Qué no cambiar**: verificar antes que el archivo local existe.

---

### 6.6 Listar y leer desde HDFS
**Para qué sirve**: comprobar que el archivo está realmente en HDFS.

```bash
!hdfs dfs -ls /input
!hdfs dfs -cat /input/archivo.csv | head
```

**Esto hace lo siguiente**: lista el contenido de `/input` y enseña las primeras líneas del archivo dentro de HDFS.

**Qué cambiar**: ruta y nombre.
**Qué no cambiar**: comprobar HDFS antes del siguiente paso.

---

### 6.7 grep simple / conteo por patrón
**Para qué sirve**: contar coincidencias rápidas.

```bash
!hdfs dfs -cat /input/archivo.csv | grep -o 'USA' | wc -l
```

**Esto hace lo siguiente**: lee el archivo de HDFS, extrae cada coincidencia de `USA` y cuenta cuántas hay.

**Qué cambiar**: ruta y patrón (`USA`, `ACTOR`, `DIRECTOR`, etc.).
**Qué no cambiar**: validar antes que el patrón existe en el archivo.

**Alternativa frecuente en las tareas**: usar el jar de ejemplos de Hadoop para `grep`.

```bash
!hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep /input /output_grep 'USA'
!hdfs dfs -cat /output_grep/part-r-00000
```

**Esto hace lo siguiente**: ejecuta un job de ejemplo de Hadoop que busca el patrón en HDFS y deja el resultado en `/output_grep`.

---

### 6.8 Estructura mínima Java MapReduce
**Para qué sirve**: contar palabras o valores con Java.

```java
public static class TokenizerMapper extends Mapper<Object, Text, Text, IntWritable> {
    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context) throws IOException, InterruptedException {
        String[] tokens = value.toString().split(",");
        for (String token : tokens) {
            word.set(token.trim());
            context.write(word, one);
        }
    }
}

public static class IntSumReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
    public void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) sum += val.get();
        context.write(key, new IntWritable(sum));
    }
}
```

**Esto hace lo siguiente**:
- `map` lee cada línea,
- la divide,
- emite pares `clave,1`,
- `reduce` suma todos los `1` de cada clave.

**Qué cambiar**:
- separador,
- columna o token que quieres contar,
- limpieza de texto.

**Qué no cambiar**:
- la lógica `emitir clave,1` en `map` y `sumar` en `reduce` cuando haces wordcount/conteo.

---

### 6.9 Compilar y ejecutar Java
**Para qué sirve**: convertir el `.java` en job ejecutable.

```bash
!javac -classpath `$HADOOP_HOME/bin/hadoop classpath` -d wc_classes WordCount.java
!jar -cvf wordcount.jar -C wc_classes/ .
!hdfs dfs -rm -r -f /output_java
!hadoop jar wordcount.jar WordCount /input/archivo.csv /output_java
!hdfs dfs -cat /output_java/part-r-00000 | head
```

**Esto hace lo siguiente**:
- compila el código Java,
- crea el `.jar`,
- borra output previo,
- ejecuta el job,
- lee la salida.

**Qué cambiar**:
- `WordCount.java`, `WordCount`, input, output.

**Qué no cambiar**:
- limpiar el output antes,
- leer `part-r-00000` al final.

---

### 6.10 mapper.py mínimo
**Para qué sirve**: emitir `clave\t1` en Streaming.

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

**Esto hace lo siguiente**: lee entrada estándar línea a línea, separa tokens y saca cada token acompañado de `1`.

**Qué cambiar**:
- delimitador,
- qué token exacto emites,
- limpieza.

**Qué no cambiar**:
- imprimir pares `clave\t1`.

---

### 6.11 reducer.py mínimo
**Para qué sirve**: sumar valores de la misma clave.

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

**Esto hace lo siguiente**: recibe pares ya agrupados/ordenados por clave y suma todos los valores de cada clave.

**Qué cambiar**: casi nada en un conteo básico.
**Qué no cambiar**: lógica de acumulación y cambio de clave.

---

### 6.12 Lanzar Hadoop Streaming
**Para qué sirve**: ejecutar mapper y reducer en Python sobre HDFS.

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

**Esto hace lo siguiente**:
- da permisos a los scripts,
- limpia el output,
- ejecuta el jar de streaming,
- manda el archivo HDFS al mapper,
- recoge su salida en el reducer,
- deja resultado final en HDFS.

**Qué cambiar**: input, output, nombre de scripts si cambian.
**Qué no cambiar**: `-file` para enviar scripts al job.

---

### 6.13 Comparar resultados
**Para qué sirve**: cerrar el ejercicio con validación.

```bash
!hdfs dfs -cat /output_java/part-r-00000 | head
!hdfs dfs -cat /output_py/part-00000 | head
```

**Esto hace lo siguiente**: enseña una muestra de ambas salidas para comprobar que cuentan de forma coherente.

---

## 7. Explicación mínima para principiantes
### Terminal / bash
- `!` en Colab: ejecuta un comando de terminal.
- `|` pipe: manda la salida del comando de la izquierda al de la derecha.
- `head`: enseña primeras líneas.
- `wc -l`: cuenta líneas.

### HDFS
- `hdfs dfs -mkdir -p`: crea carpetas.
- `hdfs dfs -put`: sube archivo local a HDFS.
- `hdfs dfs -ls`: lista archivos en HDFS.
- `hdfs dfs -cat`: imprime contenido desde HDFS.
- `hdfs dfs -rm -r -f`: borra rutas en HDFS.

### Java MapReduce
- `Mapper`: transforma cada línea en pares `clave,valor`.
- `Reducer`: agrupa por clave y combina valores.
- `main`: configura y lanza el job.

### Streaming Python
- `mapper.py`: lee entrada estándar y emite pares.
- `reducer.py`: suma o agrega pares por clave.
- `stdin`: lo que entra al script.
- `stdout`: lo que imprime el script y consume Hadoop.

### Diferencia entre grep, Java y Streaming
- `grep`: conteo rápido de un patrón concreto.
- Java MapReduce: solución más formal y más pesada, pero estándar de la tarea.
- Streaming Python: misma idea MapReduce con lógica más fácil de adaptar si no dominas Java.

---

## 8. Reglas de adaptación al examen
### Mirar primero
1. qué archivos hay,
2. qué archivo tiene el texto útil,
3. qué palabra/patrón tiene sentido,
4. si el delimitador es coma, tabulador u otro.

### Cambia casi siempre
- nombre del dataset,
- ruta local,
- ruta HDFS,
- patrón de `grep`,
- delimitador,
- token o columna que quieres contar,
- nombre de outputs.

### Cambia a veces
- lógica del `mapper` si no quieres contar todas las palabras sino solo una columna.
- limpieza de cabecera o valores vacíos.

### No suele cambiar
- secuencia general,
- necesidad de HDFS,
- necesidad de limpiar outputs,
- estructura `map -> reduce -> output`,
- lectura final desde `part-*`.

### Estrategia de tiempo
- primero deja funcionando HDFS,
- luego resuelve `grep`,
- luego Java mínimo,
- luego Streaming mínimo,
- al final compara y redacta.

---

## 9. Errores frecuentes
### Ruta local mal escrita
**Se detecta**: `No such file or directory`.
**Arreglo**: revisar `ls` y copiar la ruta exacta.

### Archivo no está en HDFS
**Se detecta**: `hdfs dfs -ls` no lo muestra.
**Arreglo**: repetir `-put` y volver a listar.

### Output ya existe
**Se detecta**: Hadoop falla al lanzar el job.
**Arreglo**: borrar con `hdfs dfs -rm -r -f /output_x`.

### grep no devuelve nada
**Se detecta**: conteo `0` o salida vacía.
**Arreglo**: revisar mayúsculas, patrón, archivo y cabecera.

### Compilación Java falla
**Se detecta**: error de `javac`.
**Arreglo**: revisar imports, nombre de clase, llaves y classpath.

### Job Java no genera salida
**Se detecta**: no aparece `part-r-00000`.
**Arreglo**: revisar input, output, nombre de clase principal y logs.

### Streaming falla por scripts
**Se detecta**: output vacío o error en job.
**Arreglo**: revisar `chmod +x`, `-file`, formato tabulado y `split("\t")`.

### reducer.py rompe por líneas mal formadas
**Se detecta**: `ValueError` al dividir o convertir.
**Arreglo**: asegurar que el mapper imprime `clave\t1` siempre igual.

### Copiar código sin adaptar
**Se detecta**: el job corre pero cuenta cosas sin sentido.
**Arreglo**: revisar patrón, delimitador, archivo y objetivo antes de ejecutar.

---

## 10. Checklist exprés
1. revisar dataset,
2. elegir archivo útil,
3. decidir patrón de grep,
4. preparar Hadoop,
5. subir a HDFS,
6. verificar HDFS,
7. lanzar grep,
8. interpretar,
9. compilar/ejecutar Java,
10. ejecutar Streaming Python,
11. validar outputs,
12. comparar y cerrar.

---

## 11. Prioridad final
### Imprescindible para aprobar
- HDFS básico,
- grep funcional,
- Java MapReduce mínimo,
- Streaming Python mínimo,
- validación de resultados.

### Más rentable para copiar y adaptar
- `hdfs dfs -put/-ls/-cat/-rm -r -f`,
- plantilla Java `map + reduce + main`,
- `mapper.py`, `reducer.py`,
- comando de streaming completo.

### Debes entender sí o sí
- qué archivo analizas,
- qué cuenta cada bloque,
- qué entra al mapper,
- qué suma el reducer,
- por qué falla un output existente.

### Depende del dataset
- patrón de grep,
- columna/token útil,
- delimitador,
- limpieza de cabecera,
- nombre de rutas y archivos.

### Conviene dejar preparado antes del examen
- plantilla Java funcional,
- `mapper.py` y `reducer.py` base,
- secuencia HDFS,
- comando de streaming,
- nombres estándar de carpetas (`/input`, `/output_java`, `/output_py`).

---

## 12. Estado de confianza
### Confirmado
- estructura de la tarea,
- peso real de HDFS + grep + Java + Streaming,
- recomendación del cuaderno `0203_Hadoop_MapReduce`,
- patrones de instalación/uso/HDFS/Java/Streaming presentes en dos tareas resueltas revisadas.

### Inferido razonablemente
- unificación de rutas, nombres de carpetas y bloques para hacer la chuleta más estable.
- compactación de la lógica Java/Python a una forma canónica de examen.

### Pendiente para afinar más
- revisión interna completa de los cuadernos `0201`–`0204`.
- revisión adicional del tercer notebook de solución anómala.
