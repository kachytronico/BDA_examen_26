# CONTEXTO_UD2 · Almacenamiento y procesamiento en Hadoop

> **Versión:** v3 (alineada con la nueva arquitectura del sistema BDA v7)
> **Cómo usar este documento:** pégalo en una celda markdown del cuaderno. Quédate en el cuaderno como referencia permanente para el agente. **No la borres ni la escondas.**

> **Asunción importante:** este contexto **asume que el cuaderno tiene celdas pre-cargadas** con la instalación de Hadoop, configuración de variables de entorno, plantilla Java (`WordCountSimple.java`) y plantillas Streaming (`mapper.py`, `reducer.py`). El agente **no debe regenerar esos elementos**, debe usarlos.

> **Información del enunciado concreto** (palabra a buscar, archivo a usar, etc.) **vive en PLAN_TAREA**, no aquí.

---

## ÍNDICE

1. [Qué se evalúa en UD2](#1-qué-se-evalúa-en-ud2)
2. [Qué manda en caso de duda](#2-qué-manda-en-caso-de-duda)
3. [Mapa conceptual de los apartados típicos de UD2](#3-mapa-conceptual-de-los-apartados-típicos-de-ud2)
4. [Reglas semánticas críticas por apartado](#4-reglas-semánticas-críticas-por-apartado)
5. [Decisiones que el agente NO puede tomar solo](#5-decisiones-que-el-agente-no-puede-tomar-solo)
6. [Pipeline técnico obligatorio de UD2](#6-pipeline-técnico-obligatorio-de-ud2)
7. [Bloques operativos mínimos de referencia](#7-bloques-operativos-mínimos-de-referencia)
8. [Señales de que un apartado está bien resuelto](#8-señales-de-que-un-apartado-está-bien-resuelto)
9. [Señales de que el agente se está equivocando](#9-señales-de-que-el-agente-se-está-equivocando)

> **Nota:** los detalles del enunciado actual (palabra a buscar, archivo concreto, mapa apartado↔unidad, decisiones tomadas) están en la celda PLAN_TAREA del cuaderno, no aquí.

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
4. **plantillas pre-cargadas del cuaderno** (instalación, Java, Streaming),
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

### Apartado: Explicación del dataset y del grep
**Significa:** describir brevemente el dataset elegido (suele ser el mismo de UD1) y argumentar **qué palabra concreta se va a buscar y por qué tiene sentido buscarla**.

**Patrón típico:**
- 2-3 párrafos en celda markdown,
- mencionar el archivo concreto (ej. `stranger_things_all_dialogue.csv`) y por qué se elige,
- proponer la palabra y justificar interés analítico.

**🚨 Riesgo:** la palabra concreta **viene de PLAN_TAREA**. El agente no la inventa.

### Apartado: Operaciones HDFS 🚨
**Significa:** **mover el archivo desde el filesystem local de Colab al sistema de archivos distribuido HDFS** usando comandos `hdfs dfs`.

**No es:**
- abrir el CSV con `pd.read_csv()` y "ya está disponible",
- copiar el archivo a `/content/` y considerar que está "subido",
- listarlo con `os.listdir()` en lugar de `hdfs dfs -ls`.

**Patrón típico:**
1. crear directorio HDFS (`hdfs dfs -mkdir`),
2. copiar archivo desde local a HDFS (`hdfs dfs -put`),
3. verificar con `hdfs dfs -ls`.

**🔍 Distinción crítica ruta local vs HDFS:**
- `/content/archivo.csv` es ruta **local** de Colab.
- `STUD2/archivo.csv` o `/user/root/STUD2/...` es ruta **HDFS**.
- Estas rutas son diferentes aunque parezcan equivalentes. El agente confunde fácilmente las dos.

### Apartado: MapReduce Grep 🚨
**Significa:** ejecutar un job de Hadoop que filtra líneas que contienen un patrón y las cuenta. Comando típico:
```bash
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep <input> <output> <patrón>
```

**No es:**
- `df['col'].str.contains('palabra')` en pandas,
- `grep palabra archivo.csv` en bash sobre el archivo local (eso es grep de Unix, no MapReduce Grep),
- `df['col'].value_counts()` con un filtro previo.

**Patrón típico:**
1. ejecutar el `hadoop jar ... grep`,
2. listar el directorio de salida con `hdfs dfs -ls`,
3. mostrar el contenido del `part-r-00000` o equivalente,
4. interpretar el resultado.

**🔍 Riesgo de "atajos":** Gemini puede proponer "una versión simplificada con pandas" para mostrar el conteo. **Eso no es UD2**, es UD1. La regla es que cada apartado de UD2 use el ecosistema Hadoop real.

### Apartado: MapReduce en Java 🚨
**Significa:** escribir (o adaptar) un programa MapReduce en Java que cuente palabras, compilarlo, empaquetarlo en `.jar` y ejecutarlo con Hadoop.

**Pipeline obligatorio:**
1. partir de la **plantilla Java pre-cargada** (`WordCountSimple.java` o equivalente),
2. adaptar solo lo necesario (ver más abajo),
3. compilar con `javac`,
4. empaquetar en `.jar`,
5. ejecutar con `hadoop jar`,
6. listar el directorio de salida y mostrar `part-r-00000`.

**🔧 Plantilla Java reutilizable:**
El agente debe asumir que existe una plantilla Java en el cuaderno y adaptar solo:
- nombre de la clase principal,
- nombre del archivo `.java` y `.jar`,
- nombre del trabajo en `Job.getInstance(...)`,
- lógica del `map()` (tokenización, limpieza),
- rutas de entrada y salida.

Y **NO debe tocar** (a menos que sea estrictamente necesario):
- imports de Hadoop,
- estructura general `Mapper + Reducer + main`,
- suma estándar del reducer en WordCount clásico,
- comandos `javac`, `jar`, `hadoop jar`.

**Regla dura:** si el ejercicio es WordCount o variante cercana, **no inventar Java desde cero**. Adaptar la plantilla.

### Apartado: Hadoop Streaming en Python 🚨
**Significa:** ejecutar el mismo conteo de palabras pero usando Python (`mapper.py` y `reducer.py`) a través del jar de Hadoop Streaming.

**Pipeline obligatorio:**
1. partir de las **plantillas pre-cargadas** `mapper.py` y `reducer.py`,
2. adaptar el `mapper.py` si el caso lo requiere (suele cambiar),
3. el `reducer.py` suele ser reutilizable casi tal cual,
4. **limpiar la carpeta de salida HDFS antes de relanzar** (si se relanza, falla con "output directory already exists"),
5. ejecutar con `hadoop jar ... hadoop-streaming-*.jar -mapper mapper.py -reducer reducer.py -input ... -output ...`,
6. listar y mostrar el resultado.

**🔍 Error típico:** olvidar limpiar la carpeta de salida HDFS antes de relanzar Streaming. Comando: `hdfs dfs -rm -r <output_dir>`.

---

## 5. Decisiones que el agente NO puede tomar solo

Detente y consulta antes de:

### Sobre el flujo Hadoop
- saltarse la creación del directorio HDFS antes de hacer `put`,
- ejecutar grep sobre el archivo local en lugar de sobre HDFS,
- considerar que el apartado HDFS está completo sin haber hecho `put` real,
- mezclar `os.path` con rutas HDFS sin distinguirlas.

### Sobre el código Java
- inventar una clase Java nueva si el ejercicio se resuelve adaptando la plantilla,
- modificar partes "fijas" de la plantilla (estructura general, imports) sin necesidad clara,
- cambiar el reducer estándar de WordCount cuando el ejercicio es WordCount.

### Sobre Streaming
- relanzar el job sin limpiar la carpeta de salida HDFS,
- modificar `reducer.py` cuando el `mapper.py` ya hace todo el trabajo de transformación,
- usar rutas locales en `-input` o `-output` cuando deben ser rutas HDFS.

### Sobre estrategia general
- sustituir un apartado de UD2 por una versión "equivalente" en pandas,
- saltarse el orden técnico obligatorio (instalar → HDFS → grep → Java → Streaming),
- considerar que el apartado de Java está hecho sin haber visto un `part-r-00000` real.

---

## 6. Pipeline técnico obligatorio de UD2

Este orden no se puede saltar. Cada paso técnico debe ejecutarse y verificarse antes del siguiente.

| Paso | Qué se hace | Cómo se verifica |
|---|---|---|
| 1 | Instalación de Hadoop (celda pre-cargada) | `hadoop version` muestra 3.4.2 (o versión fijada) |
| 2 | Configuración de variables de entorno (celda pre-cargada) | `echo $HADOOP_HOME` muestra ruta correcta |
| 3 | Crear directorio HDFS | `hdfs dfs -ls` muestra el directorio nuevo |
| 4 | Subir archivo(s) a HDFS | `hdfs dfs -ls <dir>` muestra el archivo |
| 5 | MapReduce Grep | `part-r-00000` muestra conteo |
| 6 | MapReduce Java (compilar + empaquetar + ejecutar) | `part-r-00000` muestra resultado |
| 7 | Hadoop Streaming (mapper + reducer + ejecutar) | `part-00000` muestra resultado |
| 8 | Validación final | `_SUCCESS` en directorios de salida |

**Regla:** si un paso falla, no se sigue al siguiente sin haberlo resuelto. El agente no debe "intentar continuar" con un paso roto.

---

## 7. Bloques operativos mínimos de referencia

> **Importante:** estos bloques son **referencia conceptual**. La instalación de Hadoop, configuración de Java y plantillas (Java + Streaming) **están pre-cargadas en el cuaderno** y el agente las usa, no las regenera.

### 7.1 Verificar Hadoop disponible
```python
!hadoop version
!echo $HADOOP_HOME
!echo $JAVA_HOME
```

### 7.2 Crear directorio HDFS y subir archivo
```bash
!hdfs dfs -mkdir -p <DIR_HDFS>            # crear directorio HDFS
!hdfs dfs -put -f <ruta_local> <DIR_HDFS> # copiar local → HDFS, -f sobrescribe
!hdfs dfs -ls <DIR_HDFS>                  # verificar contenido
```

### 7.3 MapReduce Grep
```bash
!hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar \
    grep <input_hdfs> <output_hdfs> '<PATRÓN>'

!hdfs dfs -ls <output_hdfs>
!hdfs dfs -cat <output_hdfs>/part-r-00000
```

### 7.4 MapReduce Java (asumiendo plantilla pre-cargada)
```bash
!javac -classpath `hadoop classpath` -d /tmp/classes <ARCHIVO>.java
!jar -cvf <NOMBRE>.jar -C /tmp/classes/ .
!hadoop jar <NOMBRE>.jar <CLASE_PRINCIPAL> <input_hdfs> <output_hdfs>
!hdfs dfs -cat <output_hdfs>/part-r-00000
```

### 7.5 Hadoop Streaming Python (asumiendo mapper.py y reducer.py pre-cargados)
```bash
# Limpiar carpeta de salida antes de relanzar
!hdfs dfs -rm -r <output_hdfs> 2>/dev/null || true

!hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*.jar \
    -files mapper.py,reducer.py \
    -mapper mapper.py \
    -reducer reducer.py \
    -input <input_hdfs> \
    -output <output_hdfs>

!hdfs dfs -ls <output_hdfs>
!hdfs dfs -cat <output_hdfs>/part-00000
```

> **Adaptaciones:** sustituir `<DIR_HDFS>`, `<PATRÓN>`, `<ARCHIVO>`, etc. con los valores concretos de PLAN_TAREA. El agente lee PLAN_TAREA antes de proponer estos bloques.

---

## 8. Señales de que un apartado está bien resuelto

- El archivo se sube a HDFS con `hdfs dfs -put` real, no se confunde con copia local.
- El grep se ejecuta con `hadoop jar ... grep`, no con pandas ni con grep de Unix sobre archivo local.
- El job Java compila, empaqueta y ejecuta sin errores.
- El job Streaming se ejecuta con `hadoop-streaming-*.jar` y produce un `part-00000` con resultados.
- Antes de relanzar Streaming, se limpia la carpeta de salida HDFS.
- Cada paso del pipeline tiene salida visible (`hdfs dfs -ls`, `hdfs dfs -cat`) que demuestra el resultado.
- Las rutas HDFS (`STUD2/archivo.csv`) y locales (`/content/archivo.csv`) están claramente diferenciadas.
- El agente cita CONTEXTO_UD2 + PLAN_TAREA en cada apartado antes de proponer código.

---

## 9. Señales de que el agente se está equivocando

- Propone hacer el grep con `df['col'].str.contains()` o equivalente en pandas → **fallo grave, no es UD2**.
- Considera el apartado HDFS resuelto sin haber hecho `hdfs dfs -put` (solo lee el archivo con pandas) → **fallo grave**.
- Mezcla rutas locales y HDFS sin advertirlo.
- Reescribe el `WordCountSimple.java` desde cero en vez de adaptar la plantilla.
- Reescribe `mapper.py` o `reducer.py` desde cero sin necesidad clara.
- Olvida limpiar la carpeta de salida HDFS antes de relanzar Streaming → el job falla con "output directory already exists".
- Considera que un apartado está hecho sin haber visto el `part-r-00000` o `part-00000` con resultados reales.
- Afirma que un comando funcionó sin tener evidencia visible en el cuaderno.
- Salta directamente a Java sin haber hecho HDFS y grep.
- Considera que ha completado el apartado 4 del enunciado solo con Java, olvidando Streaming (o viceversa).
- Conclusiones que terminan con frases tipo *"asegurando la consistencia..."*, *"garantizando la integridad..."* (eso lo prohíbe la guía de estilo).
