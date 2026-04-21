# Documento de control de decisiones para Gemini en Colab

## Objetivo
Evitar que el agente tome por su cuenta decisiones que puedan desviar, simplificar mal o estropear la resolución del examen.

## Regla general
Si una decisión cambia el sentido del análisis, elimina información, fija un criterio discutible o tiene varias opciones razonables, **debes pararte y consultarme antes de seguir**.

## Formato obligatorio cuando debas consultarme
Cuando detectes una decisión que requiere validación, responde en el chat con este formato:

- **Situación detectada:** qué has observado.
- **Decisión pendiente:** qué hay que decidir.
- **Opciones razonables:** máximo 2 o 3.
- **Consecuencia de cada opción:** breve y clara.
- **Recomendación provisional:** cuál te parece mejor y por qué.
- **Pregunta final:** qué opción quieres que aplique.

No crees nuevas celdas de código hasta que yo responda.

---

## 1. Calidad de datos: decisiones que no puedes tomar solo

### Nulos
Debes consultarme antes de:
- eliminar filas con nulos,
- rellenar nulos con media, moda, cero, “desconocido” u otro valor,
- dejar nulos sin tratar si afectan al punto actual.

### Duplicados
Debes consultarme antes de:
- eliminar duplicados sin explicar qué columnas definen el duplicado,
- quedarte con un registro y descartar otro.

### Tipos de datos
Debes consultarme antes de:
- convertir columnas a fecha, entero, float o categoría si puede haber pérdida de información,
- forzar conversiones que generen valores nulos o errores.

### Columnas y filas
Debes consultarme antes de:
- borrar columnas,
- filtrar filas de forma agresiva,
- reducir el dataset de forma irreversible.

---

## 2. Integración de datos: decisiones que no puedes tomar solo

### Claves de unión
Debes consultarme antes de:
- elegir entre varias posibles claves de unión,
- asumir que dos columnas equivalentes son la misma clave,
- hacer un merge si no está clara la relación.

### Tipo de merge
Debes consultarme antes de elegir:
- left join,
- inner join,
- right join,
- outer join.

Si hay duda, debes explicar qué cambia en el resultado según el tipo de unión.

### Enriquecimiento
Debes consultarme antes de:
- crear columnas derivadas no evidentes,
- combinar datasets si no está claro que sea correcto.

---

## 3. Análisis y transformaciones: decisiones que no puedes tomar solo

### Agregaciones
Debes consultarme antes de decidir:
- qué variable agrupar,
- qué métricas usar,
- si hay varias agregaciones razonables.

### Hipótesis
Debes consultarme antes de:
- elegir una hipótesis concreta entre varias posibles,
- interpretar resultados de forma fuerte sin evidencia clara.

### Anomalías
Debes consultarme antes de:
- fijar umbrales,
- marcar registros como anómalos,
- decidir si eliminarlos, separarlos o solo reportarlos.

---

## 4. Elección de herramienta: decisiones que no puedes tomar solo

### MongoDB vs Neo4j
Debes consultarme antes de elegir una de las dos si el enunciado no lo fuerza claramente.

### Hadoop / HDFS / grep / MapReduce
Debes consultarme si:
- hay varios archivos posibles para analizar,
- hay varias palabras o patrones razonables para buscar,
- el objetivo del conteo no está completamente claro.

### Pig / Spark / Hive
Debes consultarme si:
- hay varias transformaciones posibles,
- no está claro qué tablas crear,
- no está claro qué consultas HQL son las mejores.

---

## 5. Estrategia general: decisiones que no puedes tomar solo

Debes consultarme antes de:
- cambiar el orden aprobado de resolución,
- saltarte un punto del enunciado,
- resolver dos puntos a la vez,
- sustituir un bloque simple por otro más complejo,
- abandonar una estrategia ya validada,
- rehacer una parte importante del cuaderno.

---

## 6. Casos en los que sí puedes avanzar sin consultarme

Puedes seguir sin preguntarme cuando:
- el siguiente paso es directo y obvio según el enunciado,
- solo vas a cargar, mostrar, comprobar o validar algo,
- la comprobación no implica pérdida de información,
- el bloque no fija una decisión discutible,
- el contexto y la chuleta dejan claro qué hacer.

Aun así, si hay riesgo de error conceptual, debes parar.

---

## 7. Regla de seguridad final
Ante la duda:
- no decidas,
- no simplifiques,
- no borres,
- no supongas,
- pregunta.

Tu prioridad no es avanzar rápido.
Tu prioridad es no cometer una decisión equivocada que arruine el ejercicio.
