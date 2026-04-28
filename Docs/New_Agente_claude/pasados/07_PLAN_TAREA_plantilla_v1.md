# PLAN_TAREA · Plantilla

> **Versión:** v1
> **Cómo usar este documento:** pégalo en una celda markdown del cuaderno justo debajo del enunciado y de las celdas CONTEXTO_UD#. La rellenará el agente con el prompt 3 del flujo. **No la borres ni la escondas.**

---

## 📋 PLAN DE LA TAREA

### Lectura del enunciado
- **Apartados pedidos:** `[ADAPTAR — ej: "1-12" o "1, 2, 5, 7"]`
- **Mapa apartado ↔ unidad:** `[ADAPTAR según corresponda — usar tabla si hay varias unidades]`

| Apartado | Unidad | Tema |
|---|---|---|
| 1 | UD? | [ADAPTAR] |
| 2 | UD? | [ADAPTAR] |
| ... | ... | ... |

- **Requisitos materiales del enunciado:** `[ADAPTAR — datasets relacionados, mínimos de filas, formato de carga, etc.]`
- **Restricciones específicas del enunciado:** `[ADAPTAR — instrucciones particulares del profesor para esta tarea concreta]`

### Datos identificados
- **Dataset principal:** `[ADAPTAR]`
- **Dataset secundario:** `[ADAPTAR]`
- **Dataset/API enriquecimiento:** `[ADAPTAR]`
- **Clave(s) de integración:** `[ADAPTAR]`
- **Variable objetivo:** `[ADAPTAR]`
- **Columnas continuas (escalables):** `[ADAPTAR]`
- **Identificadores numéricos (no escalar):** `[ADAPTAR]`
- **Granularidad correcta para análisis estadísticos:** `[ADAPTAR — ej: "agregado por episodio"]`

### Estado de avance
| Apartado | Estado |
|---|---|
| 1 | ⏳ pendiente |
| 2 | ⏳ pendiente |
| ... | ... |

> Estados: ⏳ pendiente · 🔄 en progreso · ✅ completado · ⚠️ con dudas

### Decisiones tomadas (se rellena durante la sesión)
*Vacío al inicio. Cada vez que se toma una decisión que afecta a apartados posteriores, el agente la añade aquí en una línea.*

Ejemplos de decisiones que se anotan aquí:
- "Apartado 4: aplicada variante reforzada (StandardScaler + MinMaxScaler) sobre las 5 numéricas continuas."
- "Apartado 6: integración por season+episode, enriquecimiento por título normalizado (lower+strip)."
- "Apartado 8: PCA aplicado sobre dataset agregado por episodio, no sobre líneas de diálogo."

---

## Reglas de uso de esta celda

**Para el agente:**
- La celda PLAN_TAREA contiene la **lectura concreta del enunciado** y el **estado de la sesión actual**.
- Las celdas CONTEXTO_UD# contienen las **reglas conceptuales fijas** de cada unidad. **No mezcles información entre ambos lugares.**
- Al iniciar un apartado, consulta primero PLAN_TAREA para saber a qué unidad pertenece, luego consulta el CONTEXTO_UD# correspondiente para las reglas semánticas.
- Cuando completes un apartado, actualiza la tabla "Estado de avance" y añade una línea a "Decisiones tomadas" si hubo alguna decisión relevante.
- Si tienes que reiniciar la sesión, esta celda + las celdas CONTEXTO_UD# son lo único que necesitas para continuar.

**Para el usuario:**
- Esta celda la rellena el agente al inicio (prompt 3 del flujo).
- Si en algún momento ves información obsoleta o redundante, pídele al agente que la reescriba — **no acumular**, mejor reescribir.
- Si reinicias la sesión, asegúrate de que el agente vea esta celda al empezar.
