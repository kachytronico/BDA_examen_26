# Plantilla 07 · Pig · Top N palabras (TOKENIZE + UNION + ORDER → STORE → !head)

> **Cuándo usar:** apartado 3.2 del enunciado UD3 (tratamiento de datos con Pig: análisis interesante).
> **Requisitos previos:** plantilla 05 (instalación de Pig) ejecutada. Si las palabras se buscan en datos limpios, plantilla 06 ejecutada antes (la salida `salida_limpios/` puede ser una de las entradas).
> **Cómo se inyecta:** el usuario pasa este `.md` por chat cuando el agente la pide.
> **Resultado esperado:** archivo CSV con todas las palabras ordenadas DESC en `salida_palabras_ordenado/part-*` y las `<N>` más repetidas mostradas con `!head`.

---

## Por qué esta plantilla existe

UD3 suele pedir un análisis tipo "top N palabras más repetidas" sobre uno o varios CSVs. El patrón directo `ORDER BY ... DESC` + `LIMIT 3` + `STORE` falla en Pig 0.17 con `ERROR 2244: Job failed, hadoop does not return any error message` (bug conocido del CONTEXTO_UD3 v6 §4).

El patrón verificado y seguro es:

1. `ORDER BY ... DESC` → ordena la relación.
2. `STORE` → materializa el fichero ordenado en disco.
3. `!head -n <N> salida.../part-*` → extrae las N primeras líneas en orden DESC garantizado (head es secuencial, lee desde el inicio).

Esta plantilla aplica el patrón sobre 1 o 2 CSVs (UNION para juntar textos), con stopwords inline encadenadas con `!=` (Pig 0.17 no tiene `IN`).

---

## Celda Markdown (a insertar antes de las celdas de código)

```markdown
## 🐷 Pig · Top <N> palabras más repetidas

Aplicamos el patrón canónico para obtener un ranking de palabras:

1. **Cargar** los CSVs implicados (cada uno con su esquema).
2. **Filtrar la cabecera** y proyectar solo las columnas de texto a analizar.
3. **Tokenizar** en minúsculas (`TOKENIZE(LOWER(...))`), concatenando varias columnas si hace falta con `CONCAT`.
4. **Unir** las relaciones de palabras con `UNION` si hay más de un origen.
5. **Filtrar stopwords** con lista cerrada inline (Pig 0.17 no tiene `IN`, así que se encadenan desigualdades).
6. **Agrupar y contar** las ocurrencias de cada palabra.
7. **Ordenar DESC y `STORE`** el ranking completo.
8. **Extraer las N primeras** con `!head -n <N>` sobre el `part-*` (orden DESC garantizado por la lectura secuencial de head).

Este patrón evita el bug conocido de Pig 0.17 con `ORDER BY` + `LIMIT` directo.
```

---

## Celda de código 1 · Limpieza preventiva de la salida

```python
!rm -rf salida_palabras_ordenado
```

---

## Celda de código 2 · Script Pig (`%%writefile`)

> **Adaptación:** sustituir nombres de archivos, esquemas (del mapeo Pandas → Pig), columnas de texto a analizar, lista de stopwords y `<N>`. Si solo hay un archivo, suprimir todo lo relativo al segundo.

```python
%%writefile top_palabras.pig

-- 1. Cargar PRIMER CSV con su esquema real (salida del mapeo Pandas → Pig)
[ADAPTAR_REL_1]_raw = LOAD 'input/[ADAPTAR_ARCHIVO_1].csv' USING PigStorage(',') AS (
    [ADAPTAR_ESQUEMA_1]
);
[ADAPTAR_REL_1] = FILTER [ADAPTAR_REL_1]_raw BY [ADAPTAR_COL_ID_1] != '[ADAPTAR_VALOR_CABECERA_1]';

-- Tokenizar columnas de texto del primer CSV (concatenar con CONCAT si son varias)
texto_1 = FOREACH [ADAPTAR_REL_1] GENERATE
    FLATTEN(TOKENIZE(LOWER(CONCAT([ADAPTAR_COL_TEXTO_1A], CONCAT(' ', [ADAPTAR_COL_TEXTO_1B]))), ' ')) AS palabra;

-- 2. Cargar SEGUNDO CSV (puede ser salida_limpios/ de la plantilla 06 o un input/ directo)
[ADAPTAR_REL_2]_raw = LOAD '[ADAPTAR_RUTA_2]' USING PigStorage(',') AS (
    [ADAPTAR_ESQUEMA_2]
);
-- Si la fuente es 'salida_limpios/part-m-*' NO necesita FILTER de cabecera (Pig ya la quitó)
-- Si la fuente es 'input/<archivo>.csv' SÍ necesita el FILTER:
-- [ADAPTAR_REL_2] = FILTER [ADAPTAR_REL_2]_raw BY [ADAPTAR_COL_ID_2] != '[ADAPTAR_VALOR_CABECERA_2]';

texto_2 = FOREACH [ADAPTAR_REL_2]_raw GENERATE
    FLATTEN(TOKENIZE(LOWER(CONCAT([ADAPTAR_COL_TEXTO_2A], CONCAT(' ', [ADAPTAR_COL_TEXTO_2B]))), ' ')) AS palabra;

-- 3. Unir todas las palabras
todas_palabras = UNION texto_1, texto_2;

-- 4. Filtrar vacíos y stopwords (lista cerrada inline — Pig 0.17 no tiene IN)
palabras_validas = FILTER todas_palabras BY palabra IS NOT NULL AND palabra != ''
    AND palabra != '[ADAPTAR_SW_1]'
    AND palabra != '[ADAPTAR_SW_2]'
    AND palabra != '[ADAPTAR_SW_3]'
    AND palabra != '[ADAPTAR_SW_4]'
    AND palabra != '[ADAPTAR_SW_5]'
    AND palabra != '[ADAPTAR_SW_6]';

-- 5. Agrupar y contar
grupos = GROUP palabras_validas BY palabra;
conteos = FOREACH grupos GENERATE
    group AS palabra,
    COUNT(palabras_validas) AS cnt;

-- 6. Ordenar DESC y guardar (el TOP <N> se extrae con !head, NO con LIMIT directo)
ordenado = ORDER conteos BY cnt DESC;
STORE ordenado INTO 'salida_palabras_ordenado' USING PigStorage(',');
```

---

## Celda de código 3 · Ejecución y verificación

```python
# Ejecutar el script Pig (filtrando líneas INFO para reducir ruido)
print("[Ejecutando top_palabras.pig...]")
!pig top_palabras.pig 2>&1 | grep -E "FAILED|Successfully stored|ERROR" || true

# TOP <N> en orden DESC garantizado (head secuencial)
print(f"\n=== TOP <N> PALABRAS MÁS REPETIDAS ===")
!head -n [ADAPTAR_N] salida_palabras_ordenado/part-*
```

---

## Reglas de uso

### Reglas duras
- **No usar `LIMIT` tras `ORDER BY` directamente** — provoca el bug `ERROR 2244` en Pig 0.17. Siempre `ORDER → STORE → !head`.
- **Lista de stopwords inline con `!=` encadenadas** — Pig 0.17 no tiene `IN`. No usar `MATCHES '^(a|an|the)$'` (técnicamente funciona pero el v6 lo desaconseja por reglas de escape entre `%%writefile` y Pig).
- **No depender de archivos de stopwords externos** (`stopwords.txt`) salvo que se creen con `%%writefile` en una celda previa del cuaderno.
- **El `LOAD` desde `salida_limpios/part-m-*`** (si se viene de la plantilla 06) **no lleva FILTER de cabecera** — Pig ya la quitó. Solo los CSV originales en `input/` la llevan.
- Verificación: `!head -n <N>` debe mostrar palabras reales con sus contadores. Si la salida está vacía o tiene una sola línea, el script falló silenciosamente (revisar tokenización, columnas, encoding).

### Adaptaciones típicas
- **Solo 1 archivo:** suprimir todo lo relativo a `[ADAPTAR_REL_2]`/`texto_2` y la línea `UNION`. Cambiar `todas_palabras = UNION ...` por `todas_palabras = texto_1`.
- **Más de 2 archivos:** añadir `texto_3`, `texto_4`... y encadenar `UNION texto_1, texto_2, texto_3, ...`.
- **Una sola columna de texto** (no concatenación): usar `TOKENIZE(LOWER(<col>), ' ')` directamente, sin `CONCAT`.
- **Idioma de stopwords:** si el dataset es en español, lista típica `'la','el','de','y','o','en','con','sin','un','una','los','las'`. Si es en inglés, `'a','an','the','and','or','of','to','for','is','are','was','were'`. Mezcla si los datos lo justifican.
- **Stopwords de dominio:** añadir términos del dominio del dataset que aparecen mucho pero no aportan (ej: `'transaction','swipe','online'` en datos de transacciones).
- **Valor de N:** `<N>` se sustituye por lo que pida el enunciado (3, 5, 10...).

### Qué NO cambiar
- La secuencia `LOAD → FILTER cabecera → tokenize → UNION → FILTER stopwords → GROUP → COUNT → ORDER → STORE`.
- El nombre de la carpeta de salida: `salida_palabras_ordenado` (descriptiva).
- La extracción del TOP N con `!head` (no con `LIMIT` Pig).

### Si el agente quiere cambiar la lógica
Si el enunciado pide algo distinto al "top N palabras" (ej: "top N categorías", "top N ciudades"), **no usar esta plantilla**. Usar el bloque 7.5 del CONTEXTO_UD3 v6 (ranking por grupo) que es más simple. Esta plantilla es específica de tokenización de texto.
