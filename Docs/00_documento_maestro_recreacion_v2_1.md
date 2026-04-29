# Documento maestro · Cómo recrear el sistema de control de Gemini en Colab

> Versión: 2.1
> Última actualización: 2026-04-28
> Estado: vigente
> Audiencia: este documento es para uso del usuario y de la IA auditora del sistema. **No se carga en Gemini ni se le entrega como contexto de trabajo.**
> Cambios respecto a v2.0: integración del prompt 3 (análisis previo del dataset) al flujo principal (M001), refinamientos al prompt 5 (M005, M006), refinamiento al prompt 6 (M008), notas de granularidad y riqueza en CONTEXTO_UD# (M010, M011), eliminación del prompt redundante en plantilla y CONTEXTO_UD# (M004).

---

## ÍNDICE

1. [Propósito del documento](#1-propósito-del-documento)
2. [Para qué sirve el sistema](#2-para-qué-sirve-el-sistema)
3. [Las 6 piezas del sistema](#3-las-6-piezas-del-sistema)
4. [Diagnóstico que justifica el diseño actual](#4-diagnóstico-que-justifica-el-diseño-actual)
5. [Comportamiento exacto que queremos del agente](#5-comportamiento-exacto-que-queremos-del-agente)
6. [Cómo se carga cada pieza en Gemini](#6-cómo-se-carga-cada-pieza-en-gemini)
7. [Prompts para regenerar cada pieza](#7-prompts-para-regenerar-cada-pieza)
8. [Recomendación de modelo](#8-recomendación-de-modelo)
9. [Cierre y principio diagnóstico](#9-cierre-y-principio-diagnóstico)

---

## 1. Propósito del documento

Este documento es un **puente de reconstrucción**: si en el futuro se cambia de cuenta, de IA auditora o se pierden los archivos del sistema, otra IA puede leer este documento y regenerar las 6 piezas que componen el sistema de control de Gemini en Colab.

**No es un contexto operativo.** Gemini no lo necesita. Quien lo necesita es el usuario y la IA auditora.

El sistema completo, además de las 6 piezas que aquí se describen, incluye:
- tareas resueltas reales como referencia,
- enunciados oficiales,
- teoría y cuadernos del profesor.

Estos elementos no se generan con este documento, pero deben existir para que el sistema funcione en examen.

---

## 2. Para qué sirve el sistema

El sistema controla a Gemini dentro de un cuaderno de Google Colab para resolver tareas y exámenes prácticos del módulo BDA (ETL, Hadoop, Spark, MongoDB, Neo4j).

El objetivo NO es que Gemini improvise soluciones completas. El objetivo es que Gemini trabaje como **asistente técnico disciplinado** que:

- analiza primero el enunciado en chat,
- consulta el contexto de la unidad antes de proponer código técnico,
- crea solo bloques mínimos en el cuaderno (una markdown + una code + una conclusión por apartado),
- evita planes largos, celdas innecesarias y texto de gestión,
- adapta el código al dataset real,
- consulta antes de tomar decisiones delicadas,
- y trabaja paso a paso, sin entrar en "modo automático" de resolución autónoma.

---

## 3. Las 6 piezas del sistema

| # | Pieza | Tipo | Dónde vive | Propósito |
|---|---|---|---|---|
| 1 | **Instrucciones del Gem** (v5) | Control general | Campo de instrucciones del Gem (≤4500 chars) | Identidad y 7 reglas operativas siempre activas |
| 2 | **Documento maestro único** (v4) | Control general | Cargado por chat al inicio de cada sesión | Razonamiento técnico, error, tamaño, trazabilidad, decisiones |
| 3 | **Guía de estilo del cuaderno** (v4) | Estilo | Cargado por chat al inicio de cada sesión | Cómo debe verse el notebook final |
| 4 | **Prompts del flujo** (v6) | Plantillas operativas | Chuleta del usuario (no se carga) | 9 prompts en orden de uso |
| 5 | **Plantilla genérica de contexto de unidad** (v2) | Plantilla | Para crear UD2-UD5 a partir de ella | Esqueleto común con `[ADAPTAR]` |
| 6 | **Contexto de unidad concreto (UD1, UD2, ...)** (v2 para UD1) | Contexto técnico | Celda markdown del cuaderno | Reglas semánticas y técnicas de la unidad |

### Pieza 1 vs pieza 2: por qué hay dos documentos de control general

La pieza 1 cabe en el campo del Gem (≤4500 caracteres) y contiene solo lo crítico siempre activo: identidad, 7 reglas, prohibiciones absolutas, protocolo de los 4 pasos, regla final de seguridad.

La pieza 2 amplía la 1 con lo que no cabe en el Gem: jerarquía completa, regla de error, regla de tamaño con ejemplos, regla de trazabilidad, regla de adaptación al dataset, listado completo de decisiones a consultar.

**No hay duplicación.** Cada cosa vive en una sola pieza. Si lo crítico siempre activo cambia, se actualiza en la pieza 1; si cambia el desarrollo, se actualiza en la pieza 2.

### La decisión arquitectónica clave: pieza 6 en celda del cuaderno

En versiones anteriores del sistema, el contexto de unidad se cargaba por chat junto con el maestro y la guía de estilo. Eso causaba un problema documentado: el contexto se "diluía" tras varios turnos de conversación (fenómeno *lost in the middle* en LLMs con ventana larga). Gemini lo "tenía" en su contexto pero no lo aplicaba al razonar — un caso real fue resolver el apartado 4 de UD1 con limpieza textual cuando el contexto decía claramente "escalado numérico".

La solución: el contexto de unidad vive en una **celda markdown del cuaderno** marcada como `CONTEXTO_UD#`. Como el cuaderno es lo más cercano a la conversación activa, esta celda permanece "visible" para Gemini en cada turno. Además, la pieza 1 (instrucciones del Gem) le obliga a **citar la regla del CONTEXTO_UD#** antes de proponer código técnico — esto fuerza la consulta activa, no solo la presencia pasiva.

### El prompt 3 del flujo: análisis previo y rellenado de la sección 9

El sistema v6 incorpora un paso obligatorio entre la exploración estructural (prompt 2) y el primer apartado (prompt 4): el análisis previo del dataset (prompt 3). Este prompt fuerza al agente a convertir la salida cruda de la exploración en reglas estructuradas que rellenan la sección 9 del CONTEXTO_UD#.

**Por qué este paso es importante:** sin él, la sección 9 queda en `[ADAPTAR]` y el agente improvisa la interpretación de columnas (numéricas continuas vs identificadores) en cada apartado, lo que produce errores reales documentados (apartado 4 fallado, apartado 8 PCA trivial).

---

## 4. Diagnóstico que justifica el diseño actual

El sistema v5/v6 incorpora aprendizajes específicos de fallos reales observados durante simulacros:

### Problema 1 · Modo automático
Gemini tiende a entrar en "modo de resolución autónoma" tras los primeros turnos: empieza a crear celdas Task/Plan/Strategy/Roadmap, encadena varios apartados sin permiso y sigue por inercia. Esto es un patrón reconocible documentado en investigación sobre LLMs (completion mode vs instruction-following mode).

**Mitigación en el sistema:** prohibición explícita de tokens (Task, Plan, Strategy, Roadmap, Checklist, Summary, Next step) en la pieza 1 + estructura de prompts del flujo (pieza 4) que fuerza interrupción tras cada paso.

### Problema 2 · Reinterpretación silenciosa
Gemini puede "tener" un documento en contexto pero no consultarlo al razonar, defaultando a su conocimiento general. El caso documentado es UD1 apartado 4: "normalización" → tomó la interpretación más frecuente en datos de pretraining (limpieza textual: `.strip()`, `.lower()`) ignorando la interpretación específica del contexto cargado (escalado numérico con `StandardScaler`).

**Mitigación en el sistema:** regla 2 de la pieza 1 ("consulta y cita el contexto antes de proponer código técnico") + prompts 4 y 7 de la pieza 4 que obligan a citar literal la regla aplicable + arquitectura del contexto en celda del cuaderno (pieza 6) para evitar dilución.

### Problema 3 · Alucinación de archivos y columnas
Gemini puede afirmar nombres de archivos o columnas sin haberlos verificado contra el cuaderno. Caso documentado: en una sesión de UD1 afirmó dos sets de archivos distintos en mensajes consecutivos (`titles.csv/credits.csv` vs `df_dialogue/df_ratings/df_episodes`) sin admitir la contradicción.

**Mitigación en el sistema:** regla 7 de la pieza 1 ("verifica antes de afirmar"). Antes de afirmar nombres de archivos, columnas o variables, debe haberlos visto en una celda ejecutada.

### Problema 4 · Conclusiones académicas
Las conclusiones de Gemini tienden a empezar con voz pasiva ("Se ha garantizado...") y terminar con gerundios decorativos sin valor técnico: *"asegurando que la base es sólida..."*, *"garantizando la consistencia..."*, *"manteniendo la trazabilidad..."*. Esto contamina el cuaderno final.

**Mitigación en el sistema:** la pieza 3 (guía de estilo) incluye ejemplos negativos literales sacados de sesiones reales + el prompt 6 de la pieza 4 (en versión v6) prohíbe explícitamente esos inicios y finales con lista cerrada de frases prohibidas.

### Problema 5 · Sobrecarga de instrucciones
Sistemas previos con muchas reglas (>10) y estructura compleja (fases A/B, turnos T0/T1/T1B/T2/T3, citas obligatorias triplete LECTURA/EVIDENCIA/APLICACIÓN) causaban que Gemini entrara más fácilmente en modo automático: las múltiples reglas diluyen la atención de cada una. Investigación documenta degradación significativa con más de 7-10 reglas.

**Mitigación en el sistema:** la pieza 1 tiene exactamente **7 reglas operativas**, en el límite del threshold óptimo documentado.

### Problema 6 · Granularidad de análisis incorrecta
En el simulacro 3 se detectó que Gemini aplicaba apartados estadísticos (PCA, hipótesis, anomalías) al dataset principal de líneas de diálogo (32.519 filas) cuando la pregunta era sobre episodios (25-34). El resultado: PCA trivial, p-valores artificialmente bajos por inflación de tamaño muestral, anomalías de bloque que no aportan información nueva.

**Mitigación en el sistema:** notas explícitas de granularidad correcta en la pieza 6 (CONTEXTO_UD#) en los apartados 8, 10, 11.

### Problema 7 · Riqueza analítica insuficiente
En el mismo simulacro se detectó que Gemini cumplía "lo mínimo" en apartados que requieren más (perfilado sin visualización, normalización solo de la variable objetivo cuando había varias continuas, PCA sin coloreado por target).

**Mitigación en el sistema:** notas de "riqueza requerida" en la pieza 6 + aclaración del significado de "bloque mínimo" en el prompt 5 ("no añadir nada que no pida el apartado, pero suficiente para cumplir todo lo que el apartado sí pide").

---

## 5. Comportamiento exacto que queremos del agente

### Antes de tocar el cuaderno
- leer el enunciado completo,
- consultar el contexto de la unidad (celda CONTEXTO_UD# del cuaderno),
- responder **solo en chat** con: qué ha entendido, qué apartado toca, qué dice el contexto sobre ese apartado (cita literal), qué bloque toca, y la pregunta "¿empiezo por este apartado?".

En este momento **no debe crear ninguna celda**.

### Tras la confirmación del usuario
- explicar brevemente en chat qué va a hacer,
- crear **solo**:
  - una celda markdown con el texto literal del apartado y explicación simple,
  - una celda de código si el apartado lo requiere (omitible en apartados narrativos),
- esperar a que el usuario ejecute.

### Tras ejecutar
- leer la salida real,
- escribir una conclusión breve en celda del cuaderno (1-3 líneas, primera persona del singular ("He..."), ligada al resultado, terminada con un dato concreto),
- volver al chat.

### Antes del siguiente apartado
- releer enunciado, contexto y cuaderno,
- proponer solo el siguiente apartado lógico citando otra vez la regla del contexto.

### Lo que NO debe hacer
- crear celdas con títulos Task, Plan, Strategy, Roadmap, Checklist, Summary, Next step (en español o inglés),
- crear varias celdas markdown seguidas,
- llenar el cuaderno de texto,
- encadenar varios pasos importantes sin permiso,
- decidir por su cuenta cosas delicadas,
- afirmar nombres de archivos o columnas sin verificar,
- usar definiciones genéricas para reinterpretar apartados que el contexto ya define,
- empezar conclusiones con "Se ha...", "Hemos...", "El proceso de...",
- terminar conclusiones con gerundios decorativos ("asegurando...", "garantizando...", "permitiendo así...").

---

## 6. Cómo se carga cada pieza en Gemini

Orden de carga al iniciar una sesión:

1. **Una sola vez al crear el Gem:** se pega la pieza 1 (instrucciones) en el campo de instrucciones del Gem.
2. **En cada sesión nueva:**
   - se carga por chat la pieza 2 (maestro),
   - se carga por chat la pieza 3 (guía de estilo).
3. **En cada cuaderno de tarea:**
   - el usuario pega la pieza 6 correspondiente (CONTEXTO_UD1, UD2, etc.) como celda markdown del cuaderno, justo debajo del enunciado.
   - **La sección 9 del CONTEXTO_UD# se rellena en cada sesión usando el prompt 3 del flujo, no manualmente.**
4. **El usuario maneja la pieza 4** (prompts del flujo) por su cuenta — no se cargan a Gemini, son la chuleta del usuario para pegar en el chat según el momento.
5. **La pieza 5** (plantilla genérica) solo se usa cuando hay que crear un nuevo CONTEXTO_UD#.

---

## 7. Prompts para regenerar cada pieza

Estos son los prompts que pasaríamos a otra IA si hubiera que reconstruir el sistema desde cero.

### 7.1 Prompt para regenerar la pieza 1 (Instrucciones del Gem)

```
Genera un documento Markdown con instrucciones blindadas para un Gem de Gemini que actúa como asistente técnico para tareas y examen práctico del módulo BDA (Big Data Aplicado) en Google Colab.

Restricciones:
- Máximo 4500 caracteres (límite del campo de instrucciones del Gem).
- Exactamente 7 reglas operativas, ni una más.
- Tono técnico, directo, imperativo.

Debe contener:
- identidad y documentos a seguir,
- auto-chequeo al inicio (si faltan documentos cargados, los pide),
- jerarquía cuando haya conflicto,
- las 7 reglas operativas:
  1. Analiza primero, escribe después
  2. Consulta el contexto y cita la regla aplicable antes de proponer código técnico
  3. Un bloque mínimo por apartado (markdown + code + conclusión)
  4. Tras ejecutar, conclusión y stop
  5. Decisiones delicadas requieren confirmación
  6. No seguir por inercia
  7. Verifica antes de afirmar (anti-alucinación)
- estilo (delegando a guía cargada por chat),
- declaración explícita "modo interactivo controlado, no autónomo",
- prohibición literal de tokens: Task, Plan, Strategy, Roadmap, Checklist, Summary, Next step,
- regla final de seguridad ("ante la duda: pregunta").

El contexto de la unidad vive en una celda markdown del cuaderno marcada como CONTEXTO_UD#, no se carga por chat.
```

### 7.2 Prompt para regenerar la pieza 2 (Documento maestro)

```
Genera un documento Markdown que amplía las instrucciones del Gem para asistente BDA en Colab. Se carga por chat al inicio de cada sesión.

NO debe duplicar lo que ya está en las instrucciones del Gem. Debe contener:
- principio "memorizar ≠ aplicar" explicando que tener un documento en contexto no garantiza aplicarlo, por eso la regla 2 del Gem (citar contexto) es obligatoria,
- jerarquía de fuentes detallada,
- regla anti-reinterpretación,
- separación chat vs cuaderno,
- regla de tamaño de paso con ejemplos correctos vs incorrectos,
- regla de error (describir, proponer corrección, esperar confirmación si cambia criterio),
- regla de trazabilidad,
- regla de adaptación al dataset,
- listado completo de decisiones a consultar agrupadas por tema (calidad, integración, análisis, herramienta, estrategia),
- fase final opcional de depuración editorial.

Tono técnico, directo, sin relleno.
```

### 7.3 Prompt para regenerar la pieza 3 (Guía de estilo)

```
Genera un documento Markdown que controla cómo debe verse el cuaderno BDA en Colab. Se carga por chat al inicio de cada sesión.

Debe contener:
- regla general (cuaderno solo con lo necesario, no es chat ni informe),
- estructura permitida por apartado: una markdown + una code + una conclusión (en celda del cuaderno, no solo en chat),
- formato de cada celda con prohibiciones explícitas,
- formato de la conclusión:
  - reglas (1-3 líneas, primera persona del singular "He...", terminar con dato concreto),
  - frases prohibidas al inicio: "Se ha...", "Hemos...", "El proceso de... se basa en...",
  - frases prohibidas al final: "asegurando que...", "garantizando la...", "permitiendo así...", "evitando así que...", "manteniendo la...", "demostrando ser una técnica...",
  - ejemplos válidos y NO válidos sacados de sesiones reales,
- prohibiciones absolutas en el cuaderno (Task, Plan, Strategy, Roadmap, Summary, Next step en español o inglés),
- estilo de comentarios en código,
- fase final opcional de depuración editorial con qué sí y qué no puede tocar.

Tono operativo. Incluir ejemplos concretos, no solo abstractos.
```

### 7.4 Prompt para regenerar la pieza 4 (Prompts del flujo)

```
Genera un documento Markdown con 9 prompts ordenados (8 del flujo + 1 de emergencia) para controlar a Gemini durante una sesión de tarea BDA en Colab.

Flujo lineal:
[INICIO]
  1. Carga de documentos (maestro + guía + ubicar CONTEXTO_UD#)
  2. Fase inicial: exploración estructural del dataset (una celda de código)
  3. Análisis previo: rellenar la sección 9 del CONTEXTO_UD# con reglas del dataset
[CICLO POR APARTADO]
  4. Analizar siguiente apartado (cita del CONTEXTO_UD#, propuesta, espera OK)
  5. Autorizar bloque (con hueco fijo de aclaraciones, celda de código condicional, aclaración sobre "bloque mínimo")
  → ejecuta el usuario
  6. Pedir conclusión (con frases prohibidas explícitas al inicio y al final)
  7. Pasar al siguiente apartado (versión corta del 4)
[CIERRE]
  8. Auditoría final (cobertura, granularidad, limpieza, estilo)
[EMERGENCIA]
  9. Vuelve al protocolo

Cada prompt debe ser autocontenido y copiable directamente. Incluir índice del flujo, tabla resumen de uso típico.
```

### 7.5 Prompt para regenerar la pieza 5 (Plantilla genérica)

```
Genera un documento Markdown con una plantilla genérica de contexto de unidad, con huecos [ADAPTAR] para crear contextos específicos UD1, UD2, UD3, UD4, UD5.

Estructura mínima común:
1. Qué evalúa la unidad
2. Qué manda en caso de duda
3. Mapa de los apartados del enunciado (tabla resumen)
4. Reglas semánticas críticas por apartado (formato: Significa / No es / Patrón típico / 🔍 Granularidad correcta / 🔍 Riqueza requerida)
5. Decisiones que el agente NO puede tomar solo
6. Bloques operativos mínimos de referencia
7. Señales de que está bien resuelto
8. Señales de que se está equivocando
9. Reglas específicas del dataset (a rellenar por el agente con el prompt 3 del flujo)

NO incluir prompts dentro de la plantilla. El rellenado de la sección 9 lo gestiona el prompt 3 del flujo, no un prompt local.

La plantilla se pega como celda markdown del cuaderno. Se queda permanente como referencia para el agente, NO se carga por chat.
```

### 7.6 Prompt para regenerar una pieza 6 (Contexto de unidad concreto)

```
Genera un documento Markdown que sea el CONTEXTO_UD# completo de la unidad [NOMBRE_UNIDAD] del módulo BDA, partiendo de la plantilla genérica.

Para rellenarlo necesitas:
- el enunciado oficial de la tarea de la unidad,
- la guía del profesor sobre cómo hacer la tarea,
- al menos una tarea resuelta y aprobada como referencia,
- los errores reales documentados que ha cometido Gemini en esta unidad.

El documento debe:
- empezar con un índice (tabla de contenidos),
- mapear todos los apartados del enunciado,
- detallar las reglas semánticas críticas SOLO de los apartados donde Gemini se ha equivocado o puede equivocarse,
- incluir notas de granularidad correcta cuando la unidad de análisis difiera del dataset principal,
- incluir notas de riqueza requerida cuando "lo mínimo" se quede corto (visualizaciones, varias columnas a escalar, etc.),
- proporcionar bloques operativos mínimos genéricos con [ADAPTAR] (no copiar literal de la tarea de referencia),
- incluir señales claras de error frecuente y de buena resolución,
- terminar con sección 9 "Reglas específicas del dataset" en blanco con [ADAPTAR] (sin incluir prompt local — eso lo gestiona el prompt 3 del flujo).

Longitud orientativa: 350-450 líneas, sin relleno. Cada apartado del enunciado merece una entrada solo si tiene riesgo de error.

Esta celda se pega en el cuaderno de la tarea, debajo del enunciado.
```

---

## 8. Recomendación de modelo

El sistema funciona con cualquier modelo Gemini moderno, pero hay diferencias prácticas observadas:

- **Gemini Flash** (versión más reciente disponible): recomendado para este sistema. Sigue instrucciones estructuradas más literalmente, con menor tendencia a "interpretar" o "mejorar" lo pedido. Es ventaja para tareas educativas con formato fijo.
- **Gemini Pro**: mayor capacidad de razonamiento, pero también mayor tendencia a añadir interpretaciones no solicitadas o a sobre-pensar instrucciones simples. Útil cuando hay un problema técnico complejo que requiere análisis profundo, pero suele ser contraproducente para el flujo controlado del sistema.

Recomendación: usar Flash por defecto. Cambiar a Pro solo para apartados muy concretos donde el razonamiento profundo aporte (interpretar resultados estadísticos ambiguos, debugging complejo).

---

## 9. Cierre y principio diagnóstico

> Si las 6 piezas están bien hechas, el comportamiento descrito en la sec 5 debe reproducirse sin reglas adicionales. Si no se reproduce, el problema casi siempre está en:
> 1. la pieza 1 (instrucciones del Gem) — porque es la única siempre activa,
> 2. la pieza 6 (contexto de la unidad) — porque define el patrón técnico real,
> 3. el prompt usado en ese momento (pieza 4) — porque es el que activa el comportamiento concreto.
>
> Casi nunca el problema está en la pieza 2, 3 o 5.
>
> Y casi nunca el problema se resuelve añadiendo más reglas. Suele resolverse simplificando, fortaleciendo el prompt puntual, o moviendo una regla a la pieza 1 si tiene que estar siempre activa.

**Principio diagnóstico clave:** *"No suele bastar con dar más contexto técnico si el problema es de comportamiento."* Si Gemini se descontrola, normalmente la solución no es ampliar el contexto de unidad sino reforzar el control en el Gem o usar el prompt 9 (vuelve al protocolo).
