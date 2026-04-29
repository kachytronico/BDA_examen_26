# CONTEXTO_UD2 · Almacenamiento y procesamiento en Hadoop

> **Versión:** v6 (alineada con la plantilla de examen — plantillas operativas pre-cargadas en el cuaderno)
> **Cómo usar este documento:** pégalo en una celda markdown del cuaderno. Quédate en el cuaderno como referencia permanente para el agente. **No la borres ni la escondas.**

> **Cambio respecto a v5:** las plantillas operativas vuelven a estar **pre-cargadas en el cuaderno** como celdas con cabecera `## ⚙️ Plantilla pre-cargada · …` o `## ☕ Plantilla pre-cargada · …`. El agente las identifica visualmente, las usa y las adapta, **no las pide por chat ni las regenera**. Esta arquitectura es la usada en la plantilla de examen real.

> **Información del enunciado concreto** (palabra a buscar, archivo a usar, etc.) **vive en PLAN_TAREA**, no aquí.

---

## ÍNDICE

0. [Plantillas operativas disponibles](#0-plantillas-operativas-disponibles)
1. [Qué se evalúa en UD2](#1-qué-se-evalúa-en-ud2)
2. [Qué manda en caso de duda](#2-qué-manda-en-caso-de-duda)
3. [Mapa conceptual de los apartados típicos de UD2](#3-mapa-conceptual-de-los-apartados-típicos-de-ud2)
4. [Reglas semánticas críticas por apartado](#4-reglas-semánticas-críticas-por-apartado)
5. [Decisiones que el agente NO puede tomar solo](#5-decisiones-que-el-agente-no-puede-tomar-solo)
6. [Pipeline técnico obligatorio de UD2](#6-pipeline-técnico-obligatorio-de-ud2)
7. [Bloques operativos mínimos de referencia](#7-bloques-operativos-mínimos-de-referencia)
8. [Señales de que un apartado está bien resuelto](#8-señales-de-que-un-apartado-está-bien-resuelto)
9. [Señales de que el agente se está equivocando](#9-señales-de-que-el-agente-se-está-equivocando)

---

## 0. Plantillas operativas disponibles

UD2 tiene **plantillas operativas pre-cargadas en el cuaderno** como celdas markdown con cabecera reconocible:

| Cabecera de la celda en el cuaderno | Para qué apartado | Qué contiene |
|---|---|---|
| `## ⚙️ Plantilla pre-cargada · Instalación de Hadoop y configuración` | Antes de empezar UD2 | Instala Hadoop 3.4.2 y configura `JAVA_HOME`, `HADOOP_HOME`, `PATH` |
| (pre-cargada también en la zona de carga) | Apartado "Operaciones HDFS" | Patrón de 4 pasos: `shutil.copy` a `/content/` → `mkdir dataset` → `put -f` → `ls` |
| `## ☕ Plantilla pre-cargada · MapReduce en Java (WordCount)` | Apartado "MapReduce Java" | Tres archivos separados (`WordCount.java`, `WordCountMapper.java`, `WordCountReducer.java`) + celda de compilación y ejecución |
| `## 🐍 Plantilla pre-cargada · MapReduce en Python (Hadoop Streaming)` | Apartado "MapReduce Streaming" | `mapper.py` + `reducer.py` + celda de ejecución con `hadoop-streaming-*.jar` |

### Cómo trabaja el agente con las plantillas

Al llegar a un apartado de UD2:

1. **Identifica** qué plantilla aplica (por la cabecera de la celda en el cuaderno).
2. **Usa** la plantilla pre-cargada: la ejecuta tal cual o crea celdas nuevas que la adaptan al ejercicio concreto, según los marcadores de la plantilla.
3. **No la pide por chat. No la regenera. No la duplica.**

### Regla de decisión: cómo el agente convierte la plantilla en celdas del cuaderno

Las plantillas vienen con dos tipos de bloques de código:

- **Bloques de comandos directos** (Python, Bash con `!hdfs dfs`, `!hadoop jar`, etc.): el agente puede ejecutar la celda existente o crear celdas nuevas adaptando los `[ADAPTAR]` con valores de PLAN_TAREA.
- **Bloques con `%%writefile`** (archivos `.java`, `.py` para Streaming): el agente escribe celdas con `%%writefile` **literal**, sin tocar el contenido marcado como `NORMALMENTE NO CAMBIAR` o `REVISAR`. Las adaptaciones de marcadores `CAMBIAR CASI SIEMPRE` se hacen solo si el ejercicio lo pide explícitamente, y siempre consultando con el usuario antes.

### Reglas duras sobre las plantillas

- **No regenerar.** Si el agente cree que la plantilla pre-cargada está incompleta o tiene un error, **avisa al usuario y se detiene**. No la rehace en silencio.
- **No duplicar.** Si la plantilla ya está en el cuaderno (existe la celda con su cabecera reconocible), el agente NO la vuelve a escribir. Si necesita modificarla, lo hace en la celda existente o crea una celda secundaria con la adaptación, manteniendo la plantilla original intacta.
- **No reinstalar.** Si Hadoop ya está instalado por la celda pre-cargada, el agente NO ejecuta `wget` ni `tar` adicionales. Solo verifica con `!hadoop version`.
- **Respetar los nombres canónicos:** carpeta HDFS `dataset/`, jar `wordcount.jar`, archivos Java `WordCount.java` / `WordCountMapper.java` / `WordCountReducer.java`, salidas `grep_<patrón>` / `wordcount_java` / `wordcount_streaming`.
- **Convención común a todos los jobs Hadoop:** el input apunta a la **carpeta** HDFS (`dataset/`), nunca a un archivo concreto. Las carpetas de salida llevan nombre descriptivo, nunca genérico (`output_grep` ❌, `grep_spain` ✅).
- **Limpieza preventiva** del directorio de salida antes de cada ejecución: `!hdfs dfs -rm -r <carpeta_salida> 2>/dev/null || true`. Aplica a grep, Java y Streaming.
- **Si el agente renombra la clase Java** (poco habitual), debe renombrar **los tres archivos**, las **clases**, todas las referencias y el `.jar` final. Si va a hacer esto, **pregunta antes**.

---

## 1. Qué se evalúa en UD2

UD2 evalúa saber trabajar con el **ecosistema Hadoop real** dentro de Colab para procesar datos de forma distribuida. Los conceptos clave son:

- **HDFS** (Hadoop Distributed File System) para gestionar archivos.
- **Comandos Unix** sobre HDFS y sobre la salida de los jobs.
- **MapReduce Grep** para conteos por filtrado.
- **MapReduce en Java** para conteo de palabras (WordCount o variantes).
- **Hadoop Streaming en Python** para hacer lo mismo con `mapper.py` + `reducer.py`.

UD2 **no es un ETL en pandas**, es **trabajo distribuido real con comandos Hadoop**.

---

## 2. Qué manda en caso de duda

1. **enunciado oficial** del ejercicio actual,
2. **PLAN_TAREA** (lectura concreta del enunciado y estado de avance),
3. **este contexto de unidad** (reglas conceptuales de UD2),
4. **plantillas operativas** (sección 0, pre-cargadas en el cuaderno),
5. **tareas resueltas y aprobadas**,
6. cuadernos de ejemplo del profesor,
7. teoría general.

Si una definición genérica contradice el patrón operativo de UD2, **manda el patrón de UD2**.

---

## 3. Mapa conceptual de los apartados típicos de UD2

| Apartado típico | Tema | Lo crítico |
|---|---|---|
| Explicación del dataset y del grep | Contexto + grep teórico | Decir qué palabra buscar y por qué tiene sentido |
| Operaciones HDFS | Gestión de archivos distribuidos | Subir archivos a HDFS con comandos reales |
| MapReduce Grep | Conteo por filtrado | Comando `hadoop jar ... grep` real, no pandas |
| MapReduce Java | Conteo de palabras | Compilar `.java`, empaquetar `.jar`, ejecutar |
| Hadoop Streaming Python | Conteo de palabras alternativo | `mapper.py` + `reducer.py` con `hadoop-streaming-*.jar` |

> **Numeración real:** los apartados 1, 2, 3, 4 del enunciado oficial de UD2 agrupan estos temas (apartado 4 = Java + Streaming). En examen multi-unidad, la numeración la fija PLAN_TAREA.

---

## 4. Reglas semánticas críticas por apartado

Estas son las interpretaciones obligatorias en UD2. Si el agente quiere desviarse de alguna, debe consultar antes.

### Convención común a todos los jobs Hadoop de esta unidad

- El **input** apunta a la **carpeta HDFS** que contiene los archivos (típicamente `dataset/`), no a archivos individuales. Hadoop procesa todos los archivos de la carpeta.
- Las **carpetas de salida** llevan nombre descriptivo según la operación: `grep_<patron>`, `wordcount_java`, `wordcount_streaming`. Nunca nombres genéricos tipo `output_grep` o `salida`.
- **Limpieza preventiva** del directorio de salida antes de cada ejecución: `!hdfs dfs -rm -r <carpeta_salida> 2>/dev/null || true`. Aplica a grep, Java y Streaming.

### Apartado: Explicación del dataset y del grep
**Significa:** describir brevemente el dataset elegido (suele ser el mismo de UD1) y argumentar **qué palabra concreta se va a buscar y por qué tiene sentido buscarla**.

**Patrón típico:**
- 2-3 párrafos en celda markdown,
- mencionar el archivo concreto y por qué se elige,
- proponer la palabra y justificar interés analítico.

**🚨 Riesgo:** la palabra concreta **viene de PLAN_TAREA**. El agente no la inventa.

### Apartado: Operaciones HDFS 🚨
**Significa:** **mover el archivo desde el filesystem local de Colab al sistema de archivos distribuido HDFS** usando comandos `hdfs dfs`.

**Plantilla operativa requerida:** patrón HDFS pre-cargado en el cuaderno (también requiere que la plantilla pre-cargada de instalación de Hadoop se haya ejecutado antes).

**No es:**
- abrir el CSV con `pd.read_csv()` y "ya está disponible",
- copiar el archivo a `/content/` y considerar que está "subido",
- listarlo con `os.listdir()` en lugar de `hdfs dfs -ls`,
- hacer `hdfs dfs -put` directamente desde la cache de kagglehub (las rutas cambian entre ejecuciones por el caching),
- crear el directorio HDFS con un nombre distinto a `dataset` (ni `datasets`, ni `data`, ni `archivos`).

**Patrón típico (4 pasos):**
1. **estabilizar la ruta** del archivo: `shutil.copy(<ruta_origen>, '/content/<nombre_corto>.csv')`,
2. **crear directorio HDFS**: `hdfs dfs -mkdir -p dataset` (siempre 'dataset' en singular),
3. **subir archivo**: `hdfs dfs -put -f /content/<nombre_corto>.csv dataset/`,
4. **verificar**: `hdfs dfs -ls dataset/`.

**Si PLAN_TAREA lista varios archivos para Hadoop**, repetir los pasos 1 y 3 una vez por cada archivo, dejando los pasos 2 y 4 una sola vez.

**🔍 Distinción crítica ruta local vs HDFS:**
- `/content/archivo.csv` es ruta **local** de Colab.
- `dataset/archivo.csv` o `/user/root/dataset/...` es ruta **HDFS**.
- Estas rutas son diferentes aunque parezcan equivalentes. El agente confunde fácilmente las dos.

### Apartado: MapReduce Grep 🚨
**Significa:** ejecutar un job de Hadoop que filtra líneas que contienen un patrón y las cuenta. Comando típico:
```bash
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep <carpeta_input> <carpeta_output> <patrón>
```

**Plantilla operativa requerida:** ninguna específica (el grep usa el `hadoop-mapreduce-examples-*.jar` que viene con la instalación). El agente puede usar el bloque de referencia 7.3.

**No es:**
- `df['col'].str.contains('palabra')` en pandas,
- `grep palabra archivo.csv` en bash sobre el archivo local (eso es grep de Unix, no MapReduce Grep),
- `df['col'].value_counts()` con un filtro previo.

**Patrón típico:**
1. limpieza preventiva: `hdfs dfs -rm -r grep_<patron> 2>/dev/null || true`,
2. ejecutar el `hadoop jar ... grep` apuntando a la carpeta `dataset/` (no a un archivo concreto),
3. listar el directorio de salida con `hdfs dfs -ls grep_<patron>`,
4. mostrar el contenido del `part-r-00000`,
5. interpretar el resultado.

**🔍 Riesgo de "atajos":** Gemini puede proponer "una versión simplificada con pandas" para mostrar el conteo. **Eso no es UD2**, es UD1. La regla es que cada apartado de UD2 use el ecosistema Hadoop real.

### Apartado: MapReduce en Java 🚨
**Significa:** escribir (o adaptar) un programa MapReduce en Java que cuente palabras, compilarlo, empaquetarlo en `.jar` y ejecutarlo con Hadoop.

**Plantilla operativa requerida:** plantilla pre-cargada `## ☕ Plantilla pre-cargada · MapReduce en Java (WordCount)` (ya en el cuaderno).

**Pipeline obligatorio:**
1. localizar la plantilla pre-cargada en el cuaderno (cabecera `## ☕ Plantilla pre-cargada · …`),
2. crear las celdas `%%writefile` para los tres archivos: `WordCount.java`, `WordCountMapper.java`, `WordCountReducer.java`,
3. crear la celda de compilación + empaquetado + limpieza + ejecución,
4. ejecutar el job apuntando a `dataset/` (carpeta entera) y guardando en `wordcount_java`,
5. listar el directorio de salida y mostrar `part-r-00000`.

**🔧 Cómo adaptar la Plantilla Java:**
El agente respeta los marcadores de comentarios (`CAMBIAR CASI SIEMPRE`, `REVISAR`, `NORMALMENTE NO CAMBIAR`, `CAMBIAR SIEMPRE EN EJECUCIÓN`) y solo modifica lo que el ejercicio requiere. Si el ejercicio pide modificaciones en zonas `CAMBIAR CASI SIEMPRE` (limpieza de puntuación, paso a minúsculas, salto de cabecera), el agente **propone los cambios al usuario en chat antes de aplicarlos**. Si la plantilla parece incompleta o incoherente (ejemplo: nombre del archivo distinto al de la clase pública, falta Mapper o Reducer), el agente **avisa y se detiene**, no rehace la plantilla.

**Regla dura:** si el ejercicio es WordCount o variante cercana, **no inventar Java desde cero**. Adaptar la plantilla.

### Apartado: Hadoop Streaming en Python 🚨
**Significa:** ejecutar el mismo conteo de palabras pero usando Python (`mapper.py` y `reducer.py`) a través del jar de Hadoop Streaming.

**Plantilla operativa requerida:** plantilla pre-cargada `## 🐍 Plantilla pre-cargada · MapReduce en Python (Hadoop Streaming)` (ya en el cuaderno).

**Pipeline obligatorio:**
1. localizar la plantilla pre-cargada en el cuaderno (cabecera `## 🐍 Plantilla pre-cargada · …`),
2. crear las celdas `%%writefile` para `mapper.py` y `reducer.py`,
3. crear la celda de ejecución con limpieza preventiva,
4. ejecutar con `hadoop jar ... hadoop-streaming-*.jar -mapper mapper.py -reducer reducer.py -input dataset -output wordcount_streaming`,
5. listar y mostrar el resultado.

**🔍 Error típico:** olvidar limpiar la carpeta de salida HDFS antes de relanzar Streaming. Comando: `hdfs dfs -rm -r wordcount_streaming 2>/dev/null || true`.

---

## 5. Decisiones que el agente NO puede tomar solo

Detente y consulta antes de:

### Sobre el flujo Hadoop
- saltarse la creación del directorio HDFS antes de hacer `put`,
- ejecutar grep sobre el archivo local en lugar de sobre HDFS,
- considerar que el apartado HDFS está completo sin haber hecho `put` real,
- mezclar `os.path` con rutas HDFS sin distinguirlas,
- crear un directorio HDFS con un nombre distinto a `dataset`,
- hacer `put` directo desde la cache de kagglehub sin pasar por `/content/`.

### Sobre el código Java
- regenerar la plantilla Java cuando el ejercicio se resuelve adaptándola,
- modificar partes marcadas como `NORMALMENTE NO CAMBIAR` sin necesidad clara,
- aplicar adaptaciones (limpieza de puntuación, minúsculas, etc.) sin proponerlas al usuario antes,
- cambiar el reducer estándar de WordCount cuando el ejercicio es WordCount,
- renombrar la clase principal (afecta a tres archivos a la vez).

### Sobre Streaming
- relanzar el job sin limpiar la carpeta de salida HDFS,
- modificar `reducer.py` cuando el `mapper.py` ya hace todo el trabajo de transformación,
- aplicar adaptaciones al `mapper.py` (regex, lowercasing) sin proponerlas antes,
- usar rutas locales en `-input` o `-output` cuando deben ser rutas HDFS.

### Sobre estrategia general
- sustituir un apartado de UD2 por una versión "equivalente" en pandas,
- saltarse el orden técnico obligatorio (instalar → HDFS → grep → Java → Streaming),
- considerar que el apartado de Java está hecho sin haber visto un `part-r-00000` real,
- escribir una plantilla en el cuaderno cuando ya está escrita (duplicación).

---

## 6. Pipeline técnico obligatorio de UD2

Este orden no se puede saltar. Cada paso técnico debe ejecutarse y verificarse antes del siguiente.

| Paso | Qué se hace | Plantilla necesaria | Cómo se verifica |
|---|---|---|---|
| 1 | Instalación de Hadoop | `plantilla_01_instalacion_hadoop.md` | `hadoop version` muestra 3.4.2 |
| 2 | Configuración de variables de entorno | (incluida en la plantilla 01) | `echo $HADOOP_HOME` muestra ruta correcta |
| 3 | Crear directorio HDFS `dataset/` y subir archivos | `plantilla_02_hdfs.md` | `hdfs dfs -ls dataset/` muestra los archivos |
| 4 | MapReduce Grep | (ninguna específica) | `part-r-00000` muestra conteo |
| 5 | MapReduce Java (compilar + empaquetar + ejecutar) | `plantilla_03_java_wordcount.md` | `part-r-00000` muestra resultado |
| 6 | Hadoop Streaming (mapper + reducer + ejecutar) | `plantilla_04_streaming.md` | `part-00000` muestra resultado |
| 7 | Validación final | — | `_SUCCESS` en directorios de salida |

**Regla:** si un paso falla, no se sigue al siguiente sin haberlo resuelto. El agente no debe "intentar continuar" con un paso roto.

---

## 7. Bloques operativos mínimos de referencia

> **Importante:** estos bloques son **referencia conceptual**. La instalación de Hadoop, plantilla HDFS, plantilla Java y plantilla Streaming **viven como archivos `.md` independientes** (sección 0) y se inyectan al cuaderno cuando el apartado las pide. El agente no las regenera; las usa.

### 7.1 Verificar Hadoop disponible
```python
!hadoop version
!echo $HADOOP_HOME
!echo $JAVA_HOME
```

### 7.2 Subir archivo a HDFS (patrón de 4 pasos)
```python
import shutil

# 1. Estabilizar ruta: copiar a /content/ con nombre corto
archivo_origen = "<RUTA_ORIGEN>"
archivo_local = "/content/<NOMBRE_CORTO>.csv"
shutil.copy(archivo_origen, archivo_local)

# 2. Crear directorio HDFS (siempre 'dataset', singular)
!hdfs dfs -mkdir -p dataset

# 3. Subir archivo a HDFS
!hdfs dfs -put -f /content/<NOMBRE_CORTO>.csv dataset/

# 4. Verificar
!hdfs dfs -ls dataset/
```

### 7.3 MapReduce Grep
```bash
# Limpieza preventiva
!hdfs dfs -rm -r grep_<PATRON> 2>/dev/null || true

# Ejecutar grep sobre la carpeta dataset/ entera
!hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar \
    grep dataset grep_<PATRON> '<PATRÓN>'

!hdfs dfs -ls grep_<PATRON>
!hdfs dfs -cat grep_<PATRON>/part-r-00000
```

### 7.4 MapReduce Java (asumiendo plantilla pre-cargada en el cuaderno)
```bash
# Compilar los tres archivos juntos
!mkdir -p /tmp/classes
!javac -classpath `hadoop classpath` -d /tmp/classes WordCount.java WordCountMapper.java WordCountReducer.java
!jar -cvf wordcount.jar -C /tmp/classes/ .

# Limpieza preventiva
!hdfs dfs -rm -r wordcount_java 2>/dev/null || true

# Ejecutar sobre la carpeta dataset/ entera
!hadoop jar wordcount.jar WordCount dataset wordcount_java
!hdfs dfs -cat wordcount_java/part-r-00000 | head -n 20
```

### 7.5 Hadoop Streaming Python (asumiendo plantilla pre-cargada en el cuaderno)
```bash
# Limpieza preventiva
!hdfs dfs -rm -r wordcount_streaming 2>/dev/null || true

!hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*.jar \
    -files mapper.py,reducer.py \
    -mapper "python3 mapper.py" \
    -reducer "python3 reducer.py" \
    -input dataset \
    -output wordcount_streaming

!hdfs dfs -ls wordcount_streaming
!hdfs dfs -cat wordcount_streaming/part-00000 | head -n 20
```

> **Adaptaciones:** sustituir `<NOMBRE_CORTO>`, `<PATRÓN>`, etc. con los valores concretos de PLAN_TAREA. El agente lee PLAN_TAREA antes de proponer estos bloques.

---

## 8. Señales de que un apartado está bien resuelto

- El archivo se sube a HDFS con `hdfs dfs -put` real, pasando por `/content/` primero, no se confunde con copia local pura.
- La carpeta HDFS se llama `dataset` (singular).
- El grep se ejecuta con `hadoop jar ... grep` apuntando a la carpeta `dataset/`, no a un archivo concreto, ni con pandas ni con grep de Unix sobre archivo local.
- La carpeta de salida del grep se llama `grep_<patrón>` (descriptiva), no `output_grep` ni similar.
- El job Java compila, empaqueta y ejecuta sin errores. Los tres archivos Java tienen nombres consistentes con sus clases públicas.
- El job Streaming se ejecuta con `hadoop-streaming-*.jar` apuntando a `dataset/` y produce un `part-00000` con resultados.
- Antes de relanzar cualquier job, se limpia la carpeta de salida HDFS.
- Cada paso del pipeline tiene salida visible (`hdfs dfs -ls`, `hdfs dfs -cat`) que demuestra el resultado.
- Las rutas HDFS (`dataset/archivo.csv`) y locales (`/content/archivo.csv`) están claramente diferenciadas.
- Cada plantilla operativa aparece **una sola vez** en el cuaderno (no duplicada).
- El agente cita CONTEXTO_UD2 + PLAN_TAREA + plantilla aplicable en cada apartado antes de proponer código.

---

## 9. Señales de que el agente se está equivocando

- Propone hacer el grep con `df['col'].str.contains()` o equivalente en pandas → **fallo grave, no es UD2**.
- Considera el apartado HDFS resuelto sin haber hecho `hdfs dfs -put` (solo lee el archivo con pandas) → **fallo grave**.
- Mezcla rutas locales y HDFS sin advertirlo.
- Crea un directorio HDFS con nombre distinto a `dataset` (`datasets`, `data`, etc.) → **rompe la convención fija de la unidad**.
- Hace `hdfs dfs -put` directamente desde la ruta de kagglehub sin pasar por `/content/` → falla en re-ejecuciones por el caching.
- El nombre del archivo Java no coincide con el nombre de la clase pública (ej: archivo `CountryCounter.java` con `public class WordCount`) → **fallo grave de compilación**, el agente debe detectarlo y avisar.
- Genera código Java sin haber localizado la plantilla pre-cargada en el cuaderno (la inventa desde cero).
- Aplica adaptaciones a la plantilla (limpieza de puntuación, lowercasing) sin proponerlas al usuario antes.
- Escribe una plantilla en el cuaderno cuando ya estaba escrita en una celda anterior → **duplicación**.
- El input del job apunta a un archivo concreto (`dataset/archivo.csv`) en lugar de a la carpeta (`dataset/`).
- La carpeta de salida lleva nombre genérico (`output_grep`, `salida`, `out`) en lugar de descriptivo (`grep_spain`, `wordcount_java`).
- Olvida limpiar la carpeta de salida HDFS antes de relanzar un job → falla con "output directory already exists".
- Considera que un apartado está hecho sin haber visto el `part-r-00000` o `part-00000` con resultados reales.
- Afirma que un comando funcionó sin tener evidencia visible en el cuaderno.
- Salta directamente a Java sin haber hecho HDFS y grep.
- Considera que ha completado el apartado 4 del enunciado solo con Java, olvidando Streaming (o viceversa).
- Conclusiones que terminan con frases tipo *"asegurando la consistencia..."*, *"garantizando la integridad..."* (eso lo prohíbe la guía de estilo).
