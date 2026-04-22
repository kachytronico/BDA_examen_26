# Análisis: Problema Pig en UD03 - Diferencias Críticas

## 🔴 PROBLEMA IDENTIFICADO

El cuaderno actual se **atasca en la ejecución de Pig** porque usa **rutas HDFS incorrectas**.

---

## 📊 Comparación: Tarea Resuelta (FUNCIONA) vs Tarea Actual (ATASCA)

### **TAREA RESUELTA (Funciona) - BDA03_tarea_resuelta_IJM03_2026**

```pig
transactions = LOAD '/content/input/transactions_data.csv' 
  using PigStorage(',') as (...)
  
STORE city_counts_ordered INTO 'city_counts_temp';
```

**Características:**
- ✅ Usa ruta ABSOLUTA `/content/input/` (ruta real de Colab)
- ✅ Usa ruta RELATIVA para STORE (`'city_counts_temp'`)
- ✅ Aplica el patrón **workaround TOP N** correctamente:
  ```pig
  STORE ... INTO 'city_counts_temp';
  city_counts_temp = LOAD 'city_counts_temp' ...
  top10 = LIMIT city_counts_temp 10;
  DUMP top10_cities;
  ```
- ✅ No usa `hdfs dfs -rm` en outputs relativos
- ✅ Funciona sin problemas

---

### **TAREA ACTUAL (Atasca) - UD03_PIG01**

```pig
transactions_raw = LOAD '/input/transactionsCORTO_data.csv'
  USING PigStorage(',') AS (...)

STORE transactions_filled_nulls INTO '/output/transactions_cleaned' 
  USING PigStorage(',');
```

**Problemas:**
- ❌ Usa ruta HDFS absoluta `/input/` que **NO EXISTE** en Colab
- ❌ Usa ruta HDFS absoluta `/output/` que **NO EXISTE** en Colab
- ❌ Intenta ejecutar `hdfs dfs -rm -r /output/transactions_cleaned` (fallará)
- ❌ El script es demasiado complejo para lo que intenta hacer
- ❌ **Pig intenta leer desde HDFS pero el archivo está en Colab local**
- ❌ Sin NameNode formateado, HDFS no funciona correctamente

---

## 🎯 RAÍZ DEL PROBLEMA

El **documento Contexto_UD3** dice claramente:

> **"Modo Pig de referencia en Colab: `!pig script.pig` sin `-x local`, rutas relativas."**
> 
> - **LOAD** con ruta relativa: `LOAD 'input/archivo.csv'`
> - **STORE** con ruta relativa: `STORE ... INTO 'nombre_salida'`
> - **NO** usar rutas con `/` inicial (`/input/...`, `/output/...`)

**Pero:**
1. El cuaderno actual NO sigue esto → usa `/input/` y `/output/`
2. El documento del agente es **demasiado genérico** y no es suficientemente explícito
3. Falta un **ejemplo paso-a-paso claro** de cómo hacerlo bien

---

## 💡 SOLUCIÓN CORRECTA

### Paso 1: Cargar datos CON RUTA RELATIVA

```bash
# En una celda anterior (no mostrada aquí), los datos ya están en Colab:
# /content/datasets/*.csv

# Luego se copian a un directorio relativo:
!mkdir -p input
!cp /content/datasets/* input/
```

### Paso 2: Script Pig CORRECTO

```pig
%%writefile procesar_transacciones.pig

transactions_raw = LOAD 'input/transactionsCORTO_data.csv' 
  USING PigStorage(',') AS (
    id:chararray, date:chararray, client_id:int, card_id:int,
    amount:chararray, use_chip:chararray, merchant_id:int,
    merchant_city:chararray, merchant_state:chararray, zip:chararray,
    mcc:int, errors:chararray
  );

-- Filtra cabecera
transactions_no_header = FILTER transactions_raw BY id != 'id';

-- Limpia campos
transactions_cleaned = FOREACH transactions_no_header GENERATE
    (int)id AS id, date, client_id, card_id,
    (amount IS NULL ? 0.0 : (float)REPLACE(REPLACE(amount, '$', ''), ' ', '')) AS amount,
    use_chip, merchant_id, merchant_city,
    (merchant_state IS NULL ? 'UNKNOWN' : merchant_state) AS merchant_state,
    (zip IS NULL ? '0' : zip) AS zip,
    mcc,
    (errors IS NULL ? 'NO_ERROR' : errors) AS errors;

-- Guardar RESULTADO LIMPIO con ruta relativa
STORE transactions_cleaned INTO 'salida_limpios' USING PigStorage(',');
```

### Paso 3: Ejecutar

```python
!pig procesar_transacciones.pig
```

### Paso 4: Verificar resultado

```python
!ls salida_limpios/
!head salida_limpios/part-m-00000
```

---

## 📋 MEJORAS NECESARIAS EN DOCUMENTOS DEL AGENTE

### 1. **Contexto_UD3_agente_Gemini_Colab_v4.md**

**Problema actual:** La sección 7 (Pig) es correcta en teoría pero le falta:

**Añadir:**
```markdown
### 7.7. Regla de rutas en Colab ANTES de Pig

ANTES de crear scripts Pig, SIEMPRE debes:

1. Copiar los archivos a una carpeta relativa en Colab:
   !mkdir -p input
   !cp /content/datasets/* input/

2. Verificar que existen localmente:
   !ls input/

3. SOLO ENTONCES crear scripts Pig con rutas relativas:
   LOAD 'input/archivo.csv'
   STORE ... INTO 'salida_limpios'

Si ves en un script Pig rutas con "/" inicial (/input/, /output/), 
ES UN ERROR. Debe corregirse a rutas relativas.
```

### 2. **BDA_03.agent.md (El agente)**

**Problema actual:** Dice "máximo 1 ejecución por celda Pig" pero no explica POR QUÉ.

**Mejora propuesta:**

```markdown
## Regla anti-HDFS-inexistente

ANTES de ejecutar Pig:
- Verifica que los archivos de entrada existen en ruta relativa
- NO uses `/input/...` o `/output/...` (sin /content/)
- Usa `'input/...'` o `'salida_xxx'` (rutas relativas)

Si el script intenta LOAD desde `/input/` fallará porque:
→ HDFS no tiene NameNode formateado en Colab
→ Pig sin `-x local` intenta usar HDFS
→ `/input/` no existe en el filesystem real
→ Result: El proceso se atasca sin salida de error clara

Patrón correcto:
1. Datos en `/content/datasets/` (Colab)
2. Copia a `input/` local (relativo)
3. Pig carga desde `'input/archivo.csv'`
4. Pig guarda a `'salida_xxx'` (relativo)
5. Lee desde `salida_xxx/part-m-00000` en Pandas
```

### 3. **documento_maestro_unico_gemini_colab_v3.md**

**Añadir en la sección de Regla de error:**

```markdown
## Signo de atasco en Pig

Si un script Pig:
- No produce salida después de muchos segundos
- No muestra errores claros
- El proceso parece "congelado"

Probables causas (95% de los casos):
1. Rutas HDFS incorrectas (`/input/`, `/output/`)
2. STORE sin LIMIT anterior en ORDER BY (bug Pig 0.17)
3. DUMP masivo de millones de filas

Verificar:
- ¿El script usa `/input/` o `/output/`? → Cambiar a rutas relativas
- ¿Hay ORDER BY sin STORE+RELOAD+LIMIT antes? → Aplicar workaround
- ¿El DUMP carga millones de filas? → Añadir LIMIT antes
```

---

## ✅ RECOMENDACIONES FINALES

1. **El contexto UD3 está bien BUT**: Necesita sección explícita 7.7 sobre Colab
2. **El agente BDA_03 es correcto BUT**: Necesita regla anti-HDFS-inexistente más clara
3. **El documento maestro está bien BUT**: Necesita ejemplo de "signo de atasco Pig"

**Patrón seguro para Pig en Colab:**
- Input: `'input/...'` (relativo, en Colab local)
- Output: `'salida_xxx'` (relativo, en Colab local)
- Execución: `!pig script.pig` (sin rutas HDFS)
- Lectura: `!head salida_xxx/part-m-00000` o pandas

---

## 📝 CAMBIOS ESPECÍFICOS A HACER

**Contexto_UD3_agente_Gemini_Colab_v4.md**, línea ~520, NUEVA SECCIÓN 7.7:

```markdown
### 7.7. Requisito previo: Preparar archivos de entrada en ruta relativa

Antes de ejecutar CUALQUIER script Pig en Colab:

1. Los archivos CSV deben estar disponibles en una carpeta relativa
2. **NO asumas** que están en `/input/` (no existe HDFS real)
3. Cópia siempre los archivos desde `/content/` a ruta relativa:

   !mkdir -p input
   !cp /content/datasets/*.csv input/
   !ls input/

4. Verifica que existen:
   !head input/archivo.csv

5. **SOLO ENTONCES** crea scripts Pig con rutas relativas:
   - LOAD 'input/archivo.csv'
   - STORE ... INTO 'salida_limpio'

**ERROR CRÍTICO a evitar:**
```
❌ transactions = LOAD '/input/archivo.csv'  ← FALLA (HDFS no existe)
✅ transactions = LOAD 'input/archivo.csv'   ← FUNCIONA (Colab local)
```
```

---

## 📞 RESUMEN EJECUTIVO

| Aspecto | Tarea Resuelta (✅) | Tarea Actual (❌) |
|---------|-------------------|------------------|
| **Rutas archivos entrada** | `/content/input/` | `/input/` |
| **Rutas STORE** | `'relativas'` | `'/output/...'` |
| **Workaround TOP N** | Sí, correcto | No presente |
| **hdfs dfs** | No necesario | Intenta usar |
| **Resultado** | Funciona | Se atasca |

**Conclusión:** El agente debe insistir en rutas relativas y explicar por qué `/input/` no existe en Colab.
