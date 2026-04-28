# CONTEXTO_UD# · [Nombre de la unidad]

> **Versión de la plantilla:** v2
> **Cómo usar esta plantilla:** pégala en una celda markdown del cuaderno justo debajo del enunciado. Adáptala a la unidad concreta tú mismo, o pídeselo al agente con el prompt 3 del flujo (análisis previo).

> **Cambios respecto a v1:** eliminado el prompt redundante final (M004). El rellenado de la sección 9 lo gestiona el prompt 3 del flujo, no un prompt local.

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

## 3. Mapa de los apartados del enunciado

[ADAPTAR · tabla resumen de los apartados del enunciado, con sección de la guía y "lo crítico" de cada uno]

## 4. Reglas semánticas críticas (interpretación obligatoria de apartados)

[ADAPTAR · una entrada por cada apartado del enunciado donde el agente puede equivocarse de interpretación. Formato:]

### Apartado X · "[título literal del apartado]"
**Significa:** [definición operativa exacta en esta unidad]
**No es:** [lo que NO debe sustituir a esta interpretación, aunque sea técnicamente parecido]
**Patrón típico de resolución:** [qué hacer en líneas generales]
**🔍 Granularidad correcta** (cuando aplique): [a qué nivel debe aplicarse el análisis]
**🔍 Riqueza requerida** (cuando aplique): [visualizaciones, complementos]

[Ejemplo UD1, apartado 4 "Normalización":
**Significa:** escalado de variables numéricas continuas con StandardScaler o MinMaxScaler.
**No es:** limpiar strings, pasar a minúsculas, renombrar columnas, arreglar espacios.
**Patrón típico:** detectar columnas numéricas → separar identificadores de continuas → escalar las continuas → mostrar evidencia (head, mean, std).
**🔍 Riqueza requerida:** si hay varias variables continuas, escalar todas, no solo la objetivo.]

## 5. Decisiones que el agente NO puede tomar solo en esta unidad

[ADAPTAR · lista de decisiones específicas de la unidad que requieren consulta. Especialmente importante: decisiones de granularidad cuando el dataset es jerárquico (líneas vs episodios, eventos vs usuarios, etc.).]

## 6. Bloques operativos mínimos de referencia

[ADAPTAR · plantillas de código mínimas para los apartados típicos de la unidad. El agente debe adaptarlos al dataset real, no copiarlos a ciegas.]

```python
# Bloque ejemplo: detectar columnas numéricas
num_cols = df.select_dtypes(include=['number']).columns.tolist()
print(num_cols)
```

## 7. Señales de que el apartado está bien resuelto

[ADAPTAR · 3-5 señales visibles de que un apartado típico de la unidad está correctamente resuelto.]

## 8. Señales de que el agente se está equivocando

[ADAPTAR · 3-5 señales visibles de que el agente está reinterpretando, simplificando mal o saliéndose del patrón. Aquí entran los errores reales que ya hemos visto en sesiones anteriores.]

## 9. Reglas específicas del dataset

> **Esta sección la rellena el agente con el prompt 3 del flujo (análisis previo).**

- Dataset principal: `[ADAPTAR]`
- Dataset secundario: `[ADAPTAR]`
- Dataset/API de enriquecimiento: `[ADAPTAR]`
- Clave común para integración: `[ADAPTAR]`
- Columnas numéricas continuas (candidatas a escalar): `[ADAPTAR]`
- Columnas que parecen numéricas pero son identificadores (no escalar sin consultar): `[ADAPTAR]`
- Variable objetivo para validación cruzada e hipótesis: `[ADAPTAR]`
- Nivel de granularidad para apartados estadísticos (cuando aplique): `[ADAPTAR]`

---

## Nota sobre el formato

Esta celda se queda en el cuaderno como referencia permanente para el agente. **No la borres ni la escondas.** Cuanto más cerca esté del enunciado, mejor: facilita que el agente la lea cuando le pidas que cite una regla del contexto.
