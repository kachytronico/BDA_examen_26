# CONTEXTO_UD# · [Nombre de la unidad]

> **Versión de la plantilla:** v3
> **Cómo usar esta plantilla:** pégala en una celda markdown del cuaderno. Adáptala a la unidad concreta tú mismo.

> **Cambios respecto a v2:**
> - Eliminada la sección 9 "Reglas específicas del dataset". Esa información vive ahora en la celda PLAN_TAREA del cuaderno.
> - Eliminada cualquier referencia a requisitos del enunciado concreto. Esos requisitos viven en PLAN_TAREA.
> - Este documento queda con **reglas conceptuales fijas de la unidad**, reutilizables para cualquier enunciado.

---

## 1. Qué se evalúa en esta unidad

[ADAPTAR · 2-4 líneas describiendo qué se evalúa **conceptualmente** en esta unidad. NO requisitos del enunciado concreto. Ejemplo UD1: "Proceso ETL completo: explicación, carga, perfilado, normalización numérica, validación, integración, agregación, reducción, validación cruzada, hipótesis, anomalías, salida a NoSQL."]

## 2. Qué manda en caso de duda

1. **enunciado oficial** del ejercicio actual,
2. **PLAN_TAREA** (lectura concreta del enunciado y estado de avance),
3. **este contexto de unidad** (reglas conceptuales),
4. tareas resueltas y aprobadas,
5. cuadernos de ejemplo del profesor,
6. teoría general.

Si una definición genérica contradice el patrón operativo de esta unidad, **manda el patrón de la unidad**.

## 3. Mapa conceptual de los apartados típicos de la unidad

[ADAPTAR · tabla con los apartados típicos de la unidad. NO numerar como 1, 2, 3 — usar el tema del apartado. La numeración real depende del enunciado y se anota en PLAN_TAREA.]

| Apartado típico | Bloque conceptual | Lo crítico |
|---|---|---|
| [Tema 1] | [bloque] | [criterio] |
| [Tema 2] | [bloque] | [criterio] |

## 4. Reglas semánticas críticas por apartado

[ADAPTAR · una entrada por cada apartado de la unidad donde el agente puede equivocarse de interpretación. Formato:]

### Apartado: "[tema del apartado]"
**Significa:** [definición operativa exacta en esta unidad]
**No es:** [lo que NO debe sustituir a esta interpretación, aunque sea técnicamente parecido]
**Patrón típico de resolución:** [qué hacer en líneas generales]
**🔍 Granularidad correcta** (cuando aplique): [a qué nivel debe aplicarse el análisis]
**🔍 Riqueza requerida** (cuando aplique): [visualizaciones, complementos]

[Ejemplo UD1, apartado Normalización:
**Significa:** escalado de variables numéricas continuas con StandardScaler o MinMaxScaler.
**No es:** limpiar strings, pasar a minúsculas, renombrar columnas, arreglar espacios.
**Patrón típico:** detectar columnas numéricas → separar identificadores de continuas (consultar PLAN_TAREA) → escalar las continuas → mostrar evidencia.
**🔍 Riqueza requerida:** si hay varias variables continuas, escalar todas, no solo la objetivo.]

## 5. Decisiones que el agente NO puede tomar solo en esta unidad

[ADAPTAR · lista de decisiones específicas de la unidad que requieren consulta. Especialmente importante: decisiones de granularidad cuando el dataset es jerárquico (líneas vs episodios, eventos vs usuarios, etc.).]

## 6. Bloques operativos mínimos de referencia

[ADAPTAR · plantillas de código mínimas para los apartados típicos de la unidad. El agente debe adaptarlos al dataset real consultando PLAN_TAREA, no copiarlos a ciegas.]

```python
# Bloque ejemplo: detectar columnas numéricas
num_cols = df.select_dtypes(include=['number']).columns.tolist()
print(num_cols)
```

## 7. Señales de que el apartado está bien resuelto

[ADAPTAR · 3-5 señales visibles de que un apartado típico de la unidad está correctamente resuelto.]

## 8. Señales de que el agente se está equivocando

[ADAPTAR · 3-5 señales visibles de que el agente está reinterpretando, simplificando mal o saliéndose del patrón. Aquí entran los errores reales que ya hemos visto en sesiones anteriores.]

---

## Nota sobre el formato

Esta celda se queda en el cuaderno como referencia permanente para el agente. **No la borres ni la escondas.** Cuanto más cerca esté del enunciado, mejor: facilita que el agente la lea cuando le pidas que cite una regla del contexto.

> **Recordatorio importante:** la información concreta del enunciado actual (apartados pedidos, mapa apartado↔unidad, datasets a usar, claves de integración, granularidad fijada, decisiones tomadas) **NO va en este documento**, va en la celda PLAN_TAREA del cuaderno. Si el examen mezcla apartados de varias unidades, habrá una celda CONTEXTO_UD# por unidad y una sola celda PLAN_TAREA.
