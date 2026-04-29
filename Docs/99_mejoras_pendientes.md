# Mejoras pendientes del sistema BDA

> **Para qué sirve este documento:** registrar mejoras detectadas durante simulacros reales para revisarlas con cabeza fría al terminar la sesión, no al calor del momento.
>
> **Filtro obligatorio antes de aplicar cualquier mejora:** ¿añade complejidad? Si sí, ¿el beneficio justifica el coste? La investigación es clara: superar ~7 reglas o subdividir el flujo aumenta el riesgo de modo automático. Cuando dudes, descarta.

> **Estados:** propuesta · validar en próximo simulacro · ✅ aplicada · descartada · cerrada
> **Prioridades:** alta · media · baja

## Resumen de estado actual (2026-04-29)

**Aplicadas (16):** M001, M002, M004, M005, M006, M008, M010, M011, M012, M013, M014, M015, M017, M018, M019, M020.

**Cerradas / reemplazadas (3):** M003 (falsa alarma + aprendizaje rescatable), M009 (observación técnica menor), M016 (reemplazada por M017).

**Reservadas (1):**
- **M007** — Protocolo de reinicio de sesión Gemini. Solo aplicar si aparecen problemas reales de tokens.

**Pendientes que requieran acción técnica: 0.**

**Versiones de documentos vigentes (sistema v9):**
- `01_instrucciones_gem_v5_2.md` (M018: regla 4 corregida)
- `02_documento_maestro_v4.md` (sin cambios)
- `03_guia_estilo_v4.md` (sin cambios)
- `04_prompts_flujo_v9.md` (M017: plantillas inyectadas por chat, prompt 1 verifica CONTEXTO_UD#)
- `05_plantilla_contexto_unidad_v3.md` (sin cambios)
- `06_CONTEXTO_UD1_v3.md` (sin cambios)
- `06_CONTEXTO_UD2_v5.md` (M017: sección 0 reformulada para plantillas externas)
- **`06_CONTEXTO_UD3_v7.md`** ⬅️ ACTUALIZADO (M020: 4 plantillas operativas)
- `07_PLAN_TAREA_plantilla_v2.md` (sin cambios)

**Plantillas operativas (archivos `.md` independientes, no se cargan en el cuaderno):**
- `plantilla_01_instalacion_hadoop.md` (UD2)
- `plantilla_02_hdfs.md` (UD2)
- `plantilla_03_java_wordcount.md` (UD2)
- `plantilla_04_streaming.md` (UD2)
- `plantilla_05_instalacion_pig.md` (UD3, M019)
- **`plantilla_06_pig_correccion.md`** ⬅️ NUEVO (UD3, M020)
- **`plantilla_07_pig_top_palabras.md`** ⬅️ NUEVO (UD3, M020)
- **`plantilla_08_spark_hive_consultas.md`** ⬅️ NUEVO (UD3, M020)

**Cambio arquitectónico (M017):** las plantillas de UD2 y UD3 NO van pre-cargadas en el cuaderno. Se inyectan por chat solo cuando el agente las pide al llegar al apartado correspondiente. Esto reduce el ruido del cuaderno y permite escalar a UD3 sin saturar contexto.


## M001 · Falta paso de análisis previo del dataset entre exploración y primer apartado ✅ CONFIRMADA

**Detectada:** 2026-04-28, simulacros 1 y 2 BDA01 con sistema v5
**Estado:** ✅ aplicada en flujo v7+ (prompt 3 hace este trabajo)
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
**Estado:** ✅ aplicada (verificada al revisar plantilla v3, la sección ya estaba eliminada)
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
**Estado:** ✅ aplicada en flujo v7+ (prompt 5: "omítela si el apartado es puramente narrativo")
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
**Estado:** ✅ aplicada en flujo v7+
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
**Estado:** 🔒 reservada — solo aplicar si aparecen problemas reales de límite de tokens. PLAN_TAREA v2 + cuaderno actuan ya como protocolo de reanudación implícito (ver flujo v9 "Reanudar una sesión interrumpida")
**Prioridad:** baja

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
**Estado:** ✅ aplicada en flujo v8+ (prompt 6 reforzado)
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
**Estado:** ✅ aplicada en CONTEXTO_UD1 v3
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
**Estado:** ✅ aplicada en flujo v8+ (PLAN_TAREA fijo tras inicio)
**Prioridad:** media

### Problema observado
El sistema v7 establecía que tras cada apartado el agente actualiza:
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
**Estado:** ✅ aplicada en CONTEXTO_UD1 v3
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
**Estado:** ✅ aplicada en flujo v8+ (prompt 2 ampliado)
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
**Estado:** ✅ aplicada de facto vía PLAN_TAREA v2 (campo "Datos identificados" + "Archivos a subir a HDFS" listan dataset y rutas explícitamente desde el inicio; validado en simulacro UD1+UD2 de 2026-04-29)
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
---

## M014 · Subida a HDFS: estabilizar ruta vía /content/ y nombre fijo de carpeta

**Detectada:** 2026-04-28, preparación UD2
**Estado:** ✅ aplicada al CONTEXTO_UD2 v4/v5
**Prioridad:** alta

### Problema observado
Dos fallos reales en la subida de archivos a HDFS:
1. `hdfs dfs -put` directo desde la ruta de `kagglehub` rompe entre ejecuciones (caching cambia la ruta).
2. Carpetas HDFS con nombres variables (`datasets`, `data`, etc.).

### Solución aplicada
Patrón fijo de 4 pasos: `shutil.copy` a `/content/<nombre_corto>.csv` → `mkdir -p dataset` → `put -f` → `ls`. Carpeta HDFS siempre `dataset` (singular). Aplicado en CONTEXTO_UD2 v4/v5 y `plantilla_02_hdfs.md`.

---

## M015 · PLAN_TAREA debe listar todos los archivos que entran al pipeline Hadoop

**Detectada:** 2026-04-28, simulacro UD2
**Estado:** ✅ aplicada a PLAN_TAREA v2
**Prioridad:** alta

### Problema observado
El agente solo subía a HDFS los archivos en PLAN_TAREA. Si el examen requería procesar varios archivos juntos, el pipeline quedaba incompleto.

### Solución aplicada
Plantilla PLAN_TAREA v2: campo "Archivos a subir a HDFS (UD2/UD3)" con tabla de origen → nombre corto.

---

## M016 · Inventario de plantillas pre-cargadas en CONTEXTO_UD2 (REEMPLAZADA por M017)

**Detectada:** 2026-04-28, simulacro UD2 (apartado Java)
**Estado:** ⚠️ reemplazada por M017
**Prioridad:** alta

### Por qué se reemplazó
Tras el simulacro UD2 final, pre-cargar las plantillas en el cuaderno saturaba el contexto del agente y provocaba modo automático. Se cambió a M017 (plantillas inyectadas por chat al llegar al apartado).

---

## M017 · Plantillas operativas inyectadas por chat (no pre-cargadas)

**Detectada:** 2026-04-29, auditoría del cuaderno UD01_02_Turismo_New_agente__3
**Estado:** ✅ aplicada (CONTEXTO_UD2 v5, CONTEXTO_UD3 v6, flujo v9, plantillas .md independientes)
**Prioridad:** alta

### Problema observado
Cuaderno con plantillas pre-cargadas llegaba a 2045 líneas. El agente entró en modo automático en UD2 (texto en inglés tipo "Analyze context", duplicación de plantillas Java/Streaming, propuestas autónomas). Las plantillas se escribían dos veces. Cargar plantillas que no se usan en la unidad actual ocupaba ventana de contexto sin aportar nada.

### Solución aplicada
**Cambio arquitectónico:** las plantillas de UD2/UD3 ya NO van pre-cargadas en el cuaderno. Viven como archivos `.md` independientes y se inyectan por chat cuando el agente las pide al llegar al apartado correspondiente.

1. **CONTEXTO_UD2 v5 / CONTEXTO_UD3 v6:** sección 0 nueva con tabla de inventario de plantillas externas.
2. **Flujo v9:** prompts 4 y 7 amplían — si el apartado UD2/UD3 requiere plantilla y el agente no la tiene, la pide al usuario antes de proponer el bloque.
3. **5 plantillas .md creadas:** instalación Hadoop, HDFS, Java, Streaming (UD2) + instalación Pig (UD3).

---

## M018 · Regla 4 del Gem mencionaba PLAN_TAREA actualizable tras cada apartado

**Detectada:** 2026-04-29, auditoría del cuaderno UD01_02_Turismo_New_agente__3
**Estado:** ✅ aplicada (instrucciones del Gem v5.2)
**Prioridad:** alta

### Problema observado
Aunque M013 estableció en flujo v8 que PLAN_TAREA queda fijo, la regla 4 de las instrucciones del Gem v5.1 seguía diciendo "actualiza el Estado de avance de PLAN_TAREA". Instrucciones contradictorias.

### Solución aplicada
Instrucciones del Gem v5.2: regla 4 reescrita ("PLAN_TAREA queda fijo, solo se modifica si cambia el plan"). Regla 5 ampliada con modificación de zonas `NORMALMENTE NO CAMBIAR` de plantillas.

---

## M019 · CONTEXTO_UD3 alineado al sistema v9 (10 secciones canónicas + plantilla externa)

**Detectada:** 2026-04-29, auditoría del CONTEXTO_UD3 v5 contra el sistema v9
**Estado:** ✅ aplicada (CONTEXTO_UD3 v6, plantilla_05_instalacion_pig.md)
**Prioridad:** alta

### Problema observado
El CONTEXTO_UD3 v5 (1284 líneas) tenía contenido técnico excelente (mapeo Pandas→Pig, estrategia conservadora con columnas sucias, bug LIMIT en Pig 0.17, patrón TOP N con STORE+head, anti-sintaxis inventada, limpieza metastore Derby, validación pre-HQL) pero su estructura de **28 secciones** no se alineaba con el formato canónico de los CONTEXTO_UD1 v3 y CONTEXTO_UD2 v5 (10 secciones).

Consecuencias:
- Agente no podía citar reglas de forma uniforme entre las tres unidades.
- En sesiones multi-unidad (UD1+UD2+UD3) las tres referencias usaban convenciones distintas.
- Faltaba la sección 0 con plantillas operativas externas (paso obligatorio del sistema v9).
- Faltaba PLAN_TAREA en la jerarquía.
- La instalación de Pig estaba pegada en el cuaderno como celda fija — debía ser plantilla externa siguiendo M017.

### Solución aplicada
**Reestructuración a las 10 secciones canónicas (0-9):**
- **Sección 0 nueva:** inventario con `plantilla_05_instalacion_pig.md`. Spark setup y limpieza Derby quedan como bloques operativos en sección 7 (no son plantillas externas a la misma escala).
- **Sección 1:** "Qué se evalúa" en formato canónico, con los 7 puntos del enunciado oficial.
- **Sección 2:** jerarquía actualizada con PLAN_TAREA y CONTEXTO_UD1/UD2 cuando aplica.
- **Sección 3:** mapa conceptual de apartados típicos (tabla 7×3).
- **Sección 4:** reglas semánticas críticas por apartado (todo el contenido técnico de v5 reagrupado por apartado del enunciado, no por número de regla).
- **Sección 5:** decisiones que NO puede tomar solo, agrupadas por fase.
- **Sección 6:** pipeline obligatorio en tabla con cruce a plantillas/bloques.
- **Sección 7:** bloques operativos con placeholders (todos los patrones técnicos de v5 listados con numeración 7.1 a 7.13).
- **Sección 8:** señales de éxito.
- **Sección 9:** señales de error.

**Plantilla externa creada:** `plantilla_05_instalacion_pig.md` con descarga + variables de entorno + verificación, basada en la celda de instalación que tenía el usuario en `Plantilla_examen_UD01_02_UD3_New_agente.ipynb`.

**Convención de carpetas aclarada en sección 4:** `input/` (Pig local en CWD de Colab) vs `dataset/` (HDFS de UD2). No hay conflicto: son contextos distintos.

### Contenido técnico del v5 conservado íntegro
- Mapeo Pandas→Pig obligatorio (§7 v5 → bloque 7.2 v6).
- Estrategia conservadora con columnas sucias (§8 v5 → sección 4 / Pig Corrección v6).
- Excepción controlada con clase negada (§8.4 bis v5 → sección 4 v6).
- Modo Pig sin `-x local` con rutas relativas (§10 v5 → sección 4 / Convención común v6).
- Compatibilidad con Pig 0.17 (§11 v5 → sección 4 / Sintaxis prohibida v6).
- Anti-sintaxis Pig inventada (§12 v5 → sección 4 v6).
- Patrón TOP N con STORE intermedio + !head (§13 v5 → sección 4 / Transformación + bloque 7.5 v6).
- Anti-reintento (§15 v5 → sección 6 / Regla anti-reintento v6).
- Anti-DUMP masivo (§16 v5 → sección 5 v6).
- Instalación Spark con `wget .tgz + findspark`, prohibido `pip install pyspark` (§20.3.0 v5 → sección 4 / Spark + bloque 7.7 v6).
- Limpieza metastore Derby (§20.4.0 v5 → bloque 7.8 v6).
- `CREATE DATABASE` + `USE` + validación pre-HQL (§20.4.1, §20.4.2 v5 → sección 4 / Spark + bloques 7.9, 7.10 v6).

### Validación cruzada con IJM03.ipynb (tarea de compañero)
Patrones reales del cuaderno IJM03 que se confirman en el contexto v6:
- Generación de esquema Pig con dict y `df.dtypes` ✅ (bloque 7.2).
- `CREATE DATABASE IF NOT EXISTS bd_<nombre>` → `saveAsTable` ✅ (bloque 7.9).
- `SHOW TABLES` + `DESCRIBE` + `SELECT LIMIT` ✅ (bloque 7.10).
- Spark 3.5.4-bin-hadoop3 + findspark ✅ (bloque 7.7).
No se detectaron patrones técnicos en IJM03 que faltasen en el v5.

### Acciones
1. Aplicada reestructuración (CONTEXTO_UD3 v6 generado, plantilla_05 generada).
2. Validar en próximo simulacro UD3 que el agente:
   - pide la plantilla Pig por chat al llegar al apartado correspondiente,
   - cita reglas usando la nueva numeración de secciones,
   - aplica el mapeo Pandas→Pig antes del primer LOAD,
   - respeta la estrategia conservadora con columnas sucias,
   - usa `ORDER BY → STORE → !head` para TOP N,
   - limpia metastore Derby antes del primer `spark.sql` Hive.
3. Si el simulacro UD3 detecta gaps, abrir M020 con observaciones.

---

## M020 · UD3: tres plantillas operativas adicionales (corrección Pig, top palabras Pig, Spark+Hive+HQL)

**Detectada:** 2026-04-29, tras revisar UD03_PIG02.ipynb (segundo intento del usuario, funcional) y plantilla unificada antigua del usuario
**Estado:** ✅ aplicada (3 plantillas .md creadas + CONTEXTO_UD3 v7 con sección 0 ampliada)
**Prioridad:** alta

### Problema observado
Tras crear `plantilla_05_instalacion_pig.md` (M019), seguía habiendo riesgo real de que el agente improvisara scripts Pig y código Spark+Hive con patrones rotos. El usuario aportó:

1. **Cuaderno funcional UD03_PIG02.ipynb** con scripts Pig que SÍ funcionan (limpieza con clase negada, top palabras con ORDER → STORE → !head, Spark+Hive con limpieza Derby + CREATE DATABASE + validación pre-HQL).
2. **Plantilla unificada antigua del usuario** (`UD3_Plantillas_Pig_Unificadas.md`, v1) con buena estructura pero **3 patrones rotos** según el CONTEXTO_UD3 v6:
   - Plantilla 4 (limpieza importes): `REGEX_EXTRACT(importe, '([0-9.]+)', 1)` — patrón no anclado, devuelve null silencioso sobre cadenas con prefijos.
   - Plantilla 5 (top ciudades): `LIMIT` directo tras `ORDER BY` — bug Pig 0.17 ERROR 2244.
   - Plantilla 3 (fechas): regex con `\d` problemática en escape entre %%writefile y Pig.
3. **Conflicto de modo Pig:** la plantilla v1 ofrecía elegir entre local y HDFS. El CONTEXTO_UD3 v6 fija un modo único (`!pig` sin `-x local`, rutas relativas). Dos fuentes contradictorias = agente confundido.

### Decisión arquitectónica
**Crear 3 plantillas operativas externas adicionales para UD3** siguiendo el patrón M017 (mismo formato que las 4 de UD2 + la 05 de Pig install). Cada plantilla:
- está blindada con las reglas duras del CONTEXTO_UD3 v6 dentro de su contenido,
- tiene placeholders `[ADAPTAR_*]` claros,
- tiene reglas explícitas "qué cambiar / qué NO cambiar",
- usa los patrones verificados del cuaderno UD03_PIG02 (no los rotos de la plantilla v1).

### Solución aplicada
Tres plantillas nuevas:

1. **`plantilla_06_pig_correccion.md`** (apartado 3.1):
   - Esquema chararray + filtro cabecera (`FILTER ... BY id != 'id'`).
   - Limpieza de importes con clase negada `(double) REPLACE(col, '[^0-9.-]', '')` (único patrón seguro en Pig 0.17).
   - Imputación de nulos con `IS NULL` y valores por defecto.
   - STORE en `salida_limpios/`. Verificación con `!head`.

2. **`plantilla_07_pig_top_palabras.md`** (apartado 3.2):
   - LOAD de uno o dos CSVs con esquemas reales del mapeo.
   - TOKENIZE + LOWER + CONCAT para varias columnas de texto.
   - UNION para juntar palabras de varios orígenes.
   - Stopwords inline encadenadas con `!=` (Pig 0.17 no tiene IN).
   - ORDER BY DESC + STORE en `salida_palabras_ordenado/`.
   - TOP N con `!head -n <N>` secuencial (no LIMIT directo).

3. **`plantilla_08_spark_hive_consultas.md`** (apartados 4 y 5):
   - Instalación Spark con `wget .tgz + findspark` (no `pip install pyspark`).
   - SparkSession con `enableHiveSupport()`.
   - **Limpieza obligatoria de metastore Derby** antes del primer `spark.sql` Hive.
   - `CREATE DATABASE IF NOT EXISTS <base>` + `USE <base>` antes de `saveAsTable`.
   - Diferenciación CSV originales (con cabecera, inferSchema) vs salida Pig (sin cabecera, schema explícito).
   - Validación obligatoria intermedia: DESCRIBE + COUNT(*) + SELECT * LIMIT 3 por cada tabla.
   - 2 consultas HQL con JOIN entre 2 tablas.

**CONTEXTO_UD3 v7:** sección 0 ampliada para listar las 4 plantillas (instalación, corrección, top palabras, Spark+Hive). Pipeline obligatorio (sección 6) actualizado para mapear apartados → plantillas.

### Validación cruzada con plantilla unificada antigua del usuario
Reglas que se rescatan de la plantilla v1 y se mantienen en las nuevas:
- Estructura "qué cambiar / qué NO cambiar" en cada plantilla.
- Verificación obligatoria de salida con `!head` o equivalente tras cada STORE.
- Errores frecuentes documentados (mezclar local/HDFS, salida ya existe, schema mal).

Patrones de la v1 que **NO se rescatan** (porque están rotos):
- Modo `pig -x local` → contradice CONTEXTO_UD3 v6.
- `REGEX_EXTRACT('([0-9.]+)', 1)` para importes → null silencioso.
- `LIMIT` directo tras `ORDER BY` → bug Pig 0.17.
- Regex con `\d` para fechas → problemas de escape.

### Por qué este enfoque y no actualizar la plantilla unificada antigua
- La plantilla v1 mezclaba modos Pig que el sistema v9 ya unificó.
- Tener un solo archivo grande con 6 plantillas obliga al agente a leer las 6 cada vez que necesita una. Tener archivos independientes (06, 07, 08) permite inyectar solo lo necesario.
- La estructura del archivo `.md` por plantilla sigue exactamente el patrón de UD2 (M017) → coherencia del sistema.

### Acciones
1. Aplicadas las 3 plantillas nuevas (hecho).
2. Aplicado CONTEXTO_UD3 v7 con sección 0 ampliada (hecho).
3. Validar en simulacro UD3 real que el agente:
   - pide cada plantilla por chat al llegar al apartado correspondiente,
   - no improvisa variantes rotas (limpieza con `$` literal, LIMIT tras ORDER, etc.),
   - sigue el orden 05 → 06 → 07 → 08 sin saltarse pasos.
4. Si el simulacro UD3 detecta un patrón nuevo no cubierto, abrir M021 con observaciones.