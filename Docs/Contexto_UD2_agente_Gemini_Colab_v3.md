# Contexto_UD2_agente_Gemini_Colab
## UD2 · Almacenamiento y procesamiento en Hadoop · contexto operativo mejorado

## 1. Objetivo
Guiar al agente para resolver correctamente una variante de la tarea o del examen práctico de UD2 dentro de Google Colab.

Este documento no es teoría general ni una solución cerrada.  
Es una guía operativa para que el agente:

- entienda qué se evalúa realmente en UD2,
- siga el orden técnico correcto,
- use comandos reales de Hadoop/HDFS/MapReduce,
- adapte el trabajo al dataset real,
- y no sustituya el flujo de Hadoop por atajos con pandas.

---

## 2. Qué manda en UD2
Cuando haya dudas, manda este orden:

1. **enunciado oficial de UD2**,
2. **este contexto de UD2**,
3. **tareas resueltas de UD2 revisadas**,
4. **cuadernos de Hadoop / MapReduce**,
5. **programación oficial del módulo**,
6. **teoría y resumen**.

## Regla crítica
Si una definición general o una solución “cómoda” contradice el patrón operativo de UD2, **manda el patrón operativo de UD2**.

---

## 3. Qué se evalúa realmente en UD2
UD2 evalúa saber trabajar con:

- **Hadoop en Colab**,
- **HDFS**,
- **grep**,
- **MapReduce en Java**,
- **Hadoop Streaming en Python**.

En la práctica, lo que cuenta es:

1. preparar Hadoop,
2. mover archivos a HDFS,
3. hacer un conteo o filtrado con grep,
4. ejecutar un job en Java,
5. ejecutar otro job en Python Streaming,
6. y validar resultados.

---

## 4. Regla semántica crítica de UD2
### En UD2 no se simula Hadoop con pandas
Si el enunciado pide:

- `grep`,
- HDFS,
- MapReduce,
- Hadoop Streaming,

el agente debe usar **comandos reales** del ecosistema Hadoop.

### Prohibido como sustitución
No vale resolver este punto con:

- filtros de pandas,
- `.str.contains()` en DataFrames,
- conteos simulados en Python,
- ni equivalentes “parecidos”.

Esos métodos pueden servir para revisar datos, pero **no sustituyen** los apartados técnicos de UD2.

---

## 5. Orden expositivo vs orden técnico
### Orden expositivo del enunciado
El enunciado suele pedir primero:

1. explicar el dataset,
2. decir qué se quiere extraer con grep,
3. copiar a HDFS,
4. hacer grep,
5. hacer MapReduce Java,
6. hacer Streaming Python.

### Orden técnico real obligatorio
Después de explicar el dataset y qué quieres buscar, el primer bloque técnico ejecutable de UD2 debe ser:

1. **preparar Hadoop en Colab**,
2. **configurar variables de entorno**,
3. **verificar `hadoop version` y HDFS**,
4. **crear estructura HDFS**,
5. **subir archivos a HDFS**,
6. **comprobar rutas y contenido**,
7. **hacer grep real**,
8. **hacer MapReduce en Java**,
9. **hacer Hadoop Streaming en Python**,
10. **validar resultados**.

## Regla dura
El agente no puede pasar directamente de “qué quiero buscar con grep” a “buscar con pandas” o a un grep ficticio.  
Primero debe montar el entorno Hadoop.

---

## 6. Paso 0 técnico obligatorio
Antes de cualquier grep, Java o Streaming, el agente debe comprobar si el entorno Hadoop ya está listo.

### Si no está listo, debe hacer obligatoriamente:
- instalar Java/Hadoop,
- definir `JAVA_HOME`, `HADOOP_HOME` y `PATH`,
- verificar `hadoop version`,
- verificar `hdfs dfs -ls /` o equivalente.

### Si ya está listo
Debe demostrarlo con comandos visibles antes de seguir.

## Regla
Sin este Paso 0 no se puede continuar con el bloque técnico de UD2.

---

## 7. Regla de revisión del cuaderno existente
Antes de proponer el siguiente bloque, el agente debe revisar el cuaderno actual y detectar:

- si Hadoop ya está instalado,
- si las variables de entorno están definidas,
- si HDFS responde,
- si ya existe carpeta HDFS de entrada,
- si los archivos ya se subieron,
- y si ya existe alguna salida previa que haya que limpiar.

## No puede asumir
No puede asumir que “como el cuaderno ya tiene CSV cargados”, entonces grep o MapReduce ya pueden ejecutarse.

En UD2, cargar CSV con pandas no sustituye:
- instalar Hadoop,
- configurar HDFS,
- ni usar comandos reales.

---

## 8. Bloque preferido de instalación
### Opción preferida
La instalación por defecto que debe priorizar el agente es:

- **Java 17**,
- **Hadoop 3.4.2**,
- variables en `/usr/local/hadoop`,
- y verificación con `hadoop version`.

### Opción de respaldo
Solo si la opción simple falla, puede proponer un montaje pseudo-distribuido más completo con XML, NameNode/DataNode y arranque manual.

## Regla
No debe ir directamente a la opción más compleja si la simple aún no se ha intentado.

---

## 9. Regla de HDFS
Antes de grep, Java o Streaming, el agente debe haber hecho como mínimo:

- `hdfs dfs -mkdir -p <directorio>`
- `hdfs dfs -put ...`
- `hdfs dfs -ls <directorio>`
- `hdfs dfs -head <archivo>` o `hdfs dfs -cat <archivo> | head`

### Objetivo
Que quede clara la diferencia entre:

- ruta local de Colab,
- y ruta HDFS.

## Regla dura
No se puede pasar a `grep` si no se ha probado antes que el archivo está realmente accesible en HDFS o, si el caso técnico lo requiere, en una ruta local explícita preparada para el jar de ejemplos.

---

## 10. Regla de grep en UD2
### Lo que debe hacer el agente
Si el usuario quiere, por ejemplo, hacer **MapReduce Grep para `Will`** en el dataset de diálogos, el agente debe plantear un bloque técnico real que incluya:

1. elegir el archivo correcto,
2. verificar su contenido,
3. ejecutar el grep,
4. listar el directorio de salida,
5. y mostrar el resultado.

### Patrón mínimo que debe saber resolver
- realizar **MapReduce Grep** para `'Will'` en `stranger_things_all_dialogue.csv`,
- listar el contenido del directorio de resultados,
- mostrar los resultados del job.

### Regla
No puede quedarse en “sería parecido a grep” ni en “lo haría con pandas”.

---

## 11. Regla de Java MapReduce
Después de grep, el agente debe asumir que el siguiente bloque técnico natural de UD2 será:

- escribir o adaptar un `.java`,
- compilarlo,
- empaquetarlo en `.jar`,
- ejecutarlo con `hadoop jar`,
- y leer la salida.

## El patrón que debe reconocer
- `Mapper`,
- `Reducer`,
- `main`,
- `javac`,
- `jar -cvf`,
- `hadoop jar`.

### Regla
No puede tratar el grep como final del trabajo técnico.  
En UD2, grep es solo una parte del flujo.

---

## 12. Regla de plantilla Java reutilizable
### En examen se debe partir de una plantilla, no inventar desde cero
Para la parte Java de UD2, el agente debe asumir que lo más rentable es partir de una **plantilla fija tipo `WordCountSimple.java`** o equivalente y adaptar solo lo necesario.

### Qué debe tratar como fijo
Estas partes suelen mantenerse casi iguales:

- imports de Hadoop,
- estructura general `Mapper + Reducer + main`,
- suma de valores en el reducer si es WordCount clásico,
- configuración del `Job`,
- `FileInputFormat.addInputPath(...)`,
- `FileOutputFormat.setOutputPath(...)`,
- compilación con `javac`,
- empaquetado con `jar`,
- ejecución con `hadoop jar`.

### Qué debe tratar como adaptable
Estas partes son las que más probablemente habrá que cambiar:

- nombre de la clase principal,
- nombre del archivo `.java`,
- nombre del `.jar`,
- nombre del trabajo en `Job.getInstance(...)`,
- nombre de las clases `Mapper` y `Reducer`,
- lógica interna del `map()`,
- tokenización o separación de la línea,
- limpieza de símbolos o comillas,
- salto de cabecera si hace falta,
- clave y valor emitidos con `context.write(...)`,
- rutas de entrada y salida en HDFS,
- y, si el problema no es un WordCount clásico, la lógica del `reduce()`.

### Regla dura
Si el ejercicio es un WordCount o una variante muy cercana, el agente **no debe inventar una solución Java nueva desde cero**.  
Debe partir de la plantilla y adaptar solo las líneas necesarias.

### Regla práctica para el cuaderno
Si el usuario ya tiene una plantilla Java probada, el agente debe usar esa plantilla como base y ayudar a:
- identificar qué líneas cambian,
- añadir comentarios útiles,
- adaptar `map()`,
- revisar tipos,
- y corregir errores de compilación o ejecución.

---

## 13. Regla de Hadoop Streaming
Después de Java, el agente debe asumir que vendrá el bloque de:

- `mapper.py`,
- `reducer.py`,
- `hadoop-streaming-*.jar`,
- input/output,
- validación de salida.

### Regla
Debe saber que en Streaming:
- el `mapper.py` sí cambia bastante según el caso,
- el `reducer.py` suele ser más reutilizable,
- y hay que limpiar la carpeta de salida antes de relanzar.

---

## 14. Fuentes operativas concretas para UD2
### Núcleo duro
- enunciado oficial,
- tareas resueltas revisadas,
- cuadernos 0201–0204,
- programación oficial.

### Uso de `BBDA02_tarea_resuelta_MP02.ipynb`
Este cuaderno puede usarse como **fuente operativa secundaria** para:

- instalación de Hadoop,
- creación de rutas HDFS,
- grep MapReduce,
- Java MapReduce,
- Streaming Python.

### Restricción
No debe usarse como modelo de estilo del cuaderno final ni copiarse a ciegas.  
Debe usarse como referencia de **bloques técnicos**.

---

## 15. Pipeline canónico obligatorio de UD2

### Paso 1 · Identificar dataset y archivo útil
Objetivo:
- elegir qué archivo analizar,
- qué palabra o patrón buscar,
- y qué sentido tiene ese conteo.

### Paso 2 · Preparar Hadoop en Colab
Objetivo:
- dejar Java y Hadoop utilizables.

### Paso 3 · Verificar entorno
Objetivo:
- comprobar `hadoop version`,
- comprobar HDFS.

### Paso 4 · Revisar archivos locales
Objetivo:
- confirmar que el dataset existe,
- comprobar nombre exacto, cabecera y separador.

### Paso 5 · Crear estructura HDFS
Objetivo:
- preparar carpeta de entrada,
- y limpiar salidas previas si hace falta.

### Paso 6 · Subir a HDFS
Objetivo:
- copiar desde local a HDFS.

### Paso 7 · Verificar HDFS
Objetivo:
- listar,
- ver primeras líneas,
- confirmar que la ruta es correcta.

### Paso 8 · Ejecutar grep real
Objetivo:
- lanzar el conteo o filtrado con Hadoop o con grep sobre datos ya accesibles.

### Paso 9 · Listar el directorio de resultados
Objetivo:
- comprobar que el job generó salida.

### Paso 10 · Mostrar resultados del grep
Objetivo:
- abrir `part-r-00000` o salida equivalente,
- e interpretar el resultado.

### Paso 11 · Ejecutar Java MapReduce
Objetivo:
- compilar, empaquetar y ejecutar.

### Paso 12 · Ejecutar Python Streaming
Objetivo:
- lanzar mapper/reducer en Hadoop Streaming.

### Paso 13 · Validar y comparar
Objetivo:
- comprobar que no hay carpetas vacías,
- revisar `_SUCCESS`,
- comparar salidas si procede,
- cerrar con conclusión.

---

## 16. Bloques técnicos mínimos que el agente debe saber proponer

### 16.1. Instalación simple de Hadoop en Colab
```python
!wget https://downloads.apache.org/hadoop/common/hadoop-3.4.2/hadoop-3.4.2.tar.gz
!tar -xzf hadoop-3.4.2.tar.gz
!mv hadoop-3.4.2 /usr/local/hadoop

import os
os.environ["JAVA_HOME"] = "/usr/lib/jvm/java-17-openjdk-amd64"
os.environ["HADOOP_HOME"] = "/usr/local/hadoop"
os.environ["PATH"] += f":{os.environ['HADOOP_HOME']}/bin:{os.environ['HADOOP_HOME']}/sbin"

!hadoop version
```

### 16.2. Crear y revisar HDFS
```bash
!hdfs dfs -mkdir -p /input
!hdfs dfs -ls /
```

### 16.3. Subir archivo a HDFS
```bash
!hdfs dfs -put -f /content/stranger_things_all_dialogue.csv /input
!hdfs dfs -ls /input
!hdfs dfs -head /input/stranger_things_all_dialogue.csv
```

### 16.4. MapReduce Grep para `Will`
```bash
!hdfs dfs -rm -r -f grep_will
!hadoop jar /usr/local/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.4.2.jar grep /input/stranger_things_all_dialogue.csv grep_will 'Will'
```

### 16.5. Listar resultados del grep
```bash
!hdfs dfs -ls grep_will
```

### 16.6. Mostrar resultados del grep
```bash
!hdfs dfs -cat grep_will/part-r-00000
```

### 16.7. Java MapReduce
El agente debe saber que después toca:
- `%%writefile WordCountSimple.java`
- `javac`
- `jar -cvf`
- `hadoop jar ...`

### 16.8. Hadoop Streaming
El agente debe saber que después toca:
- `%%writefile mapper.py`
- `%%writefile reducer.py`
- localizar `hadoop-streaming*.jar`
- ejecutar el job
- y abrir `part-00000`.

---

## 17. Qué debe comprobar antes de proponer cada bloque
Antes de proponer un bloque técnico, el agente debe comprobar:

- si el paso anterior ya está realmente hecho,
- si las rutas coinciden,
- si el archivo correcto existe,
- si la salida anterior debe borrarse,
- si está trabajando sobre ruta local o HDFS,
- si está usando la plantilla Java o Streaming adecuada,
- y si el bloque responde al apartado real del enunciado.

---

## 18. Qué no puede hacer el agente en UD2
### Prohibido
- simular grep con pandas,
- proponer búsquedas sin Hadoop ni HDFS cuando el punto técnico ya ha empezado,
- olvidarse de instalar Hadoop,
- lanzar Java o Streaming sin haber preparado rutas y archivos,
- hablar de grep como si fuera una idea abstracta en vez de un bloque ejecutable,
- tratar HDFS y sistema local como si fueran lo mismo,
- pasar a otro punto sin haber mostrado la salida real del grep,
- inventar una solución Java desde cero si ya existe una plantilla válida y cercana.

---

## 19. Señales de que el bloque de UD2 está bien resuelto
El proceso está bien resuelto si:

- Hadoop está instalado y verificado,
- HDFS responde,
- el archivo está en HDFS o en la ruta técnica correcta,
- se ejecuta el grep real,
- se lista el directorio de salida,
- se muestra el contenido del resultado,
- el bloque Java parte de una plantilla razonable,
- el mapper/reducer están adaptados solo en lo necesario,
- y queda claro qué se ha contado.

---

## 20. Señales de que el agente se está equivocando
El agente está fallando si:

- quiere hacer grep con pandas,
- no instala Hadoop,
- no crea ni comprueba HDFS,
- no distingue ruta local y ruta HDFS,
- omite la salida del job,
- se queda en explicación teórica,
- no plantea Java y Streaming como continuación natural del flujo,
- o rehace el Java completo cuando bastaba con adaptar pocas líneas.

---

## 21. Criterio final
En UD2 el agente debe actuar así:

- primero contextualiza el dataset y qué quiere buscar,
- luego monta Hadoop,
- después trabaja con HDFS,
- luego hace grep real,
- luego adapta una plantilla Java,
- luego adapta Streaming Python,
- y al final valida todo.

## Regla final
En UD2, el flujo técnico correcto es:

**instalación Hadoop → HDFS → grep → Java MapReduce → Hadoop Streaming**.

Y en la parte Java, la estrategia más rentable es:

**plantilla válida + adaptación mínima**.

Si el agente no está siguiendo esa cadena, hay que corregir el punto.
