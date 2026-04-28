# Prompt 2-bis · Análisis previo y reglas del dataset

> **Cuándo usarlo:** después del prompt 2 (exploración estructural ejecutada) y **antes** del prompt 3 (analizar el primer apartado).
>
> **Por qué existe:** la exploración del prompt 2 produce salida cruda. Este prompt convierte esa salida en reglas claras del dataset que el agente aplicará en cada apartado. Rellena la sección 9 del CONTEXTO_UD#, que de otro modo quedaría vacía.

```text
Antes de empezar con los apartados del enunciado, vamos a analizar a fondo
el dataset y rellenar la sección 9 del CONTEXTO_UD#.

Basándote en los resultados de la exploración estructural que ya ejecuté:

1. Identifica los datasets disponibles y propón cuál usaremos como:
   - principal (el más grande o central),
   - secundario (el que se va a integrar con el principal),
   - enriquecimiento (tercer dataset o API para el apartado 6).

2. Para los datasets que vamos a usar:
   - identifica la(s) clave(s) común(es) para integración,
   - separa columnas numéricas continuas (candidatas a escalar) de
     columnas que parecen numéricas pero son identificadores,
   - identifica una variable objetivo plausible para validación cruzada
     y testeo de hipótesis,
   - señala columnas problemáticas (espacios, nulos altos, tipos raros)
     que requieran limpieza antes de algún apartado.

3. Propón en el chat (NO edites todavía la celda CONTEXTO_UD#) cómo
   rellenarías cada hueco [ADAPTAR] de la sección 9.

4. Si para alguno tienes dudas o ves varias opciones razonables, dilo
   claramente y pregúntame antes de fijarlo.

Cuando esté de acuerdo con tu propuesta te daré OK para que edites la
celda CONTEXTO_UD#.
```
