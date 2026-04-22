# 🔧 GUÍA PARA ARREGLAR EL CUADERNO ACTUAL (UD03_PIG01)

## Problema resumido
El cuaderno se atasca en Pig porque usa rutas HDFS absolutas (`/input/`, `/output/`) que no existen en Colab sin HDFS real formateado.

---

## ✅ SOLUCIÓN: 3 CAMBIOS ESPECÍFICOS

### 1️⃣ ANTES del script Pig: Preparar carpeta relativa

**Ubicación:** Después de descargar/copiar los datasets, ANTES de crear scripts Pig.

```python
# Crear carpeta relativa
!mkdir -p input

# Copiar archivos descargados (AJUSTA según dónde tengas los archivos)
!cp /content/datasets/*.csv input/

# Verificar que existen
!ls -la input/
```

**Resultado esperado:**
```
-rw-r--r--  cards_data.csv
-rw-r--r--  users_data.csv
-rw-r--r--  transactionsCORTO_data.csv
-rw-r--r--  transactions_data.csv
```

---

### 2️⃣ CAMBIO EN SCRIPT PIG (process_transactions.pig)

**CAMBIAR:**
```pig
transactions_raw = LOAD '/input/transactionsCORTO_data.csv'
  USING PigStorage(',') AS (...)

...

STORE transactions_filled_nulls INTO '/output/transactions_cleaned' 
  USING PigStorage(',');
```

**POR:**
```pig
transactions_raw = LOAD 'input/transactionsCORTO_data.csv' 
  USING PigStorage(',') AS (...)

...

STORE transactions_filled_nulls INTO 'salida_limpios' 
  USING PigStorage(',');
```

**Cambios específicos:**
- `/input/` → `input/` (quitar `/` inicial)
- `/output/transactions_cleaned` → `salida_limpios` (quitar `/`, renombrar)

---

### 3️⃣ ELIMINAR línea de borrado HDFS

**ELIMINAR esta línea:**
```python
!hdfs dfs -rm -r /output/transactions_cleaned
```

**REEMPLAZAR por:**
```python
!rm -rf salida_limpios
```

---

## 📋 RESULTADO COMPLETO CORRECTO

```python
# ====== PASO PREVIO: Preparar ======
!mkdir -p input
!cp /content/datasets/*.csv input/
!ls input/

# ====== Script Pig ======
%%writefile procesar_transacciones.pig

transactions_raw = LOAD 'input/transactionsCORTO_data.csv' 
  USING PigStorage(',') AS (
    id_str:chararray,
    date:chararray,
    client_id:int,
    card_id:int,
    amount_str:chararray,
    use_chip:chararray,
    merchant_id:int,
    merchant_city:chararray,
    merchant_state_raw:chararray,
    zip_raw:chararray,
    mcc:int,
    errors_raw:chararray
  );

transactions_no_header = FILTER transactions_raw BY id_str != 'id';

transactions_cleaned_amount = FOREACH transactions_no_header GENERATE
    (int)id_str AS id,
    date,
    client_id,
    card_id,
    CASE
        WHEN amount_str IS NULL THEN 0.0
        WHEN TRIM(REPLACE(amount_str, '$', '')) MATCHES '' THEN 0.0
        ELSE (float)REPLACE(TRIM(amount_str), '$', '')
    END AS amount,
    use_chip,
    merchant_id,
    merchant_city,
    merchant_state_raw,
    zip_raw,
    mcc,
    errors_raw;

transactions_filled_nulls = FOREACH transactions_cleaned_amount GENERATE
    id,
    date,
    client_id,
    card_id,
    amount,
    use_chip,
    merchant_id,
    merchant_city,
    (merchant_state_raw is null ? 'UNKNOWN' : merchant_state_raw) AS merchant_state,
    CASE
        WHEN zip_raw IS NULL THEN '0'
        WHEN TRIM(zip_raw) MATCHES '' THEN '0'
        ELSE zip_raw
    END AS zip,
    mcc,
    (errors_raw is null ? 'NO_ERROR' : errors_raw) AS errors;

STORE transactions_filled_nulls INTO 'salida_limpios' USING PigStorage(',');

# ====== Ejecutar ======
!rm -rf salida_limpios
!pig procesar_transacciones.pig

# ====== Verificar resultado ======
!ls salida_limpios/
!head salida_limpios/part-m-00000
```

---

## 🧪 CÓMO VERIFICAR QUE FUNCIONA

Después de ejecutar el script Pig:

```python
import pandas as pd

# Leer el resultado
df_limpio = pd.read_csv('salida_limpios/part-m-00000', sep=',', header=None)

# Mostrar primeras filas
print(df_limpio.head())

# Verificar shape
print(df_limpio.shape)

# Verificar que los campos están limpios
print(df_limpio.info())
```

**Resultado esperado:**
- ✅ Archivo `part-m-00000` existe
- ✅ Se carga sin errores en Pandas
- ✅ Columnas tienen datos limpios (sin `$` en amount, sin nulos en estado, etc.)
- ✅ No hay congelación ni atasco

---

## 🎯 RESUMEN DE CAMBIOS

| Elemento | ❌ Antes | ✅ Después |
|----------|---------|----------|
| **Entrada** | `LOAD '/input/...'` | `LOAD 'input/...'` |
| **Salida** | `STORE ... INTO '/output/...'` | `STORE ... INTO 'salida_xxx'` |
| **Limpieza** | `!hdfs dfs -rm -r /output/...` | `!rm -rf salida_xxx` |
| **Verificación** | `!hdfs dfs -ls /output/...` | `!ls salida_xxx/` |
| **Resultado** | Se atasca | Funciona ✅ |

---

## 💡 POR QUÉ ESTO OCURRIÓ

El agente original no fue lo suficientemente explícito sobre:
1. **Rutas relativas vs absolutas en Colab**
2. **Necesidad de preparar `input/` antes de Pig**
3. **Diferencia entre `/input/` (HDFS inexistente) vs `input/` (local)**

Se han mejorado:
- ✅ **Contexto_UD3_agente_Gemini_Colab_v4.md** (sección 7.7 nueva)
- ✅ **BDA_03.agent.md** (regla anti-atasco Pig)
- ✅ **documento_maestro** (podría mejorarse más si es necesario)

---

## 🚀 PRÓXIMOS PASOS

1. Aplica los 3 cambios anteriores en tu cuaderno
2. Ejecuta y verifica que el script Pig termina correctamente
3. Si sigue atascándose, verificar:
   - ¿Existen los archivos en `input/`?
   - ¿El script realmente usa `'input/...'` (relativas)?
   - ¿Se borró la salida anterior con `!rm -rf`?

---

## 📞 PREGUNTAS FRECUENTES

### ¿Por qué `/input/` no funciona?
Porque en Colab no hay HDFS real formateado. `/input/` es una ruta HDFS que Pig intenta buscar, pero no existe. Pig se congela esperando.

### ¿Y `input/` (sin `/` inicial)?
Funciona porque es ruta relativa a `/content/` (donde Colab te coloca). Pig lo encuentra sin problemas.

### ¿Qué es `hdfs dfs -rm`?
Comando para borrar en HDFS (sistema distribuido). No funciona en Colab para archivos locales. Usa `!rm -rf` en su lugar.

### ¿Los datos se pierden al borrar la salida?
No. `!rm -rf salida_limpios` solo borra la carpeta de OUTPUT anterior (que no necesitas reutilizar). Los datos originales en `input/` siguen intactos.

---

## ✨ RESULTADO FINAL

Con estos cambios, tu cuaderno debería:
- ✅ Ejecutar scripts Pig sin atascarse
- ✅ Generar salidas limpias en carpetas relativas
- ✅ Permitir lectura con Pandas sin problemas
- ✅ Ser compatible con el pipeline Pandas → Pig → Spark → Hive
