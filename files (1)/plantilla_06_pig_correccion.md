# Plantilla 06 · Pig · Corrección de nulos e importes

> **Cuándo usar:** apartado 3.1 del enunciado UD3 (corrección de fallos en los datos con Pig).
> **Requisitos previos:** plantilla 05 (instalación de Pig) ya inyectada y ejecutada. Datasets CSV en `input/`. Mapeo Pandas → Pig ejecutado (bloque 7.2 del CONTEXTO_UD3 v6) para conocer dtypes reales.
> **Cómo se inyecta:** el usuario pasa este `.md` por chat cuando el agente la pide.
> **Resultado esperado:** archivo CSV limpio en `salida_limpios/part-m-*` con nulos imputados y la columna numérica sucia ya casteada a `double`.

---

## Por qué esta plantilla existe

UD3 exige corregir al menos **2 problemas** de calidad con Pig (nulos, vacíos, importes con símbolos, fechas, etc.).

Esta plantilla resuelve **dos casos comunes a la vez**:

1. **Imputación de nulos** en columnas categóricas (texto/zip/errores) con un valor por defecto.
2. **Limpieza de importes** que vienen como texto con símbolos (`$`, `€`, `,`) usando el patrón **clase negada** `[^0-9.-]` — el único que el CONTEXTO_UD3 v6 §4 marca como seguro en Pig 0.17.

**Patrones prohibidos que esta plantilla NO usa** (recordatorio):
- `REPLACE(col, '$', '')` → `$` significa end-of-string en regex Pig, deja la columna vacía sobre miles de filas.
- `REGEX_EXTRACT(col, '([0-9.]+)', 1)` → patrón no anclado, devuelve null silencioso sobre cadenas con prefijos.

---

## Celda Markdown (a insertar antes de las celdas de código)

```markdown
## 🐷 Pig · Corrección de fallos en los datos

Aplicamos Apache Pig para corregir dos problemas detectados en la fase Pandas:

1. **Nulos en columnas categóricas:** se imputan con un valor por defecto (`'UNKNOWN'`, `'0'`, `'NO_ERROR'`, etc., según la columna).
2. **Importes con símbolos como texto:** se castean a `double` usando `REPLACE` con clase negada `[^0-9.-]`, que elimina cualquier carácter que no sea dígito, punto o menos. Es el único patrón verificado en Pig 0.17 para esta tarea (otras variantes con `$` literal o `REGEX_EXTRACT` no anclado dejan la columna vacía sin warning).

El script:
- carga el CSV con su esquema (todas las columnas como `chararray` excepto las que el mapeo Pandas → Pig haya identificado como numéricas limpias),
- filtra la cabecera (`FILTER ... BY id != 'id'`),
- limpia el importe con clase negada,
- imputa nulos con valores por defecto,
- guarda en `salida_limpios/`.
```

---

## Celda de código 1 · Limpieza preventiva de la salida

```python
!rm -rf salida_limpios
```

---

## Celda de código 2 · Script Pig (`%%writefile`)

> **Adaptación obligatoria:** sustituir nombre del archivo CSV, esquema (salida del mapeo Pandas → Pig), columna del importe sucio, columnas categóricas a imputar y valores por defecto.

```python
%%writefile process_<archivo>.pig

-- 1. Cargar el CSV con TODAS las columnas como chararray
--    (excepto las que el mapeo Pandas → Pig identificó como numéricas LIMPIAS)
[ADAPTAR_NOMBRE_RELACION]_raw = LOAD 'input/[ADAPTAR_NOMBRE_ARCHIVO].csv'
USING PigStorage(',') AS (
    [ADAPTAR_ESQUEMA_DEL_MAPEO]
);

-- 2. Filtrar la cabecera (idiomático en Pig 0.17)
sin_cabecera = FILTER [ADAPTAR_NOMBRE_RELACION]_raw BY [ADAPTAR_COL_ID] != '[ADAPTAR_VALOR_CABECERA_ID]';

-- 3. Limpiar la columna numérica sucia con clase negada [^0-9.-]
--    (único patrón verificado en Pig 0.17 — ver CONTEXTO_UD3 v6 §4)
con_importe_limpio = FOREACH sin_cabecera GENERATE
    [ADAPTAR_RESTO_COLUMNAS_PRESERVADAS],
    (double) REPLACE([ADAPTAR_COL_IMPORTE_SUCIO], '[^0-9.-]', '') AS [ADAPTAR_COL_IMPORTE_LIMPIO];

-- 4. Imputar nulos en columnas categóricas con valores por defecto
limpios = FOREACH con_importe_limpio GENERATE
    [ADAPTAR_COLUMNAS_QUE_NO_CAMBIAN],
    ([ADAPTAR_COL_TEXTO_1] IS NULL ? '[ADAPTAR_VALOR_DEFECTO_1]' : [ADAPTAR_COL_TEXTO_1]) AS [ADAPTAR_COL_TEXTO_1],
    ([ADAPTAR_COL_TEXTO_2] IS NULL OR TRIM([ADAPTAR_COL_TEXTO_2]) == '' ? '[ADAPTAR_VALOR_DEFECTO_2]' : [ADAPTAR_COL_TEXTO_2]) AS [ADAPTAR_COL_TEXTO_2],
    ([ADAPTAR_COL_TEXTO_3] IS NULL ? '[ADAPTAR_VALOR_DEFECTO_3]' : [ADAPTAR_COL_TEXTO_3]) AS [ADAPTAR_COL_TEXTO_3];

-- 5. Guardar el resultado limpio
STORE limpios INTO 'salida_limpios' USING PigStorage(',');
```

---

## Celda de código 3 · Ejecución y verificación

```python
# Ejecutar el script Pig
!pig process_<archivo>.pig

# Listar contenido de la salida
print("\n--- Contenido de salida_limpios/ ---")
!ls -la salida_limpios

# Mostrar primeras 10 líneas para verificar limpieza
print("\n--- Primeras 10 líneas (verificación visual) ---")
!head -n 10 salida_limpios/part-m-*
```

---

## Reglas de uso

### Reglas duras
- **El esquema del `LOAD` se obtiene del bloque 7.2 de CONTEXTO_UD3 v6 (mapeo Pandas → Pig)**, no se inventa. Cualquier columna que en Pandas salga como `object` se declara `chararray` en Pig (incluso aunque sea numérica con símbolos — la limpieza al `double` se hace en el `FOREACH`, no en el `LOAD`).
- **Solo `(double) REPLACE(col, '[^0-9.-]', '')`** para limpiar importes en Pig. **Nunca** `REPLACE(col, '$', '')` ni `REGEX_EXTRACT` no anclado.
- **Verificación obligatoria** después del STORE: `!head -n 10 salida_limpios/part-m-*` debe mostrar valores reales (no columnas vacías por dobles comas).
- Si el contador `FIELD_DISCARDED_TYPE_CONVERSION_FAILED` es alto (cerca del total de filas), parar y consultar — la columna está rota silenciosamente.

### Adaptaciones típicas según el dataset
- Si **no hay importes sucios**: eliminar el paso 3 y la columna del `FOREACH`. La plantilla queda con solo cabecera + imputación de nulos.
- Si **hay varias columnas con importes**: repetir el patrón `(double) REPLACE(...)` por cada una en el paso 3.
- Si **no hay cabecera** en el CSV: eliminar el paso 2 (`FILTER BY [col_id] != '[valor_cabecera]'`).
- **El nombre del archivo `.pig` debe coincidir con el dataset** que procesa: `process_transactions.pig`, `process_users.pig`, etc.

### Qué NO cambiar
- La secuencia `LOAD → FILTER cabecera → limpieza numérica → imputación → STORE`.
- El uso de `chararray` por defecto en el `LOAD`.
- El patrón de clase negada `[^0-9.-]`.
- El sufijo de la carpeta de salida: `salida_limpios` (descriptiva, no genérica).
- La verificación con `!head` después del STORE.

### Si la plantilla parece no encajar con el caso
Avisar al usuario en chat **antes** de empezar a modificarla (ej: "el dataset no tiene importes sucios pero sí fechas raras — propongo adaptar el paso 3 a una limpieza distinta o pasar las fechas a Spark, ¿qué prefieres?"). No la rehacer en silencio.
