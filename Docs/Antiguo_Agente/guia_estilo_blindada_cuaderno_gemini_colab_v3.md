# Guía de estilo blindada del cuaderno para Gemini en Colab
## Cómo debe verse y escribirse el notebook final de BDA

## Objetivo
Construir un cuaderno limpio, corto, lógico, fácil de revisar y defendible en examen.

Este documento no controla la estrategia general.  
Controla **cómo debe quedar el notebook**.

---

## Regla general del cuaderno
El cuaderno debe contener **solo lo necesario para resolver**.

No debe parecer:
- una clase,
- un informe largo,
- una conversación,
- un diario de trabajo,
- ni una planificación.

Debe poder leerse de arriba abajo como una resolución limpia.

---

## Estructura permitida por cada punto
Cada punto aprobado solo puede tener:

1. una celda markdown,
2. una celda de código,
3. una conclusión breve.

No añadas más celdas salvo que yo lo pida.

---

## Formato obligatorio de la celda markdown
La celda markdown debe contener:

- el **título literal del punto** que se está resolviendo,
- una explicación breve, sencilla y directa de lo que se va a hacer.

### Debe ser
- corta,
- clara,
- útil,
- natural,
- fácil de leer.

### No debe contener
- planes,
- listas largas,
- objetivos generales,
- teoría,
- roadmap,
- task,
- strategy,
- resumen del ejercicio,
- texto innecesario,
- ni frases de gestión.

---

## Formato obligatorio de la celda de código
La celda de código debe:

- resolver solo el punto actual,
- ser lo más corta posible,
- incluir una comprobación visible por pantalla,
- usar nombres claros,
- evitar complejidad innecesaria.

### Debe mostrar evidencia visible
Siempre que sea posible, el código debe enseñar algo útil:
- primeras filas,
- columnas,
- shape,
- conteos,
- nulos,
- medias,
- escalado,
- resultados de consulta,
- o evidencia clara de que funcionó.

No dejar bloques ciegos.

---

## Formato obligatorio de la conclusión
La conclusión debe ser:

- muy breve,
- en primera persona,
- sencilla,
- humanizada,
- directa,
- conectada con el resultado del punto.

### Ejemplo válido
“He comprobado que el archivo se ha cargado bien y que hay nulos en dos columnas.”

### Ejemplo no válido
- “Como inteligencia artificial…”
- “Procedemos a…”
- “En esta fase estratégica…”
- “De cara a pasos posteriores…”

---

## Estilo obligatorio del texto del cuaderno
Todo texto del cuaderno debe quedar:

- en primera persona,
- sencillo,
- humanizado,
- directo,
- sin tono académico,
- sin sonar a IA,
- sin adornos,
- sin repetir explicaciones obvias.

### Regla práctica
Si un texto suena:
- robótico,
- excesivamente técnico,
- demasiado largo,
- o demasiado “formal de manual”,

debe simplificarse.

---

## Prohibiciones absolutas en el cuaderno
Está prohibido:

- usar el cuaderno como chat,
- crear celdas de Task o Plan,
- crear celdas de Strategy o Roadmap,
- crear varias celdas markdown seguidas,
- meter teoría decorativa,
- meter conclusiones largas,
- explicar tres pasos futuros en el cuaderno,
- narrar lo que harás,
- dejar texto de gestión dentro del notebook.

---

## Regla de continuidad
Cada bloque debe apoyarse en el anterior.

Antes de pasar al siguiente punto debe quedar claro:
- qué se ha hecho,
- qué se ha comprobado,
- y qué falta.

Si un bloque no deja eso claro, debe simplificarse o corregirse.

---

## Regla de simplicidad
Si una celda hace demasiado, divídela solo si yo lo autorizo.  
Si una explicación es larga, recórtala.  
Si un comentario es obvio, elimínalo.

La simplicidad manda sobre el lucimiento.

---

## Regla de estilo para comentarios en código
Los comentarios dentro del código deben ser:

- cortos,
- claros,
- útiles,
- en español,
- sencillos,
- directos.

### No deben
- explicar lo evidente,
- duplicar lo que ya se ve en el código,
- sonar a manual,
- sonar a IA,
- ni convertirse en mini párrafos.

Si un comentario no aporta valor real, debe eliminarse.

---

## Fase final opcional de depuración editorial
Solo si yo lo pido, puedes hacer una pasada final de estilo.

## En esa pasada puedes
- reescribir markdown,
- simplificar conclusiones,
- humanizar el tono,
- acortar comentarios del código,
- eliminar comentarios innecesarios.

## En esa pasada no puedes
- crear nuevas celdas,
- mover bloques,
- cambiar lógica,
- alterar resultados,
- tocar el código más allá de comentarios.

---

## Criterio final
El cuaderno debe poder leerse de arriba abajo así:

- punto,
- explicación mínima,
- código,
- evidencia,
- conclusión breve,
- siguiente punto.

Y todo debe sonar como si lo hubiera escrito yo de forma simple, clara y natural.
