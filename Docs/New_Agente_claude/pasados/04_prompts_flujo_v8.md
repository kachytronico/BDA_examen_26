# Prompts del flujo · Sistema BDA en Colab v8

Prompts en orden de uso. Cópialos y pégalos al chat de Gemini según el momento del flujo.

**Cambios respecto a v7:**
- **Prompt 2** ampliado: el agente comprueba primero si hay DataFrames cargados antes de explorar todo el directorio (mejora M012, evita exploración duplicada).
- **Prompt 6** reforzado: añadida regla explícita "si la conclusión ya termina con un dato concreto, detente, no añadas segunda frase de cierre" (mejora M008, problema persistente de conclusiones decoradas).
- **Prompt 7** simplificado: eliminada la actualización rutinaria de PLAN_TAREA tras cada apartado (mejora M013). Ahora PLAN_TAREA queda fijo tras el inicio salvo cambio real de plan.
- **Regla 4 del Gem** queda obsoleta en su mención a "actualiza Estado de avance de PLAN_TAREA"; al cargar el Gem hay que advertirlo o aceptar que la regla es soft.

---

## ÍNDICE DEL FLUJO

```
[INICIO]
  1. Inicio y carga de documentos
  2. Fase inicial: exploración estructural del dataset
  3. Análisis previo: rellenar PLAN_TAREA

[CICLO POR APARTADO — se repite por cada uno de los apartados pedidos]
  4. Analizar el siguiente apartado
  5. Autorizar el bloque (con aclaraciones)
  → ejecutas el código tú
  6. Pedir conclusión
  7. Pasar al siguiente apartado

[CIERRE]
  8. Auditoría final

[EMERGENCIA]
  9. Vuelve al protocolo
```

---

## 1 · Inicio y carga de documentos

> Cuándo: al empezar la sesión, justo después de pegar al chat el documento maestro y la guía de estilo.

```text
Te he pasado por chat dos documentos: el documento maestro y la guía de estilo. En el cuaderno hay dos tipos de celdas que debes localizar:

1. Una o más celdas marcadas como CONTEXTO_UD# con las reglas conceptuales fijas de cada unidad.
2. Una celda marcada como PLAN_TAREA con la lectura concreta del enunciado actual y el estado de avance (puede estar vacía/en plantilla al inicio).

Antes de tocar nada:

1. Confirma que has recibido el maestro y la guía por chat.
2. Confirma cuántas celdas CONTEXTO_UD# hay en el cuaderno y de qué unidades son.
3. Confirma que has localizado la celda PLAN_TAREA. Si no existe, dilo.
4. Lee el enunciado y dime:
   - qué has entendido del ejercicio en general (3-5 líneas),
   - cuántos apartados pide el enunciado en total,
   - una primera estimación de qué unidad pertenece cada apartado (mapa apartado ↔ unidad), si es relevante.
5. No propongas todavía qué apartado resolver primero.

Espera mi señal para pasar a la fase de exploración del dataset.
```

---

## 2 · Fase inicial: exploración estructural del dataset

> Cuándo: tras confirmar la carga de documentos. Aquí Gemini sí crea celda en el cuaderno, pero **solo de exploración estructural**, sin interpretar ni avanzar.

```text
Vamos con la fase inicial de exploración estructural del dataset. Esta celda no resuelve ningún apartado del enunciado, solo nos sirve para conocer los datos antes de empezar.

Antes de explorar todo el directorio, comprueba si ya hay DataFrames cargados en el espacio de variables (revisa qué `df_*` existen con `dir()` o similar). Si los hay, lístalos primero y completa la exploración solo con los archivos relevantes restantes — no rehagas el trabajo si los DataFrames principales ya están cargados.

Crea UNA celda de código (sin celda markdown previa) que:

- compruebe DataFrames ya cargados (si los hay, los lista),
- haga `os.walk` o `ls` sobre la ruta donde están los archivos,
- cargue cada dataset disponible que aún no esté cargado,
- muestre para cada uno: shape, head, info, dtypes, nulos, duplicados,
- y un inventario por columna: nombre, dtype, nº de únicos, nº de nulos, 3-5 ejemplos.

Reglas:
- NADA de gráficos.
- NADA de transformaciones, limpiezas ni imputaciones.
- NADA de interpretación todavía.
- NADA de proponer qué apartado resolver primero.

Después de crear la celda, párate y espera a que yo la ejecute.
```

---

## 3 · Análisis previo: rellenar PLAN_TAREA

> Cuándo: después de ejecutar el prompt 2 y ver la salida de la exploración. Antes de empezar el primer apartado.
>
> Por qué: la exploración produce salida cruda. Este prompt convierte esa salida + el enunciado en un PLAN_TAREA concreto que el agente consultará en cada apartado. Sin esto, el agente improvisaría en cada apartado.

```text
Vamos a rellenar la celda PLAN_TAREA con la lectura del enunciado y el análisis del dataset.

Basándote en el enunciado que has leído + los resultados de la exploración estructural:

1. **Mapa apartado ↔ unidad:** lista qué apartado del enunciado pertenece a qué unidad (UD1, UD2, etc.). Si el enunciado tiene una sola unidad, dilo.

2. **Requisitos materiales del enunciado actual:** cualquier requisito explícito del profesor en este enunciado concreto (datasets relacionados, mínimos de filas, formato de carga, etc.).

3. **Restricciones específicas:** cualquier instrucción particular del profesor para esta tarea concreta (variables a usar, exclusiones, etc.).

4. **Datos identificados:**
   - Dataset principal, secundario, enriquecimiento.
   - Clave(s) de integración.
   - Variable objetivo plausible para análisis estadísticos.
   - Columnas continuas (escalables) vs identificadores numéricos (no escalar).
   - Granularidad correcta para apartados estadísticos (PCA, hipótesis, anomalías) si aplica.

5. **Archivos a subir a HDFS (UD2/UD3) — si aplica:** lista todos los archivos físicos que el pipeline Hadoop debe procesar, con ruta de origen y nombre corto que tendrán en `/content/` y en la carpeta HDFS `dataset/`. Si el examen no tiene apartados Hadoop, deja este campo vacío.

6. **Estado de avance inicial:** todos los apartados en ⏳ pendiente.

Propón en chat (NO edites todavía la celda PLAN_TAREA) cómo rellenarías cada hueco [ADAPTAR] de la plantilla.

Si para alguno tienes dudas o ves varias opciones razonables, dilo claramente y pregúntame antes de fijarlo.

Cuando esté de acuerdo te daré OK para que edites la celda PLAN_TAREA.
```

---

## 4 · Analizar el siguiente apartado

> Cuándo: al empezar cada apartado del enunciado.

```text
Pasa al siguiente apartado del enunciado.

Antes de proponer nada:

1. Identifica qué apartado toca según el orden del enunciado y el "Estado de avance" de PLAN_TAREA.
2. Consulta PLAN_TAREA para identificar a qué unidad pertenece este apartado y los datos específicos a usar.
3. Consulta el CONTEXTO_UD# correspondiente y cita la regla aplicable a este apartado (literal o casi literal). Si el apartado pertenece a UD2 o UD3 y hay plantillas pre-cargadas relevantes, cita también qué plantilla aplica.
4. Dime en chat:
   - qué apartado toca y a qué unidad pertenece,
   - qué dice el CONTEXTO_UD# sobre ese apartado (con la cita),
   - qué plantilla pre-cargada aplica (si aplica),
   - qué bloque tocaría hacer adaptado a los datos de PLAN_TAREA,
   - qué evidencia visible vas a mostrar,
   - qué decisiones delicadas hay que necesiten mi confirmación.
5. Pregúntame si quiero que empieces por este apartado.

No toques el cuaderno todavía.
```

---

## 5 · Autorizar el bloque

> Cuándo: tras revisar la propuesta de Gemini, cuando ya quieres que cree las celdas.

```text
Tienes permiso para resolver solo este apartado.

Aclaraciones para este apartado:
[ESCRIBIR AQUÍ aclaraciones, decisiones que ya he tomado, columnas concretas a usar, alternativa elegida, etc. — o dejar vacío si no hace falta]

Crea exactamente:
- una celda markdown con el texto literal del apartado y una explicación simple,
- una celda de código si el apartado lo requiere (omítela si el apartado es puramente narrativo, como Explicación del dataset o NoSQL conceptual).

Sobre el alcance del bloque:
- "mínimo" significa "no añadir nada que el apartado no pida", NO significa "hacer lo más corto posible".
- Cumple TODO lo que el apartado pida explícitamente, incluyendo las indicaciones específicas del CONTEXTO_UD# para ese apartado (visualizaciones requeridas, granularidad de análisis, etc.).
- Si el apartado tiene plantilla pre-cargada (UD2/UD3), úsala respetando los marcadores `CAMBIAR CASI SIEMPRE` / `REVISAR` / `NORMALMENTE NO CAMBIAR`. No la regeneres.
- El código debe incluir comprobación visible por pantalla.

Nada más. No añadas conclusión todavía, no propongas el siguiente apartado, no anuncies lo que harás después. Cuando hayas creado las celdas, párate y espera a que yo ejecute.
```

---

## 6 · Pedir conclusión

> Cuándo: tras ejecutar el código y ver que el resultado es correcto.

```text
Ahora escribe solo la conclusión de este apartado.

Reglas de forma:
- celda de texto del cuaderno (no solo en chat),
- breve, sencillo y directo pero sin olvidar nada esencial de lo qeu pida el enunciado, que se cite si hay cosas pendientes o tareas para después.
- en primera persona del singular ("He..." o "Veo que...", NO "Hemos..." ni "Se ha...").
- ligada al resultado real que se ve en la salida,
- termina con un dato concreto observado en la salida (cifra, nombre de columna, recuento, p-valor, etc.).

**Regla de parada:** si tu conclusión ya termina con un dato concreto, detente. NO añadas una segunda frase de cierre, resumen, valoración general o comentario sobre la utilidad del paso. Una sola frase con el dato es suficiente.

Frases prohibidas al inicio (incumplen primera persona):
- "Se ha garantizado..."
- "Se han definido..."
- "Hemos garantizado..."
- "El proceso de... se basa en..."
- "Aplicando X... hemos logrado..."

Frases prohibidas al final (anuncian pasos futuros o decoran):
- "lo cual será clave para..."
- "permitiendo así..."
- "evitando así que..."
- "asegurando que..."
- "garantizando la..."
- "manteniendo la..."
- "demostrando ser una técnica muy efectiva..."
- "...nos permite observar..." (primera persona del plural prohibida)
- "...cerrando así la fase de..."
- "...con lo que hemos completado..."

No propongas el siguiente apartado, no añadas estrategia, solo la conclusión.
```

---

## 7 · Pasar al siguiente apartado

> Cuándo: tras escribir la conclusión, para enlazar con el siguiente apartado.

```text
Apartado completado. Pasa al siguiente apartado siguiendo el protocolo de análisis: cita la regla del CONTEXTO_UD# correspondiente, propón el bloque y pregúntame si empiezas.

No toques el cuaderno hasta mi OK.
```

> **Nota:** este prompt y el prompt 4 son funcionalmente equivalentes. El 7 es la versión corta para el bucle (cuando ya estamos en ritmo). Si necesitas más detalle, vuelve al 4.

> **PLAN_TAREA queda fijo tras el inicio.** El agente NO actualiza rutinariamente la tabla "Estado de avance" después de cada apartado (el progreso se ve mirando qué celdas existen en el cuaderno). Solo actualiza PLAN_TAREA si en algún apartado se toma una decisión que **cambia el plan inicial** (cambio de granularidad, cambio de variable objetivo, descubrimiento técnico que altera la estrategia). En ese caso, el agente lo dice en chat y pide OK antes de editar.

---

## 8 · Auditoría final

> Cuándo: cuando todos los apartados están terminados y antes de entregar.

```text
Vamos a hacer una auditoría final del cuaderno antes de entregar. No toques el cuaderno todavía.

Repasa el cuaderno entero y dime en chat:

1. **Cobertura del enunciado:** ¿están todos los apartados pedidos resueltos? Recorre el cuaderno y compara con el enunciado. Si falta alguno, dilo.
2. **Coherencia de cada apartado con su CONTEXTO_UD#:** para cada apartado, di si lo resuelto coincide con la interpretación del CONTEXTO_UD# correspondiente (consultado a través del mapa de PLAN_TAREA). Si algún apartado se resolvió con una interpretación distinta, márcalo.
3. **Granularidad de análisis (apartados estadísticos):** ¿se aplicaron al nivel correcto fijado en PLAN_TAREA?
4. **Limpieza del cuaderno:** ¿hay celdas Task, Plan, Strategy, Roadmap, Summary, Next step o similares (en español o inglés)? ¿hay celdas duplicadas o de planificación que sobren?
5. **Estilo de las conclusiones:** ¿alguna conclusión usa "Se ha...", primera persona del plural ("nos permite", "hemos completado"), gerundios decorativos al final ("asegurando...", "garantizando..."), o tono académico? ¿Alguna añade segunda frase de cierre tras el dato concreto? Lista cuáles.
6. **Comentarios del código:** ¿alguno explica lo evidente, suena a manual o sobra?
7. **Plantillas pre-cargadas (UD2/UD3 si aplica):** ¿se respetaron los nombres canónicos (carpeta HDFS `dataset/`, salidas descriptivas tipo `grep_<patrón>` y `wordcount_*`, archivos Java con nombres consistentes)?

Solo el informe en chat. No edites nada todavía. Después decidimos qué arreglar.
```

---

## 9 · Vuelve al protocolo (emergencia)

> Cuándo: cuando Gemini se descontrola — propone varios apartados, mete celdas Task/Plan, sigue por inercia, reinterpreta sin citar, inventa columnas que no existen, o cambia al inglés.

```text
Para. Te has salido del protocolo.

Olvida lo que acabas de proponer. Vuelve al ciclo:

- nada de varios apartados,
- nada de Task, Plan, Strategy, Roadmap, Summary, Next step,
- nada de seguir por inercia,
- nada de afirmar columnas o archivos sin haberlos visto en una celda ejecutada o en PLAN_TAREA.

Reformula en chat:
1. qué apartado estamos resolviendo y a qué unidad pertenece (consultar PLAN_TAREA),
2. qué dice el CONTEXTO_UD# sobre ese apartado (cítalo literal),
3. qué bloque toca,
4. si quiero que empieces.

No toques el cuaderno hasta mi OK.
```

---

## Resumen del uso típico de una sesión

| Momento | Prompt | Resultado esperado |
|---|---|---|
| Empiezas | **1** | Confirma docs + lee enunciado + mapa apartado-unidad |
| Tras confirmar | **2** | Celda de exploración del dataset |
| Antes del primer apartado | **3** | PLAN_TAREA rellenada con datos concretos |
| Por cada apartado: |  |  |
| · empezar | **4** o **7** | Análisis en chat con cita |
| · autorizar | **5** | Markdown + código en cuaderno |
| · *ejecutas el código* |  |  |
| · cerrar apartado | **6** | Conclusión en celda del cuaderno |
| · siguiente | **7** | Propone siguiente apartado |
| Cuaderno terminado | **8** | Informe de auditoría en chat |
| Si se descontrola | **9** | Recentrado |

**Total por apartado típico:** 3 prompts (4, 5, 6, opcionalmente 7) + ejecutar el código.

En la práctica, el agente suele anticipar el siguiente paso por lo que muchos prompts se fusionan en confirmaciones cortas. El número real de mensajes que escribes suele ser bastante menor.

---

## Reanudar una sesión interrumpida

Si tienes que reiniciar la conversación con Gemini a mitad de tarea:

1. Carga maestro + guía como en el inicio normal (prompt 1).
2. El agente, al ejecutar el prompt 1, leerá CONTEXTO_UD# **y** PLAN_TAREA. PLAN_TAREA + las celdas existentes en el cuaderno le dicen exactamente dónde estás.
3. Salta directamente al prompt 4 o 7 para el siguiente apartado pendiente.

No necesitas un protocolo especial: PLAN_TAREA + el cuaderno son el protocolo de reanudación.
