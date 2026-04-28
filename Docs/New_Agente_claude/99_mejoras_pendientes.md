# Mejoras pendientes del sistema BDA

> **Para qué sirve este documento:** registrar mejoras detectadas durante simulacros reales para revisarlas con cabeza fría al terminar la sesión, no al calor del momento.
>
> **Filtro obligatorio antes de aplicar cualquier mejora:** ¿añade complejidad? Si sí, ¿el beneficio justifica el coste? La investigación es clara: superar ~7 reglas o subdividir el flujo aumenta el riesgo de modo automático. Cuando dudes, descarta.

> **Estados:** propuesta · validar en próximo simulacro · aceptada · descartada · aplicada
> **Prioridades:** alta · media · baja

---

## M001 · Falta paso de análisis previo del dataset entre exploración y primer apartado ✅ CONFIRMADA

**Detectada:** 2026-04-28, simulacros 1 y 2 BDA01 con sistema v5
**Estado:** validada en simulacro 3 · pendiente integrar al documento oficial
**Prioridad:** alta

### Problema observado
El prompt 2 produce una celda de exploración estructural con output crudo. Pero el sistema NO convierte esa salida en reglas estructuradas del dataset. La sección 9 del CONTEXTO_UD# está pensada para eso pero el sistema v5 no fuerza rellenarla.

### Validación en simulacro 3
Aplicado el prompt 2-bis tras el prompt 2. El agente:
- identificó roles de los 3 datasets (principal/secundario/enriquecimiento) con justificación,
- detectó problema real de cruce con el tercer dataset (Episode_Number absoluto vs season/episode),
- distinguió correctamente continuas (Rating, Runtime) de identificadores (season, episode, line, Episode_Number),
- propuso variable derivada (longitud_dialogo) por iniciativa propia,
- hizo dos preguntas concretas sobre decisiones que no podía tomar solo (clave de unión, tipo de join),
- editó la celda CONTEXTO_UD1 limpiamente tras el OK del usuario.

Resultado: la sección 9 quedó con información concreta y citable. El test ácido del apartado 4 (normalización) será determinante.

### Acciones pendientes al cerrar el simulacro
1. Integrar el prompt 2-bis al `04_prompts_flujo_v5.md` definitivo.
2. Decidir nomenclatura: "2-bis" o renumerar todo el flujo.
3. Actualizar la sección 6 del documento maestro de recreación (00) para reflejar el nuevo paso.
4. Actualizar plantilla genérica de contexto de unidad (05) y CONTEXTO_UD1 (06) — ver M004.

---

## M004 · Eliminar prompt redundante del CONTEXTO_UD1 (y de la plantilla)

**Detectada:** 2026-04-28, simulacro 3 BDA01
**Estado:** propuesta clara, aplicable al cerrar simulacro
**Prioridad:** media

### Problema observado
El CONTEXTO_UD1 (y la plantilla genérica 05) terminan con un prompt sugerido para que el usuario pida al agente rellenar la sección 9. Este prompt fue diseñado cuando el rellenado era opcional y manual.

Con la incorporación del prompt 2-bis al flujo (M001), el rellenado pasa a ser parte estándar del proceso. El prompt al final del CONTEXTO_UD1 es **redundante** con el prompt 2-bis.

### Riesgo de mantenerlo
- Dos prompts ligeramente distintos para la misma tarea (confunde).
- Si alguien lee solo el CONTEXTO_UD1 sin conocer el prompt 2-bis, puede usar el redundante y saltarse pasos del flujo.

### Acción
1. Eliminar la sección "Prompt para que el agente te ayude a rellenar esta sección" del CONTEXTO_UD1 (06) y de la plantilla genérica (05).
2. Sustituir por una nota corta tipo: *"Esta sección la rellenará el agente con el prompt 2-bis del flujo."*

---

## M005 · Prompt 4 fuerza celda de código incluso en apartados narrativos

**Detectada:** 2026-04-28, simulacro 3 BDA01
**Estado:** detectada y resuelta manualmente por usuario
**Prioridad:** media

### Problema observado
El prompt 4 ("autorizar bloque") tiene plantilla fija con "una celda markdown + una celda de código". Pero hay apartados puramente narrativos donde la celda de código es artificial:
- Apartado 1 (Explicación del dataset)
- Apartado 12 (NoSQL conceptual)
- Posibles apartados 2 (Carga, según el caso)

El usuario tuvo que adaptar el prompt manualmente eliminando la línea de la celda de código.

### Acción
Reformular el prompt 4 con una versión condicional:
- Mantener "una celda markdown + una celda de código si el apartado lo requiere",
- O añadir nota: "si el apartado es puramente narrativo y no necesita código, omite la celda de código".

Validar que el agente entienda la condición sin liarla.

---

## M006 · Aclarar el significado de "bloque mínimo" en el prompt 4

**Detectada:** 2026-04-28, simulacro 3 BDA01
**Estado:** propuesta
**Prioridad:** media

### Problema observado
La palabra "mínimo" puede ser ambigua. Significa:
- (intención original) no añadir cosas que sobran, no irse por las ramas,
- (lectura posible) hacer muy poco aunque el apartado pida más.

En apartados como Perfilado (3) o Validación (5), una sola línea de código no cumple lo que el apartado pide. El agente puede leer "mínimo" como "lo más corto posible" y quedarse corto.

### Tensión a tener en cuenta
**Mantener "mínimo" tal cual:** el principal problema de Gemini ha sido excederse (Task/Plan, conclusiones largas, decoración académica). Suavizar "mínimo" puede abrir la puerta a sobreproducción.

**Cambiarlo a "lo necesario":** evita quedarse corto pero pierde el freno contra excesos.

### Solución intermedia propuesta
No quitar "mínimo". Añadir aclaración corta dentro del prompt 4:

> *"mínima en el sentido de no añadir nada que no pida el apartado, pero suficiente para cumplir todo lo que el apartado sí pide"*

Una sola línea, dentro del prompt, sin necesidad de reformular cada vez.

### Acción
Aplicar el matiz al prompt 4 cuando se actualice el documento `04_prompts_flujo_v5.md` definitivo (junto con M005).

---

## M007 · Protocolo de reinicio de sesión Gemini

**Detectada:** 2026-04-28, simulacro 3 BDA01 (intuición del usuario)
**Estado:** propuesta para investigar
**Prioridad:** baja, salvo que el usuario tenga problemas reales de límite de tokens

### Problema potencial
Si la conversación con Gemini se reinicia (límite de tokens, error de plataforma, sesión nueva al día siguiente), no hay un protocolo claro para reanudar el trabajo a mitad del cuaderno. El usuario podría pensar que necesita "conclusiones largas en el cuaderno" para que Gemini tenga más contexto al reiniciar, pero esa solución es ilusoria (Gemini lee igual el cuaderno largo o corto, lo que necesita es el .ipynb completo + documentos del sistema).

### Solución correcta
Crear un protocolo de reinicio breve:
1. Pasar el .ipynb actual al chat nuevo,
2. Cargar maestro + guía como en el inicio normal,
3. Recordar al agente dónde lo dejamos: último apartado completado, próximo apartado a empezar,
4. Continuar con el flujo normal desde el prompt 3.

### Lo que NO es solución
**Hacer las conclusiones del cuaderno más largas para "dar contexto al agente al reiniciar".** El precio es ensuciar el cuaderno entregable con tono académico, y el beneficio es ilusorio.

### A validar
- Si el usuario tiene problemas reales de límite de tokens en sesiones largas (apartados 1-12 + auditoría puede acumular bastante).
- Si conviene un prompt 0 específico para reanudar.

---

## M008 · Conclusiones siguen incumpliendo el espíritu de la guía de estilo 🔥 prioridad alta

**Detectada:** 2026-04-28, simulacro 3 BDA01, apartados 1-5
**Estado:** confirmada en 4 conclusiones de 5
**Prioridad:** alta

### Problema observado
El prompt 5 pide "primera persona, breve, ligada al resultado real, termina con dato concreto". El agente cumple **la letra** pero esquiva **el espíritu**:

Ejemplos reales de las conclusiones del simulacro 3:

| # | Inicio problemático | Final problemático |
|---|---|---|
| P2 | "Se ha garantizado la reproducibilidad y automatización..." | "...verificándose la lectura de..." |
| P3 | "El perfilado revela..." | "...lo cual será clave para las posteriores pruebas de hipótesis." |
| P4 | "Se ha garantizado la comparabilidad..." | "...evitando así que su magnitud original introduzca sesgos en futuros cálculos." |
| P5 | "Se ha garantizado la consistencia estructural..." | "...la coherencia de los rangos numéricos." |

Patrón doble:
1. **Inicio en pasiva refleja** ("Se ha...") en lugar de primera persona ("He..."). El agente interpreta "primera persona" como "primera persona impersonal del plural".
2. **Final que anuncia pasos futuros** ("lo cual será clave para...", "evitando así... en futuros cálculos") aunque el prompt explícitamente dice "no añadas estrategia".

### Causa raíz
El prompt 5 pide reglas pero no da ejemplos negativos suficientes. El agente cumple las reglas técnicas (breve, dato concreto al final) pero adopta un registro académico por defecto.

### Solución propuesta (no aplicar todavía)
Reforzar el prompt 5 con ejemplos negativos explícitos:

```
No empieces con "Se ha...", "Hemos garantizado...", "Se han definido...".
Empieza directamente con "He [verbo]..." o con la observación concreta.

No termines anunciando lo que se hará después.
Frases prohibidas al final:
- "lo cual será clave para..."
- "permitiendo así..."
- "evitando así que..."
- "asegurando que..." / "garantizando la..."
```

### Acciones pendientes al cerrar el simulacro
1. Aplicar el prompt 13 (depuración final) sobre las conclusiones del cuaderno actual para verificar que el problema se puede arreglar a posteriori.
2. Si el prompt 13 funciona bien: aceptar que el prompt 5 puede quedar imperfecto y depurar al final.
3. Si el prompt 13 no es suficiente: reforzar el prompt 5 con los ejemplos negativos.

---

## M009 · Apartado 5 (Validación) limpia columnas que ya se usaron en apartado 4

**Detectada:** 2026-04-28, simulacro 3 BDA01
**Estado:** observación técnica menor — CERRADA tras simulacro completo
**Prioridad:** baja

### Problema observado
En el simulacro, el agente:
- En apartado 4, usó `col_rating = ' Rating'` (con espacio inicial).
- En apartado 5, limpió los espacios de los nombres de columnas (`df_ratings.columns.str.strip()`).

Orden lógico ETL ideal: validar/limpiar antes de transformar. El agente siguió el orden del enunciado del profesor (que pone Normalización antes que Validación), no el orden técnico ideal.

### Resolución
El agente se autorrecuperó sin problema en el apartado 6 (usó `Title` sin espacio, ya limpio). No hay impacto técnico.

### Acción
Cerrada. Solo registrar como aprendizaje: el agente sabe gestionar nombres de columnas entre apartados.

---

## M010 · Análisis estadístico al nivel de granularidad inadecuado 🔥 prioridad alta

**Detectada:** 2026-04-28, auditoría completa simulacro 3 BDA01
**Estado:** confirmada como gap real del CONTEXTO_UD1
**Prioridad:** alta

### Problema observado
En la comparación con la tarea aprobada de Maitane, los apartados donde el sistema produjo resultados inferiores tienen un patrón común: aplicaron el análisis al dataset principal grande (32.519 líneas de diálogo) cuando la pregunta era a nivel de episodio (25-34 episodios).

**Casos concretos:**
- **P10 (hipótesis):** t-test sobre 32.519 líneas vs 32.519 líneas dio p≈0 (rechazo trivial por inflación de tamaño muestral). Maitane hizo ANOVA sobre 8-9 ratings por temporada, p=0.6221, no rechaza. Las dos son técnicamente válidas pero la segunda responde a la pregunta real.
- **P11 (anomalías):** IsolationForest sobre 32.519 líneas detectó 519 "anomalías" que en realidad son líneas pertenecientes a episodios con rating bajo (información ya conocida). Maitane lo aplicó a nivel de episodio y detectó capítulos atípicos identificables.
- **P3 (perfilado), P4 (normalización), P8 (PCA):** mismo patrón en menor grado.

### Causa raíz
El CONTEXTO_UD1 fija que el dataset principal es `stranger_things_all_dialogue.csv`, pero NO especifica que **algunos apartados estadísticos (hipótesis, anomalías) deben trabajar al nivel de granularidad de la pregunta**, no del dataset principal.

El agente, fiel al contexto, aplica todo al dataset principal. Técnicamente correcto pero estadísticamente trivial.

### Solución propuesta
Añadir al CONTEXTO_UD1 (sección 4 · Reglas semánticas críticas) una nota en los apartados sensibles:

> **Apartados 8, 10, 11:** la unidad de análisis debe ser el episodio (no la línea de diálogo) cuando la pregunta es sobre características del episodio. Si vas a aplicar PCA, tests de hipótesis o detección de anomalías sobre ratings, usa el dataset agregado (`df_episode_summary` o equivalente con una fila por episodio), no el dataset principal de líneas.

Esto añade ~150 caracteres al CONTEXTO_UD1 y previene un fallo metodológico real.

### Acciones pendientes al cerrar el simulacro
1. Aplicar la nota al CONTEXTO_UD1 en sección 4 (apartados 8, 10, 11).
2. Verificar que en CONTEXTO_UD2/3/4/5 hay reglas equivalentes según corresponda.

---

## M013 · Actualizar PLAN_TAREA tras cada apartado añade ruido sin valor real

**Detectada:** 2026-04-28, simulacro 4 BDA01 (dataset turismo)
**Estado:** propuesta clara, aplicar al cerrar simulacro
**Prioridad:** media

### Problema observado
El sistema v7 establece que tras cada apartado el agente actualiza:
- la tabla "Estado de avance" de PLAN_TAREA (marca apartado como ✅),
- la sección "Decisiones tomadas" (añade línea si hay decisión relevante).

En el simulacro 4 esto añade un prompt completo al ciclo de cada apartado ("Actualiza el estado del apartado X a ✅ completado"). El usuario ha detectado que:
1. La información de "Estado de avance" es trivial: se ve en el cuaderno mirando qué celdas existen.
2. Las "Decisiones tomadas" sí tienen valor pero pueden vivir como comentarios en el código del apartado correspondiente.
3. El protocolo de reanudación funciona igual sin esa actualización: si reinicias, el agente lee el cuaderno y ve qué apartados tienen celdas.

### Solución propuesta
- **PLAN_TAREA se llena al inicio** (con prompt 3) y queda **fijo** durante el resto de la sesión.
- **Solo se modifica** si durante la ejecución hay un cambio real respecto al plan inicial (ej: cambio de variable objetivo, cambio de granularidad, descubrimiento de problema técnico que altera la estrategia).
- **Las decisiones técnicas puntuales** (tipo de merge, columnas elegidas) viven como comentarios en el código del apartado correspondiente, no en PLAN_TAREA.
- El **prompt 7 se simplifica** eliminando la actualización rutinaria de PLAN_TAREA.

### Beneficios
- El ciclo por apartado pasa de 4 prompts (analizar → autorizar → conclusión → actualizar) a 3 (analizar → autorizar → conclusión).
- PLAN_TAREA conserva su valor real (mapa apartado-unidad, datos identificados, granularidad), que es lo que protege contra reinterpretaciones.
- Menos ruido visual en el cuaderno.

### Riesgos
- Si en algún momento el examen es muy largo y necesitas recordar qué llevas hecho, no tendrás un resumen visual centralizado. Mitigación: en la auditoría final (prompt 8) se hace ese resumen si hace falta.

### Acciones pendientes al cerrar el simulacro
1. Reformular el prompt 7 eliminando la línea sobre actualizar Estado de avance.
2. Reformular la regla 4 del Gem v5.1 (quitar "actualiza Estado de avance de PLAN_TAREA").
3. Actualizar la nota de reglas de uso en la plantilla PLAN_TAREA v1.
4. Aclarar que PLAN_TAREA es **fijo tras el inicio**, salvo cambio real de plan.

---

## M011 · Conclusiones más detalladas en P3, P4, P8 (mejor visualización)

**Detectada:** 2026-04-28, comparación con Maitane
**Estado:** observación de calidad
**Prioridad:** media

### Problema observado
Apartados donde Maitane es claramente más rica:
- **P3 (perfilado):** Maitane añadió heatmap de correlación + boxplot de duración. Tu sesión solo histograma de Rating.
- **P4 (normalización):** Maitane escaló 5 columnas (`season`, `episode`, `line`, `start_time_minutes`, `end_time_minutes`). Tu sesión solo `Rating`.
- **P8 (PCA):** Maitane scatter coloreado por Rating (variable objetivo visible). Tu sesión scatter monocromático.

Estas omisiones no se deben a fallo del agente: las pidió el sistema. El prompt 4 dice "celda de código mínima" + "comprobación visible". El agente cumple lo mínimo.

### Tensión real
- **A favor de mantener mínimo:** evita sobreproducción.
- **En contra:** algunos apartados (perfilado, PCA) puntúan mejor con análisis más rico.

### Solución propuesta
NO modificar el prompt 4 genérico (riesgo de abrir sobreproducción).

SÍ añadir nota al CONTEXTO_UD1 en los apartados específicos donde el "mínimo" se queda corto:

> **Apartado 3 (Perfilado):** además de head/info/nulls, incluir al menos UNA visualización adicional (heatmap correlación, boxplot, histograma) que aporte valor exploratorio.
> **Apartado 4 (Normalización):** si hay varias variables numéricas continuas, escalar todas las relevantes (no solo la variable objetivo).
> **Apartado 8 (PCA):** scatter plot coloreado por la variable objetivo cuando exista.

### Acciones pendientes al cerrar el simulacro
1. Añadir las notas específicas al CONTEXTO_UD1.
2. Considerar para CONTEXTO_UD2-5 cuando se construyan.

---

## M012 · Exploración duplica trabajo cuando ya hay DataFrames cargados

**Detectada:** 2026-04-28, simulacro 4 BDA01 (dataset turismo)
**Estado:** observación menor del prompt 2
**Prioridad:** baja

### Problema observado
En el simulacro 4, el dataset se cargó previamente desde Kaggle (`df_employed`, `df_tourist`). Aún así, el agente ejecutó el prompt 2 explorando los 36 CSVs del directorio descargado, sin reconocer que dos de ellos ya estaban cargados como DataFrames.

El agente se reconduce solo (al rellenar PLAN_TAREA reconoce los DataFrames ya cargados), pero gasta un turno en exploración innecesaria.

### Causa
El prompt 2 dice "carga cada dataset disponible" sin mencionar comprobar el espacio de variables previo.

### Solución propuesta
Añadir al prompt 2 una línea:

> "Antes de investigar todos los archivos del directorio, comprueba si ya hay DataFrames cargados en el espacio de variables (revisa qué `df_*` existen con `dir()` o similar). Si los hay, lístalos y completa la exploración solo con los archivos relevantes restantes."

### A validar
Si en simulacros futuros vuelve a aparecer el patrón, aplicar. Si no vuelve a aparecer, descartar (puede ser caso aislado del dataset turismo con 36 CSVs, no se repetirá con datasets normales de 2-3 archivos).

---

## M002 · Mejor arquitectura: dataset a mano + ruta en el enunciado

**Detectada:** 2026-04-28, simulacro 2 BDA01
**Estado:** validar en próximo simulacro
**Prioridad:** media

### Problema observado / decisión arquitectónica
En el simulacro 1 el dataset venía de un repo clonado y el agente tenía que descubrir qué archivos eran relevantes (5 CSVs entre los que había que elegir 3). Esto creaba ambigüedad y obligaba a resolución autónoma del agente.

### Mejora aplicada (a confirmar tras simulacro 2)
- Dataset subido a mano por el usuario.
- Ruta y archivos esperados listados explícitamente al final del enunciado del cuaderno.
- El agente recibe la información como parte del enunciado, no como descubrimiento.

### Beneficios esperados
- Replicabilidad con el día del examen.
- Reduce ambigüedad sobre qué archivos usar.
- El usuario controla qué ve el agente en lugar de que el agente lo decida.

### A validar
- Si en simulacro 2 el agente sigue mirando los 5 CSVs o se ciñe a los del enunciado.
- Si la ruta en el enunciado es suficiente o conviene un formato más explícito.

---

## M003 · Riesgo de plantilla pre-cargada con contenido textual completo

**Detectada:** 2026-04-28, simulacro 2 BDA01
**Estado:** falsa alarma técnica + aprendizaje rescatable
**Prioridad:** baja

### Aclaración (no es problema en este simulacro)
La celda 8 ("### Conclusión del Punto: Carga de Datos") la creó el usuario como parte de su plantilla del examen, no el agente. El comportamiento del agente fue correcto.

### Aprendizaje rescatable
Aún así, hay un riesgo arquitectónico real que vale la pena dejar registrado: las celdas pre-cargadas con contenido textual completo (no solo titulares) pueden inducir al agente a:
- interpretarlas como apartado ya cerrado y saltárselo,
- usarlas como modelo para escribir conclusiones similares en otros apartados (replicando estilo académico si lo hubiera),
- o intentar reescribirlas si las ve incompletas.

### Recomendación
- En la plantilla del examen, dejar pre-cargadas celdas de **estructura** (titulares H1/H2, celdas de carga estándar), no de **contenido** completo (conclusiones redactadas, explicaciones largas).
- Si hay celdas con contenido completo necesarias para el examen, marcarlas explícitamente al agente al inicio de la sesión: "estas celdas son plantilla mía, ignórralas hasta que toque el apartado correspondiente".

### Estado
Cerrado por ahora. Si en simulacros futuros se observa que el agente sí toma las celdas pre-cargadas como modelo, reabrir.
