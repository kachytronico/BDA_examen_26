# Contexto UD1 para Gemini en Colab · versión reforzada
## Gestión de soluciones Big Data · contexto operativo para resolver variantes de la tarea

## 1. Objetivo de este documento
Guiar al agente para resolver correctamente la parte de examen o tarea correspondiente a la UD1.

Este documento no es una chuleta teórica ni una solución cerrada.  
Es un contexto operativo para que el agente:

- use las fuentes correctas,
- interprete bien cada apartado del enunciado,
- adapte los bloques al dataset real,
- y no sustituya el significado del punto por una interpretación genérica.

---

## 2. Jerarquía de fuentes que manda en UD1
Cuando haya dudas, manda este orden:

1. **enunciado oficial de UD1**,  
2. **tareas resueltas de UD1 revisadas**,  
3. **cuaderno oficial de la unidad**,  
4. **contexto de esta unidad**,  
5. **teoría y resumen**,  
6. **inferencia técnica general**.

## Regla crítica
Si una definición general de ciencia de datos contradice el patrón operativo que fijan el enunciado y las tareas resueltas, **manda el patrón operativo de UD1**.

No reinterpretar por intuición.

---

## 3. Qué entra de verdad en UD1
UD1 evalúa un flujo ETL sobre varios datasets relacionados, normalmente en Colab, con estos bloques típicos:

1. explicación del conjunto de datos,
2. carga de datos,
3. perfilado de datos,
4. normalización de datos,
5. validación de datos,
6. integración y enriquecimiento,
7. agregación,
8. reducción / simplificación,
9. contraste o validación simple,
10. detección de anomalías,
11. salida o preparación final,
12. posible carga a NoSQL / cierre técnico.

## Regla importante
Los apartados del enunciado son **etapas distintas**.  
No deben fusionarse ni redefinirse sin permiso.

---

## 4. Regla semántica crítica de UD1
### Interpretación obligatoria de “normalización de datos”
En **UD1**, el punto de **normalización de datos** significa por defecto:

- **escalar variables numéricas**,
- dejar variables numéricas comparables en la misma escala,
- usar herramientas como `StandardScaler` o `MinMaxScaler`,
- y, si procede, hacer codificación de categóricas cuando el caso lo justifique.

### Lo que NO cumple por sí solo el punto 4
No se considera suficiente para resolver el punto 4, por sí solo:

- pasar texto a minúsculas,
- quitar puntuación,
- limpiar strings,
- hacer preprocesado textual,
- renombrar columnas,
- arreglar espacios,
- o “normalizar texto” en sentido genérico.

Eso puede ser limpieza o preparación, pero **no sustituye** la normalización numérica de UD1.

### Regla dura
Si el agente interpreta “normalización” como limpieza textual sin haber validado antes esa lectura con el usuario, **está interpretando mal el apartado**.

---

## 5. Qué debe hacer el agente en el punto 4 de UD1
### Objetivo
Identificar qué columnas numéricas tiene sentido escalar y aplicar una normalización coherente.

### Patrón principal
La opción por defecto debe ser:

- `StandardScaler` como patrón principal,
- `MinMaxScaler` como alternativa razonable.

### Cuándo usar StandardScaler
Cuando quieres centrar las variables en media ≈ 0 y desviación ≈ 1, especialmente si luego puede haber:

- PCA,
- modelos,
- comparación entre magnitudes distintas,
- medidas sensibles a escala.

### Cuándo usar MinMaxScaler
Cuando quieres llevar todo a un rango 0–1 y te interesa una interpretación más simple del rango.

---

## 6. Qué columnas suelen entrar y cuáles no
### Sí pueden entrar
Columnas numéricas continuas o cuantitativas con sentido analítico real.

Ejemplos:
- importes,
- puntuaciones,
- votos,
- popularidad,
- duración,
- llegadas,
- valores agregados,
- medidas continuas.

### No deben entrar sin pensar
Columnas numéricas que en realidad son identificadores o índices.

Ejemplos típicos:
- `id`,
- `line`,
- `episode`,
- `season`,
- claves artificiales,
- códigos sin valor cuantitativo real.

### Regla crítica
Si una columna es numérica pero su papel no es claramente analítico, el agente **no debe asumir automáticamente** que hay que normalizarla.

Debe consultarlo si hay duda.

---

## 7. Qué debe hacer si el dataset tiene pocas numéricas útiles
Si el dataset real de UD1 tiene pocas columnas numéricas claras, o si la única numérica continua útil es dudosa, el agente debe hacer esto:

1. identificar qué columnas numéricas existen realmente,
2. separar identificadores de variables continuas,
3. proponer qué columnas sí serían candidatas,
4. y **preguntar antes de decidir** si la elección no es obvia.

### Ejemplo de comportamiento correcto
“En UD1 este punto suele significar escalado de variables numéricas. En este dataset veo `season`, `episode`, `line` y `duration`, pero las tres primeras parecen identificadores/ordinales y la única continua clara sería `duration`. ¿Quieres que normalice esa columna o prefieres reservar este punto para otro dataset numérico tras integrar datos?”

### Ejemplo de comportamiento incorrecto
“Como normalización también puede significar limpieza textual, voy a pasar a minúsculas y quitar puntuación”.

---

## 8. Relación correcta entre apartados de UD1
### Punto 3 · Perfilado
Sirve para detectar:
- tipos,
- nulos,
- outliers,
- correlaciones,
- distribución,
- problemas de calidad.

### Punto 4 · Normalización
Sirve para:
- escalar numéricas,
- dejar magnitudes comparables,
- preparar análisis posteriores.

### Punto 5 · Validación
Sirve para:
- comprobar reglas,
- detectar valores inválidos,
- contrastar rangos, formatos y coherencia.

### Regla dura
No mover limpieza textual o decisiones de validación al punto 4 solo porque “encajan más o menos”.

---

## 9. Bloques operativos mínimos para el punto 4

### 9.1. Detectar columnas numéricas
```python
num_cols = df.select_dtypes(include=['number']).columns.tolist()
print(num_cols)
```

### 9.2. Separar identificadores de variables numéricas útiles
```python
num_cols = [c for c in num_cols if c not in ['id', 'line', 'season', 'episode']]
print(num_cols)
```

### 9.3. StandardScaler
```python
from sklearn.preprocessing import StandardScaler
import pandas as pd

num_cols = ['col_num_1', 'col_num_2']   # adaptar
scaler = StandardScaler()
scaled = scaler.fit_transform(df[num_cols])

scaled_df = pd.DataFrame(scaled, columns=num_cols)
print(scaled_df.head())
print(scaled_df.mean())
print(scaled_df.std())
```

### 9.4. MinMaxScaler
```python
from sklearn.preprocessing import MinMaxScaler
import pandas as pd

num_cols = ['col_num_1', 'col_num_2']   # adaptar
scaler = MinMaxScaler()
scaled = scaler.fit_transform(df[num_cols])

scaled_df = pd.DataFrame(scaled, columns=num_cols)
print(scaled_df.head())
print(scaled_df.min())
print(scaled_df.max())
```

---

## 10. Qué debe comprobar el agente antes de ejecutar
Antes de proponer el bloque del punto 4 debe comprobar:

- qué columnas son realmente numéricas,
- cuáles son continuas y cuáles identificadores,
- si hay nulos,
- si el escalado tiene sentido con ese dataset,
- y si la interpretación del apartado coincide con UD1.

Si cualquiera de estos puntos es dudoso, debe parar y consultarlo.

---

## 11. Qué no puede hacer el agente en UD1
### Prohibido
- reinterpretar “normalización” como limpieza textual por su cuenta,
- decidir que una columna ordinal o identificadora se escala sin justificarlo,
- saltar del perfilado a una fase distinta usando teoría genérica,
- usar el punto 4 para hacer algo cómodo si no encaja con el patrón de UD1,
- ignorar tareas resueltas y cuaderno oficial cuando fijan claramente el patrón.

---

## 12. Regla de adaptación al dataset real
Este contexto no obliga a usar siempre las mismas columnas.

Sí obliga a mantener:

- el significado correcto del apartado,
- la secuencia lógica del enunciado,
- y el patrón operativo real de UD1.

El agente debe adaptar:
- nombres de columnas,
- DataFrame concreto,
- columnas numéricas útiles,
- elección de escalador,
- y validación posterior.

Pero **no** debe cambiar el significado del punto.

---

## 13. Señales de que el punto 4 está bien resuelto
El apartado 4 está bien resuelto si:

- se identifican columnas numéricas candidatas,
- se explica por qué entran o por qué se excluyen otras,
- se aplica `StandardScaler` o `MinMaxScaler`,
- se muestra evidencia visible (`head`, media/std o min/max),
- y el resultado queda conectado con el flujo posterior.

---

## 14. Señales de que el agente se está equivocando
El agente está fallando si:

- habla de “normalización” como limpieza textual,
- no usa ningún escalador,
- no distingue identificadores de variables continuas,
- usa teoría general para corregir el enunciado,
- o no consulta cuando el dataset no encaja limpiamente en el patrón habitual.

---

## 15. Criterio final
En UD1, el agente debe actuar así:

- primero interpretar el enunciado según el patrón real de la unidad,
- después detectar qué columnas numéricas tiene sentido normalizar,
- luego proponer un bloque mínimo de escalado,
- y consultar si hay ambigüedad.

## Regla final
**En UD1, punto 4 = normalización numérica por defecto.**  
Si el agente quiere hacer otra cosa, debe justificarlo y preguntarlo antes.
