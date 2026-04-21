# Guía de estilo blindada del cuaderno para Gemini en Google Colab

## Objetivo
Construir un cuaderno de examen o tarea práctica de BDA que sea limpio, corto, verificable y fácil de corregir.

El cuaderno no debe parecer una conversación, una clase, un informe largo ni un plan de trabajo. Debe parecer una resolución técnica clara y controlada.

## Regla general del cuaderno
Cada punto del enunciado debe resolverse con la estructura mínima necesaria.

El cuaderno debe avanzar en bloques pequeños, visibles y conectados entre sí.

No debe usarse como chat interno del agente.

## Estructura permitida por cada punto
Por cada punto aprobado solo se permite esta secuencia:

1. una única celda markdown,
2. una única celda de código,
3. una conclusión breve tras ejecutar.

No añadas más celdas salvo que el usuario lo pida de forma explícita.

## Formato obligatorio de la celda markdown
La celda markdown debe incluir solo:
- el texto literal del punto que se está resolviendo, o una versión mínima fiel si el enunciado es demasiado largo,
- una explicación breve, simple y directa de lo que se va a hacer en ese punto.

La celda markdown debe ser corta.

Debe evitar:
- planes,
- estrategias,
- objetivos generales,
- resúmenes del ejercicio,
- teoría,
- listas largas,
- texto decorativo,
- justificaciones innecesarias.

## Formato obligatorio de la celda de código
La celda de código debe:
- resolver solo el punto actual,
- ser lo más corta posible,
- usar nombres claros,
- evitar complejidad innecesaria,
- reutilizar solo lo necesario del contexto o de bloques anteriores,
- mostrar evidencia visible de que el paso funciona.

No debe:
- resolver varios puntos a la vez,
- introducir abstracciones innecesarias,
- crear funciones largas si no hacen falta,
- meter código de “por si acaso”,
- esconder el resultado sin mostrar nada.

## Necesidad de comprobación visible
Siempre que sea posible, la celda de código debe mostrar una comprobación visible.

Se considera comprobación visible válida, según el caso:
- `head()` o primeras filas,
- `shape`,
- `columns`,
- `info()`,
- conteo de nulos,
- resultados de un filtro,
- salida de `SHOW TABLES`,
- `DESCRIBE`,
- `COUNT(*)`,
- resultado de una consulta,
- mensaje claro de que una carga, unión o escritura ha funcionado.

No dejes celdas ciegas cuando puedas mostrar evidencia útil.

## Formato obligatorio de la conclusión
Tras ejecutar el bloque, la conclusión debe ser:
- muy breve,
- clara,
- en primera persona,
- centrada en el resultado obtenido,
- conectada con el siguiente paso.

Formato recomendado:
- qué he comprobado,
- qué ha salido,
- qué implica para continuar.

Ejemplos válidos:
- “He comprobado que el archivo se ha cargado bien y que hay nulos en dos columnas.”
- “He corregido los nulos de `merchant_state` y la salida ya se guarda sin error.”
- “He creado la tabla en Hive y ya puedo lanzar la siguiente consulta.”

Ejemplos no válidos:
- “Como inteligencia artificial...”
- “Procedemos estratégicamente a...”
- “En esta fase del roadmap...”

## Prohibiciones absolutas en el cuaderno
- No usar el cuaderno como chat.
- No crear celdas de Task, Plan, Strategy, Roadmap o similares.
- No crear varias celdas markdown seguidas.
- No meter teoría decorativa.
- No escribir conclusiones largas.
- No anticipar varios pasos futuros dentro del cuaderno.
- No resolver media tarea en una sola celda.
- No duplicar bloques que ya están resueltos.
- No mezclar explicación larga y código cuando bastan dos celdas.
- No añadir texto motivacional, académico o de relleno.

## Regla de continuidad
Cada bloque debe apoyarse claramente en el anterior.

Antes de pasar al siguiente punto debe quedar visible:
- qué se ha hecho,
- qué se ha comprobado,
- y qué queda pendiente.

Si un bloque genera una salida que alimenta al siguiente, eso debe quedar claro en la conclusión o en el propio código.

## Regla de simplicidad
Si una celda hace demasiado, divídela.

Si una explicación es larga, recórtala.

Si un bloque puede resolverse con una opción simple y otra compleja, usa la simple salvo que el enunciado obligue a otra cosa.

Si una parte del código no aporta valor visible para el punto actual, elimínala.

## Criterio final
El cuaderno debe poder leerse de arriba abajo como una resolución limpia y controlada:

punto → markdown breve → código útil → comprobación visible → conclusión breve → siguiente punto.

Si una celda no ayuda a resolver, comprobar o enlazar el siguiente paso, no debe estar en el cuaderno.
