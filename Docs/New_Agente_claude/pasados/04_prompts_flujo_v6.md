# Prompts del flujo · Sistema BDA en Colab v6

Prompts en orden de uso. Cópialos y pégalos al chat de Gemini según el momento del flujo.

**El flujo es lineal:** prompts 1, 2 y 3 al inicio (carga, exploración, análisis previo del dataset); prompts 4-5-6-7 se repiten en bucle por cada apartado del enunciado; prompt 8 al final. El prompt 9 es de emergencia, solo si Gemini se descontrola.

**Cambios respecto a v5:**
- Añadido prompt 3 (análisis previo) al flujo principal, antes era un improvisado en el simulacro.
- Prompt 5 (autorizar bloque): celda de código ahora condicional; aclaración de "bloque mínimo" añadida.
- Prompt 6 (conclusión): reforzado con ejemplos negativos de tono académico.
- Numeración de prompts ajustada al nuevo flujo.

---

## ÍNDICE DEL FLUJO

```
[INICIO]
  1. Inicio y carga de documentos
  2. Fase inicial: exploración estructural del dataset
  3. Análisis previo y reglas del dataset (rellenar sección 9 del CONTEXTO_UD#)

[CICLO POR APARTADO — se repite por cada uno de los 12 apartados]
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
Te he pasado por chat dos documentos: el documento maestro y la guía de estilo. El contexto de la unidad está en una celda markdown del cuaderno marcada como CONTEXTO_UD#.

Antes de tocar nada:

1. Confirma que has recibido el maestro y la guía por chat.
2. Confirma que has localizado la celda CONTEXTO_UD# en el cuaderno y dime cuál es su unidad.
3. Lee el enunciado del cuaderno y dime qué has entendido del ejercicio en general (3-5 líneas, sin entrar en apartados concretos todavía).
4. No propongas todavía qué apartado resolver primero.

Espera mi señal para pasar a la fase de exploración del dataset.
```

---

## 2 · Fase inicial: exploración estructural del dataset

> Cuándo: tras confirmar la carga de documentos. Aquí Gemini sí crea celda en el cuaderno, pero **solo de exploración estructural**, sin interpretar ni avanzar.

```text
Vamos con la fase inicial de exploración estructural del dataset. Esta celda no resuelve ningún apartado del enunciado, solo nos sirve para conocer los datos antes de empezar.

Crea UNA celda de código (sin celda markdown previa) que:

- haga `os.walk` o `ls` sobre la ruta donde están los archivos,
- cargue cada dataset disponible,
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

## 3 · Análisis previo y reglas del dataset

> Cuándo: después de ejecutar el prompt 2 y ver la salida de la exploración. Antes de empezar el apartado 1.
>
> Por qué: la exploración del prompt 2 produce salida cruda. Este prompt convierte esa salida en reglas concretas del dataset que el agente aplicará en cada apartado. Rellena la sección 9 del CONTEXTO_UD#, que de otro modo quedaría vacía y forzaría al agente a improvisar en cada apartado.

```text
Antes de empezar con los apartados del enunciado, vamos a analizar a fondo el dataset y rellenar la sección 9 del CONTEXTO_UD#.

Basándote en los resultados de la exploración estructural que ya ejecuté:

1. Identifica los datasets disponibles y propón cuál usaremos como:
   - principal (el más grande o central),
   - secundario (el que se va a integrar con el principal),
   - enriquecimiento (tercer dataset o API para el apartado 6).

2. Para los datasets que vamos a usar:
   - identifica la(s) clave(s) común(es) para integración,
   - separa columnas numéricas continuas (candidatas a escalar) de columnas que parecen numéricas pero son identificadores,
   - identifica una variable objetivo plausible para validación cruzada y testeo de hipótesis,
   - señala columnas problemáticas (espacios, nulos altos, tipos raros) que requieran limpieza antes de algún apartado.

3. Propón en el chat (NO edites todavía la celda CONTEXTO_UD#) cómo rellenarías cada hueco [ADAPTAR] de la sección 9.

4. Si para alguno tienes dudas o ves varias opciones razonables, dilo claramente y pregúntame antes de fijarlo.

Cuando esté de acuerdo con tu propuesta te daré OK para que edites la celda CONTEXTO_UD#.
```

---

## 4 · Analizar el siguiente apartado

> Cuándo: al empezar cada apartado del enunciado.

```text
Pasa al siguiente apartado del enunciado.

Antes de proponer nada:

1. Identifica qué apartado toca según el orden del enunciado.
2. Consulta la celda CONTEXTO_UD# del cuaderno y cita la regla aplicable a este apartado (literal o casi literal).
3. Dime en chat:
   - qué apartado toca,
   - qué dice el contexto sobre ese apartado (con la cita),
   - qué bloque tocaría hacer,
   - qué evidencia visible vas a mostrar,
   - qué decisiones delicadas hay que necesiten mi confirmación.
4. Pregúntame si quiero que empieces por este apartado.

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
- breve (1-3 líneas),
- en primera persona del singular ("He..." o "Veo que...", NO "Hemos..." ni "Se ha...").
- ligada al resultado real que se ve en la salida.

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

Termina con un dato concreto observado en la salida (cifra, nombre de columna, recuento, p-valor, etc.).

No propongas el siguiente apartado, no añadas estrategia, solo la conclusión.
```

---

## 7 · Pasar al siguiente apartado

> Cuándo: tras escribir la conclusión, para enlazar con el siguiente apartado.

```text
Apartado completado. Pasa al siguiente apartado del enunciado siguiendo el protocolo de análisis: cita la regla del CONTEXTO_UD#, propón el bloque y pregúntame si empiezas.

No toques el cuaderno hasta mi OK.
```

> **Nota:** este prompt y el prompt 4 son funcionalmente equivalentes. El 7 es la versión corta para el bucle (cuando ya estamos en ritmo). Si necesitas más detalle, vuelve al 4.

---

## 8 · Auditoría final

> Cuándo: cuando los 12 apartados están terminados y antes de entregar.

```text
Vamos a hacer una auditoría final del cuaderno antes de entregar. No toques el cuaderno todavía.

Repasa el cuaderno entero y dime en chat:

1. **Cobertura del enunciado:** ¿están los 12 apartados resueltos? Si falta alguno, dilo.
2. **Coherencia de cada apartado con su regla del CONTEXTO_UD#:** para cada apartado, di si lo resuelto coincide con la interpretación del contexto. Si algún apartado se resolvió con una interpretación distinta, márcalo.
3. **Granularidad de análisis (apartados 8, 10, 11):** ¿se aplicaron al nivel de granularidad correcto (episodio agregado vs línea de diálogo)?
4. **Limpieza del cuaderno:** ¿hay celdas Task, Plan, Strategy, Roadmap, Summary, Next step o similares (en español o inglés)? ¿hay celdas duplicadas o de planificación que sobren?
5. **Estilo de las conclusiones:** ¿alguna conclusión usa "Se ha...", gerundios decorativos al final ("asegurando...", "garantizando...") o tono académico? Lista cuáles.
6. **Comentarios del código:** ¿alguno explica lo evidente, suena a manual o sobra?
7. **Datasets y enriquecimiento:** ¿hay al menos dos datasets relacionados? ¿hay un tercer dataset o API para enriquecer (apartado 6)?

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
- nada de afirmar columnas o archivos sin haberlos visto en una celda ejecutada.

Reformula en chat:
1. qué apartado estamos resolviendo,
2. qué dice el CONTEXTO_UD# sobre ese apartado (cítalo literal),
3. qué bloque toca,
4. si quiero que empieces.

No toques el cuaderno hasta mi OK.
```

---

## Resumen del uso típico de una sesión

| Momento | Prompt | Resultado esperado |
|---|---|---|
| Empiezas | **1** | Confirma docs + lee enunciado |
| Tras confirmar | **2** | Celda de exploración del dataset |
| Antes del apartado 1 | **3** | Análisis previo + sección 9 rellenada |
| Por cada apartado: |  |  |
| · empezar | **4** o **7** | Análisis en chat con cita |
| · autorizar | **5** | Markdown + código en cuaderno |
| · *ejecutas el código* |  |  |
| · cerrar apartado | **6** | Conclusión en celda del cuaderno |
| · siguiente | **7** | Vuelve al ciclo |
| Cuaderno terminado | **8** | Informe de auditoría en chat |
| Si se descontrola | **9** | Recentrado |

**Total por apartado típico:** 4 prompts (4, 5, 6, 7) + ejecutar el código.
**Total por sesión completa:** 1 + 2 + 3 + (12 × 4) + 8 = **62 prompts**, sin contar emergencias.

En la práctica, el agente suele anticipar el siguiente paso ("añade conclusión y procede con el apartado X") por lo que muchos prompts se fusionan en confirmaciones cortas. El número real de mensajes que escribes suele ser bastante menor.
