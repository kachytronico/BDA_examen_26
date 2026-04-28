# CONTEXTO_UD# · [Nombre de la unidad]

> **Cómo usar esta plantilla:** pégala en una celda markdown del cuaderno justo debajo del enunciado. Adáptala a la unidad concreta tú mismo, o pídeselo al agente con el prompt de adaptación que está al final.

---

## 1. Qué evalúa esta unidad

[ADAPTAR · 2-4 líneas describiendo qué se evalúa de verdad en esta unidad. Ejemplo UD1: "Proceso ETL completo: explicación, carga, perfilado, normalización numérica, validación, integración, agregación, reducción, validación cruzada, hipótesis, anomalías, salida a NoSQL."]

## 2. Qué manda en caso de duda

1. enunciado oficial,
2. **este contexto de unidad**,
3. tareas resueltas de la unidad revisadas,
4. cuadernos de ejemplo del profesor,
5. teoría general.

Si una definición genérica contradice el patrón operativo de esta unidad, **manda el patrón de la unidad**.

## 3. Reglas semánticas críticas (interpretación obligatoria de apartados)

[ADAPTAR · una entrada por cada apartado del enunciado donde el agente puede equivocarse de interpretación. Formato:]

### Apartado X · "[título literal del apartado]"
**Significa:** [definición operativa exacta en esta unidad]
**No es:** [lo que NO debe sustituir a esta interpretación, aunque sea técnicamente parecido]
**Patrón típico de resolución:** [qué hacer en líneas generales]

[Ejemplo UD1, apartado 4 "Normalización":
**Significa:** escalado de variables numéricas continuas con StandardScaler o MinMaxScaler.
**No es:** limpiar strings, pasar a minúsculas, renombrar columnas, arreglar espacios.
**Patrón típico:** detectar columnas numéricas → separar identificadores de continuas → escalar las continuas → mostrar evidencia (head, mean, std).]

## 4. Decisiones que el agente NO puede tomar solo en esta unidad

[ADAPTAR · lista corta de decisiones específicas de la unidad que requieren consulta. Ejemplo UD1: elegir qué columnas escalar si hay ambigüedad, decidir si una columna ordinal entra como numérica, elegir entre StandardScaler y MinMaxScaler.]

## 5. Bloques operativos mínimos de referencia

[ADAPTAR · plantillas de código mínimas para los apartados típicos de la unidad. Pueden ser 2-5 bloques. El agente debe adaptarlos al dataset real, no copiarlos a ciegas.]

```python
# Bloque ejemplo: detectar columnas numéricas
num_cols = df.select_dtypes(include=['number']).columns.tolist()
print(num_cols)
```

## 6. Señales de que el apartado está bien resuelto

[ADAPTAR · 3-5 señales visibles de que un apartado típico de la unidad está correctamente resuelto.]

## 7. Señales de que el agente se está equivocando

[ADAPTAR · 3-5 señales visibles de que el agente está reinterpretando, simplificando mal o saliéndose del patrón. Aquí entran los errores reales que ya hemos visto en sesiones anteriores.]

---

## Prompt de adaptación de la plantilla (opcional)

Si quieres que el agente adapte esta plantilla a una unidad concreta, pégale esto al inicio de la sesión:

```text
La celda CONTEXTO_UD# del cuaderno está en plantilla genérica con [ADAPTAR].

Antes de tocar nada del cuaderno:
1. Lee el enunciado oficial.
2. Propón en el chat (no en el cuaderno) cómo rellenarías cada [ADAPTAR] de la plantilla, basándote SOLO en lo que dice el enunciado y en lo que sepas del módulo BDA.
3. Si no estás seguro de algún apartado, dilo abiertamente y pídeme la información.
4. No edites la celda hasta que yo te dé el OK con la propuesta concreta.
```

---

## Nota sobre el formato

Esta celda se queda en el cuaderno como referencia permanente para el agente. **No la borres ni la escondas.** Cuanto más cerca esté del enunciado, mejor: facilita que el agente la lea cuando le pidas que cite una regla del contexto.
