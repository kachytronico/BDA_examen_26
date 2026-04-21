# Guía de estilo blindada del cuaderno para Gemini en Colab

## Objetivo
Construir un cuaderno limpio, corto, lógico y fácil de revisar en examen.

## Regla general
El cuaderno debe contener **solo lo necesario para resolver**.
No debe parecer una clase, un informe largo ni un plan de trabajo.

## Estructura permitida por cada punto
Cada punto aprobado solo puede tener:

1. **Una celda markdown**
2. **Una celda de código**
3. **Una conclusión breve** después de ejecutar

No añadas más celdas salvo que yo lo pida.

## Celda markdown: formato obligatorio
La celda markdown debe contener:
- el **título literal del punto** que se está resolviendo,
- una explicación breve, sencilla y directa de lo que se va a hacer.

No debe contener:
- planes,
- listas largas,
- objetivos generales,
- teoría,
- roadmap,
- task,
- strategy,
- resumen del ejercicio,
- texto innecesario.

## Celda de código: formato obligatorio
La celda de código debe:
- resolver solo el punto actual,
- ser lo más corta posible,
- incluir una comprobación visible por pantalla,
- usar nombres claros,
- evitar complejidad innecesaria.

## Comprobación visible
Siempre que sea posible, el código debe mostrar algo útil:
- primeras filas,
- columnas,
- shape,
- conteos,
- nulos,
- resultados de consulta,
- o evidencia clara de que funcionó.

No dejar bloques ciegos.

## Conclusión: formato obligatorio
La conclusión debe ser:
- muy breve,
- en primera persona,
- clara,
- conectada con el siguiente paso.

Ejemplo válido:
“He comprobado que el archivo se ha cargado bien y que hay nulos en dos columnas.”

Ejemplo no válido:
“Como inteligencia artificial...”
“Procedemos a...”
“En esta fase estratégica...”

## Prohibiciones absolutas en el cuaderno
- No crear celdas de Task o Plan
- No crear celdas de Strategy o Roadmap
- No crear varias celdas markdown seguidas
- No meter teoría decorativa
- No meter conclusiones largas
- No explicar tres pasos futuros en el cuaderno
- No usar el notebook como chat

## Regla de continuidad
Cada bloque debe apoyarse en el anterior.
Antes de pasar al siguiente punto debe quedar claro:
- qué se ha hecho,
- qué se ha comprobado,
- y qué falta.

## Regla de simplicidad
Si una celda hace demasiado, divídela.
Si una explicación es larga, recórtala.
Si un bloque es complejo, simplifícalo antes de escribirlo.

## Criterio final
El cuaderno debe poder leerse de arriba abajo como una resolución limpia:
- punto,
- código,
- comprobación,
- conclusión,
- siguiente punto.
