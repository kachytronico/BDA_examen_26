# Guía de estilo del cuaderno · Sistema BDA en Colab v4

## Objetivo
Construir un cuaderno limpio, corto, lógico, fácil de revisar y defendible en examen. Este documento controla **cómo debe quedar el notebook**, no la estrategia general.

---

## Regla general
El cuaderno debe contener **solo lo necesario para resolver**.

No debe parecer una clase, un informe largo, una conversación, un diario de trabajo ni una planificación. Debe poder leerse de arriba abajo como una resolución limpia.

---

## Estructura permitida por cada punto

Cada punto aprobado tiene **exactamente tres celdas**:

1. **una celda markdown** (encabezado del punto + explicación breve),
2. **una celda de código** (resolución del punto + comprobación visible),
3. **una celda de texto con la conclusión** breve, en primera persona.

No añadas más celdas salvo que yo lo pida.

### La conclusión va en celda del cuaderno
Tras ejecutar y leer la salida, escribes la conclusión **directamente como celda de texto del cuaderno**, no solo en el chat. La conclusión es parte del cuaderno final, no un comentario lateral.

---

## Formato de la celda markdown

Debe contener:
- el **título literal del punto** que se está resolviendo,
- una explicación breve y directa de qué se va a hacer.

### Debe ser
- corta (3-6 líneas),
- clara,
- útil,
- en español natural.

### No debe contener
- planes, listas largas, objetivos generales,
- teoría, roadmap, task, strategy, summary,
- frases de gestión ("vamos a proceder a..."),
- texto innecesario.

---

## Formato de la celda de código

Debe:
- resolver **solo** el punto actual,
- ser lo más corta posible,
- incluir una **comprobación visible** por pantalla,
- usar nombres claros,
- evitar complejidad innecesaria.

### Comprobación visible obligatoria
El código debe enseñar algo: `head()`, `info()`, `shape`, conteos, nulos, medias, escalado, resultados de consulta, `printSchema()`, `SHOW TABLES`, `COUNT(*)`. **No dejes bloques ciegos.**

---

## Formato de la conclusión

Debe ser:
- muy breve (1-3 líneas),
- en primera persona,
- humanizada, directa,
- ligada al resultado real del bloque.

### Ejemplos válidos
- *"He comprobado que el archivo se ha cargado bien y que hay nulos en dos columnas."*
- *"He escalado las dos columnas numéricas continuas con StandardScaler. La media queda en 0 y la desviación en 1."*
- *"He confirmado que el join genera 32.519 filas, una por línea de diálogo, sin pérdida."*

### Ejemplos NO válidos (todos vistos en sesiones reales)
- *"...asegurando que la base del proceso ETL es sólida y coherente con el enunciado."* → tono pomposo, frase de cierre vacía
- *"...garantizando la consistencia lógica de la información antes de proceder con la integración."* → gerundio decorativo + anuncio de pasos
- *"Esta detección es clave para el proceso ETL, ya que permite aislar registros que podrían sesgar análisis predictivos."* → justificación genérica
- *"Como inteligencia artificial..."*, *"Procedemos a..."*, *"En esta fase estratégica..."* → tono de manual

### Patrón a evitar
Conclusión que termine con gerundios + sustantivos abstractos: *"asegurando la integridad...", "garantizando la consistencia...", "manteniendo la trazabilidad..."*. Esto suena a IA y no aporta nada al cuaderno.

### Patrón correcto
Conclusión que termine con un dato concreto del resultado: *"...y la media queda en 0,003"*, *"...y obtengo 32.519 filas"*, *"...y no hay duplicados"*.

---

## Estilo del texto del cuaderno

Todo texto del cuaderno debe quedar:
- en primera persona,
- sencillo, humanizado, directo,
- sin tono académico,
- sin sonar a IA,
- sin adornos, sin repetir lo obvio.

### Regla práctica
Si un texto suena robótico, demasiado técnico, demasiado largo o demasiado formal, **simplifícalo**.

---

## Prohibiciones absolutas en el cuaderno

Está prohibido:
- usar el cuaderno como chat,
- crear celdas con títulos **Task, Plan, Strategy, Roadmap, Checklist, Summary, Next step** (también en inglés),
- crear varias celdas markdown seguidas,
- meter teoría decorativa,
- meter conclusiones largas,
- explicar tres pasos futuros en el cuaderno,
- narrar lo que harás,
- dejar texto de gestión dentro del notebook.

---

## Comentarios en código

Los comentarios deben ser:
- cortos,
- claros,
- útiles,
- en español,
- directos.

### No deben
- explicar lo evidente,
- duplicar lo que el código ya muestra,
- sonar a manual,
- convertirse en mini párrafos.

Si un comentario no aporta valor real, **elimínalo**.

---

## Continuidad entre bloques

Cada bloque debe apoyarse en el anterior. Antes de pasar al siguiente punto debe quedar claro qué se hizo, qué se comprobó y qué falta.

Si un bloque no deja eso claro, debe simplificarse o corregirse.

---

## Simplicidad sobre lucimiento

Si una celda hace demasiado, divídela solo si yo lo autorizo.
Si una explicación es larga, recórtala.
Si un comentario es obvio, elimínalo.

**La simplicidad manda sobre el lucimiento.**

---

## Fase final opcional de depuración editorial

Solo si yo lo pido, puedes hacer una pasada final de estilo.

### En esa pasada SÍ puedes
- reescribir markdown,
- simplificar conclusiones,
- humanizar el tono,
- reescribir comentarios del código.

### En esa pasada NO puedes
- crear celdas nuevas,
- mover bloques,
- cambiar lógica,
- alterar resultados,
- tocar el código más allá de comentarios.

---

## Criterio final

El cuaderno debe poder leerse de arriba abajo así:

> punto → explicación mínima → código → evidencia → conclusión breve → siguiente punto.

Y todo debe sonar como si lo hubiera escrito yo de forma simple, clara y natural.
