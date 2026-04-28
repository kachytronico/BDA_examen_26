# Prompts del flujo · Sistema BDA en Colab v5

Prompts en orden de uso. Cópialos y pégalos al chat de Gemini según el momento del flujo.

**El flujo es lineal:** prompts 1 y 2 al inicio; prompts 3-4-5-6 se repiten en bucle por cada apartado del enunciado; prompt 7 al final. El prompt 8 es de emergencia, solo si Gemini se descontrola.

---

## ÍNDICE DEL FLUJO

```
[INICIO]
  1. Inicio y carga de documentos
  2. Fase inicial: conocer el dataset

[CICLO POR APARTADO — se repite por cada uno de los 12 apartados]
  3. Analizar el siguiente hito
  4. Autorizar el bloque (con aclaraciones)
  → ejecutas el código tú
  5. Pedir conclusión
  6. Pasar al siguiente hito

[CIERRE]
  7. Auditoría final

[EMERGENCIA]
  8. Vuelve al protocolo
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

## 2 · Fase inicial: conocer el dataset

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

## 3 · Analizar el siguiente hito

> Cuándo: al empezar cada apartado del enunciado.

```text
Pasa al siguiente apartado del enunciado.

Antes de proponer nada:

1. Identifica qué apartado toca según el orden del enunciado.
2. Consulta la celda CONTEXTO_UD# del cuaderno y cita la regla aplicable a este apartado (literal o casi literal).
3. Dime en chat:
   - qué apartado toca,
   - qué dice el contexto sobre ese apartado (con la cita),
   - qué bloque mínimo tocaría hacer,
   - qué evidencia visible vas a mostrar,
   - qué decisiones delicadas hay que necesiten mi confirmación.
4. Pregúntame si quiero que empieces por este apartado.

No toques el cuaderno todavía.
```

---

## 4 · Autorizar el bloque

> Cuándo: tras revisar la propuesta de Gemini, cuando ya quieres que cree las celdas.

```text
Tienes permiso para resolver solo este apartado.

Aclaraciones para este apartado:
[ESCRIBIR AQUÍ aclaraciones, decisiones que ya he tomado, columnas concretas a usar, alternativa elegida, etc. — o dejar vacío si no hace falta]

Crea exactamente:
- una celda markdown con el texto literal del apartado y una explicación simple,
- una celda de código mínima con comprobación visible.

Nada más. No añadas conclusión todavía, no propongas el siguiente apartado, no anuncies lo que harás después. Cuando hayas creado las dos celdas, párate y espera a que yo ejecute.
```

---

## 5 · Pedir conclusión

> Cuándo: tras ejecutar el código y ver que el resultado es correcto.

```text
Ahora escribe solo la conclusión de este apartado.

Reglas:
- celda de texto del cuaderno (no solo en chat),
- breve (1-3 líneas),
- en primera persona,
- ligada al resultado real que se ve en la salida,
- termina con un dato concreto (cifra, columna, recuento), no con frases tipo "asegurando que...", "garantizando la...".

No propongas el siguiente apartado, no añadas estrategia, solo la conclusión.
```

---

## 6 · Pasar al siguiente hito

> Cuándo: tras escribir la conclusión, para enlazar con el siguiente apartado.

```text
Apartado completado. Pasa al siguiente apartado del enunciado siguiendo el protocolo de análisis: cita la regla del CONTEXTO_UD#, propón el bloque mínimo y pregúntame si empiezas.

No toques el cuaderno hasta mi OK.
```

> **Nota:** este prompt y el prompt 3 son funcionalmente equivalentes. El 6 es la versión corta para el bucle (cuando ya estamos en ritmo). Si necesitas más detalle, vuelve al 3.

---

## 7 · Auditoría final

> Cuándo: cuando los 12 apartados están terminados y antes de entregar.

```text
Vamos a hacer una auditoría final del cuaderno antes de entregar. No toques el cuaderno todavía.

Repasa el cuaderno entero y dime en chat:

1. **Cobertura del enunciado:** ¿están los 12 apartados resueltos? Si falta alguno, dilo.
2. **Coherencia de cada apartado con su regla del CONTEXTO_UD#:** para cada apartado, di si lo resuelto coincide con la interpretación del contexto. Si algún apartado se resolvió con una interpretación distinta, márcalo.
3. **Limpieza del cuaderno:** ¿hay celdas Task, Plan, Strategy, Roadmap, Summary, Next step o similares (en español o inglés)? ¿hay celdas duplicadas o de planificación que sobren?
4. **Estilo de las conclusiones:** ¿alguna conclusión usa gerundios decorativos al final ("asegurando...", "garantizando...") o tono académico? Lista cuáles.
5. **Comentarios del código:** ¿alguno explica lo evidente, suena a manual o sobra?
6. **Datasets y enriquecimiento:** ¿hay al menos dos datasets relacionados? ¿hay un tercer dataset o API para enriquecer (apartado 6)?

Solo el informe en chat. No edites nada todavía. Después decidimos qué arreglar.
```

---

## 8 · Vuelve al protocolo (emergencia)

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
3. qué bloque mínimo toca,
4. si quiero que empieces.

No toques el cuaderno hasta mi OK.
```

---

## Resumen del uso típico de una sesión

| Momento | Prompt | Resultado esperado |
|---|---|---|
| Empiezas | **1** | Confirma docs + lee enunciado |
| Tras confirmar | **2** | Celda de exploración del dataset |
| Por cada apartado: |  |  |
| · empezar | **3** o **6** | Análisis en chat con cita |
| · autorizar | **4** | Markdown + código en cuaderno |
| · *ejecutas el código* |  |  |
| · cerrar apartado | **5** | Conclusión en celda del cuaderno |
| · siguiente | **6** | Vuelve al ciclo |
| Cuaderno terminado | **7** | Informe de auditoría en chat |
| Si se descontrola | **8** | Recentrado |

**Total por apartado:** 4 prompts (3, 4, 5, 6) + ejecutar el código.
**Total por sesión completa:** 1 + 2 + (12 × 4) + 7 = **58 prompts**, sin contar emergencias.

Si tienes que pegar 58 prompts manualmente puede cansar. Lo que sí compensa: tener los prompts 3, 4, 5, 6 a mano (como snippets del editor o en una pestaña aparte) para pegarlos rápido.
