# Plantilla 03 · MapReduce en Java (WordCount)

> **Cuándo usar:** apartado "MapReduce Java" de UD2.
> **Requisitos previos:** plantillas 01 y 02 ya inyectadas y ejecutadas (Hadoop instalado, archivos en HDFS).
> **Cómo se inyecta:** el usuario pasa este `.md` por chat cuando el agente la pide.
> **Resultado esperado:** WordCount Java compilado, empaquetado en `wordcount.jar`, ejecutado, resultado en `wordcount_java/part-r-00000`.

---

## Celda Markdown (a insertar antes de las celdas de código)

```markdown
## ☕ MapReduce en Java · Conteo de palabras (WordCount)

Esta plantilla está dividida en **tres archivos** Java separados que se compilan juntos:

- **`WordCount.java`** — Driver. Configura y lanza el job. Define qué clase es Mapper, qué clase es Reducer, tipos de entrada/salida y rutas.
- **`WordCountMapper.java`** — Mapper. Recibe cada línea del archivo y emite pares `(palabra, 1)`. Es la zona donde se hace **toda la lógica de tokenización y limpieza**.
- **`WordCountReducer.java`** — Reducer. Suma las apariciones de cada palabra. Casi nunca se modifica en un WordCount.

Los tres archivos se compilan juntos con `javac`, se empaquetan en un único `wordcount.jar` y se ejecutan apuntando a la carpeta `dataset/` (input) y `wordcount_java/` (output).
```

---

## Celda de código 1 · Driver (`WordCount.java`)

> **Adaptación:** ninguna obligatoria. Solo se modifica si el ejercicio lo pide explícitamente y siempre tras consultar al usuario.

```python
%%writefile WordCount.java
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCount {

    public static void main(String[] args) throws Exception {

        // REVISAR:
        // Hadoop espera normalmente dos argumentos:
        // args[0] = ruta de entrada
        // args[1] = ruta de salida
        if (args.length != 2) {
            System.err.println("Uso: WordCount <input path> <output path>");
            System.exit(-1);
        }

        Configuration conf = new Configuration();

        // CAMBIAR:
        // nombre del trabajo que se verá en Hadoop
        Job job = Job.getInstance(conf, "word count");

        // CAMBIAR:
        // debe coincidir con el nombre de esta clase principal
        job.setJarByClass(WordCount.class);

        // CAMBIAR:
        // si cambias el nombre del mapper o reducer, cámbialo aquí también
        job.setMapperClass(WordCountMapper.class);

        // REVISAR:
        // el combiner suele poder ser el mismo reducer en WordCount
        // si el ejercicio usa otra lógica, comprueba si sigue siendo válido
        job.setCombinerClass(WordCountReducer.class);
        job.setReducerClass(WordCountReducer.class);

        // REVISAR:
        // estos tipos deben coincidir con la salida final del reducer
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        // CAMBIAR SIEMPRE EN EJECUCIÓN:
        // ruta de entrada y ruta de salida en HDFS
        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

---

## Celda de código 2 · Mapper (`WordCountMapper.java`)

> **Adaptación posible (con consulta previa):** zonas marcadas `CAMBIAR CASI SIEMPRE` si el ejercicio pide limpiar puntuación, pasar a minúsculas, ignorar números, saltar cabecera, etc.

```python
%%writefile WordCountMapper.java
import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

public class WordCountMapper extends Mapper<Object, Text, Text, IntWritable> {

    // NORMALMENTE NO CAMBIAR: constante para emitir 1 por cada ocurrencia
    private final static IntWritable one = new IntWritable(1);

    // NORMALMENTE NO CAMBIAR: objeto reutilizable para la clave de salida
    private Text word = new Text();

    // MAPREDUCE llama a este método una vez por cada línea de entrada
    @Override
    public void map(Object key, Text value, Context context) throws IOException, InterruptedException {

        // CAMBIAR CASI SIEMPRE:
        // aquí decides cómo separar o interpretar cada línea del fichero
        // Para WordCount clásico usamos StringTokenizer sobre espacios
        StringTokenizer itr = new StringTokenizer(value.toString());

        while (itr.hasMoreTokens()) {

            // CAMBIAR CASI SIEMPRE:
            // si el ejercicio pide limpiar símbolos, pasar a minúsculas,
            // quitar comas, ignorar números o saltar cabecera,
            // esta es una de las zonas donde suele hacerse
            String token = itr.nextToken().trim();

            // REVISAR:
            // si quieres ignorar tokens vacíos o basura, filtra aquí
            if (!token.isEmpty()) {
                word.set(token);

                // CAMBIAR CASI SIEMPRE:
                // esta línea define qué pareja (clave, valor) emite el mapper
                // En WordCount emitimos (palabra, 1)
                context.write(word, one);
            }
        }
    }
}
```

---

## Celda de código 3 · Reducer (`WordCountReducer.java`)

> **Adaptación posible:** solo si el ejercicio pide otra agregación distinta a sumar (máximo, media, mínimo, concatenación). En WordCount estándar se queda como está.

```python
%%writefile WordCountReducer.java
import java.io.IOException;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

public class WordCountReducer extends Reducer<Text, IntWritable, Text, IntWritable> {

    // NORMALMENTE NO CAMBIAR: objeto reutilizable para la salida
    private IntWritable result = new IntWritable();

    // MAPREDUCE llama a reduce una vez por cada clave única
    @Override
    public void reduce(Text key, Iterable<IntWritable> values, Context context)
            throws IOException, InterruptedException {

        int sum = 0;

        // NORMALMENTE NO CAMBIAR en WordCount:
        // sumar todos los valores asociados a la misma palabra
        for (IntWritable val : values) {
            sum += val.get();
        }

        // CAMBIAR SOLO SI el ejercicio pide otra agregación:
        // por ejemplo máximo, media, mínimo, concatenación, etc.
        result.set(sum);

        // NORMALMENTE NO CAMBIAR:
        // emitimos (palabra, total)
        context.write(key, result);
    }
}
```

---

## Celda de código 4 · Compilar + empaquetar + ejecutar

```python
# Compilar los tres archivos juntos
!mkdir -p /tmp/classes
!javac -classpath `hadoop classpath` -d /tmp/classes WordCount.java WordCountMapper.java WordCountReducer.java

# Empaquetar en un único .jar
!jar -cvf wordcount.jar -C /tmp/classes/ .

# Limpiar carpeta de salida si existe (limpieza preventiva)
!hdfs dfs -rm -r wordcount_java 2>/dev/null || true

# Ejecutar el job sobre la carpeta dataset/ entera
!hadoop jar wordcount.jar WordCount dataset wordcount_java

# Verificar y mostrar resultado
print("\n--- Contenido de wordcount_java/ ---")
!hdfs dfs -ls wordcount_java
print("\n--- Primeras 20 líneas del resultado ---")
!hdfs dfs -cat wordcount_java/part-r-00000 | head -n 20
```

---

## Reglas de uso

- **El nombre de cada archivo Java debe coincidir con el nombre de su clase pública.** No tocar este invariante.
- **Si el agente quiere renombrar `WordCount` a otra cosa**, debe renombrar **los tres archivos**, las **clases**, todas las referencias y el `.jar` final. Si va a hacerlo, **pregunta al usuario antes**.
- **Adaptaciones del mapper:** si el ejercicio pide limpieza específica (puntuación, lowercasing, regex, salto de cabecera), el agente **propone los cambios al usuario en chat antes de aplicarlos**. No los aplica por iniciativa propia.
- **El input apunta a la carpeta** `dataset/`, no a un archivo concreto.
- **La carpeta de salida** se llama `wordcount_java` (descriptiva, no genérica).
- Si la compilación falla, el agente **avisa al usuario** con el error exacto, no parchea silenciosamente.
- **No duplicar.** Si esta plantilla ya está inyectada en el cuaderno (las celdas con `%%writefile` ya existen), no la vuelvas a escribir; modifica las celdas existentes si hace falta.
