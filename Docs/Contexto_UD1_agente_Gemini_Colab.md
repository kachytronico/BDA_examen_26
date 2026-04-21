
# CONTEXTO OPERATIVO UD1 PARA AGENTE GEMINI EN COLAB
## BDA · UD1 / RA1 · Gestión de soluciones Big Data

> Documento de contexto **para la parte de contenido de UD1**.  
> **No sustituye** a los documentos generales de control de comportamiento, guía de estilo del cuaderno, protocolo iterativo y control de decisiones.  
> Su función es decirle al agente **qué fuentes usar, qué patrones extraer, qué bloques son fiables y qué debe adaptar** cuando resuelva en Colab una variante de examen de UD1 con **otro dataset**.

---

## 0. Propósito exacto

Este documento sirve para guiar al agente cuando tenga que ayudar con la **parte de examen o tarea de la UD1**.

El agente debe entender que:

- el examen **no será copia literal** de una tarea resuelta,
- pero **sí seguirá la misma lógica general** de la tarea oficial,
- y por tanto debe reutilizar **patrones de resolución**, no soluciones cerradas.

El objetivo no es que el agente explique teoría decorativa, sino que use correctamente las fuentes de UD1 para ayudar a resolver un **ETL práctico en Colab** con dataset nuevo.

---

## 1. Qué debe asumir sobre la UD1

### 1.1. Qué se evalúa realmente
La UD1 evalúa la capacidad de construir un **proceso ETL completo en Google Colab** sobre varios datasets relacionados, aplicando limpieza, transformación, validación, integración, agregación, reducción y cierre en NoSQL.

### 1.2. Estructura oficial que manda
El agente debe organizar la ayuda de UD1 **siempre** según los 12 apartados oficiales del enunciado:

1. Explicación del dataset  
2. Carga de datos  
3. Perfilado  
4. Normalización  
5. Validación  
6. Integración y enriquecimiento  
7. Agregación  
8. Reducción  
9. Validación cruzada  
10. Testeo de hipótesis  
11. Detección de anomalías  
12. Importación o visualización en MongoDB o Neo4j  

### 1.3. Idea clave para examen
El examen será previsiblemente **muy parecido a la tarea**, pero con **otro dataset**.  
Por tanto:

- **sí** debe reutilizar estructura, flujo y bloques mínimos,
- **no** debe copiar nombres de columnas, decisiones de limpieza, claves de unión, hipótesis u outliers sin adaptarlos al dataset real.

---

## 2. Jerarquía de fuentes obligatoria

Cuando el agente tenga que decidir **qué usar** para resolver UD1, debe seguir este orden:

1. **Enunciado oficial de la tarea UD1**  
2. **Tareas resueltas de UD1**  
3. **Notebook de evaluación bien estructurado / ejemplo trabajado**  
4. **Cuaderno del tutor 0112_TR_Limpieza_y_transformación_de_datos**  
5. **Cuadernos 0109–0111** solo si hace falta conversión de formato  
6. **Teoría oficial UD1 y resumen I1** solo para justificar o aclarar conceptos  

### Regla de prioridad
- Si algo aparece en **enunciado + tareas resueltas**, tratarlo como **muy prioritario**.
- Si algo aparece en un notebook ejemplo pero **no se ve en otras tareas**, tratarlo como **patrón útil pero no obligatorio**.
- Si algo aparece solo en **teoría**, tratarlo como **secundario** y usarlo solo si ayuda a justificar una decisión.
- Nunca inventar un paso importante que no tenga apoyo en estas fuentes.

---

## 3. Qué debe extraer de cada tipo de fuente

## 3.1. Del enunciado oficial
Debe extraer:

- el orden fijo de los 12 apartados,
- el hecho de que deben usarse **varios archivos relacionados**,
- la necesidad de hacer ETL completo en Colab,
- la exigencia de cierre en **MongoDB o Neo4j**,
- y el criterio de que la tarea/examen se valora por apartados.

**El enunciado manda sobre todo lo demás.**

---

## 3.2. De las tareas resueltas de UD1
Debe extraer:

- los **patrones que se repiten** aunque cambie el dataset,
- qué bloques aparecen de forma estable,
- qué nivel de complejidad es suficiente para una entrega buena,
- cómo se aterrizan realmente los 12 apartados.

Patrones especialmente importantes que se repiten:

- carga de varios ficheros,
- perfilado con `head`, `info`, `describe`, nulos y tipos,
- limpieza y normalización mínimas,
- validación con reglas simples,
- integración con `merge`,
- agregación con `groupby + agg`,
- algún bloque corto de contraste,
- anomalías simples,
- y cierre final a formato exportable o explicable para NoSQL.

---

## 3.3. Del notebook de evaluación bien estructurado
Debe usarlo como fuente para:

- detectar **bloques concretos reutilizables**,
- distinguir qué parte del flujo está bien secuenciada,
- y saber qué partes **no conviene copiar literal**.

### Lo que sí debe aprovechar
Del notebook ejemplo se consideran patrones útiles:

- carga de dos CSV,
- perfilado de ambos datasets,
- uso de `StandardScaler` sobre columnas numéricas,
- validación con reglas tipo diccionario + `lambda` + `for`,
- reducción con `drop` / `dropna`,
- integración por `merge`,
- exportación con `to_csv` y `files.download()`.

### Lo que no debe copiar literal
Hay partes del notebook que deben tratarse como **demostraciones de técnica**, no como solución fiel del examen:

- hipótesis con datos sintéticos generados con `numpy`,
- anomalías con datos aleatorios y LOF genérico,
- celdas con errores de copia o de lectura,
- bloques que reutilizan mal el CSV o sobreescriben cabeceras.

### Regla fuerte
El agente puede reutilizar la **idea** de esas celdas, pero debe **adaptarlas a columnas reales del dataset del examen**.

---

## 3.4. Del cuaderno 0112 del tutor
Debe extraer sobre todo:

- patrón general de limpieza y transformación,
- estilo de bloques mínimos,
- uso razonable de Pandas y Scikit-learn,
- ejemplos de perfilado, normalización, validación y outliers.

### Cómo usarlo
- como **fuente canónica de técnicas**,
- no como plantilla a copiar entera,
- y siempre subordinado al enunciado y a la lógica real de la tarea.

---

## 3.5. De los cuadernos 0109–0111
Solo deben usarse si el examen o el dataset obligan a trabajar con:

- JSON → CSV,
- JSON → XML,
- XML → CSV.

### Regla
No meter conversiones de formato por inercia.  
Solo usar estos cuadernos si el dataset del examen realmente lo exige.

---

## 3.6. De la teoría oficial y del resumen
Solo debe extraer:

- justificación mínima de ETL,
- diferencia básica entre formatos,
- apoyo conceptual para NoSQL,
- explicación breve de por qué MongoDB o Neo4j tienen sentido.

### Regla
No convertir la ayuda de UD1 en un resumen teórico largo.

---

## 4. Qué debe considerar patrón confirmado de examen

Tratar como **patrones confirmados o muy probables**:

- uso de **varios datasets relacionados**,
- lectura en Colab de CSV o formatos parecidos,
- perfilado inicial obligatorio,
- limpieza de texto, fechas, nulos y tipos,
- validación explícita,
- integración mediante `merge` o equivalente,
- agregación para producir una tabla resumen útil,
- reducción sencilla o PCA si aporta,
- validación cruzada o contraste simple,
- hipótesis cortas y justificables,
- detección de anomalías con método sencillo,
- cierre documental o relacional para NoSQL.

### Lo más rentable para examen
Priorizar siempre primero:

1. carga  
2. perfilado  
3. limpieza / normalización  
4. validación  
5. integración  
6. agregación  
7. exportación / salida final  

Luego, si hace falta completar o reforzar:

8. reducción  
9. validación cruzada  
10. hipótesis  
11. anomalías  

---

## 5. Qué bloques debe considerar fiables y reutilizables

## 5.1. Carga
```python
import pandas as pd
df1 = pd.read_csv("archivo1.csv")
df2 = pd.read_csv("archivo2.csv")
```

Uso:
- cuando el dataset viene en CSV y hay varios ficheros relacionados.

---

## 5.2. Perfilado
```python
print(df1.head())
print(df1.info())
print(df1.isnull().sum())
print(df1.columns.tolist())
print(df1.shape)
```

Uso:
- siempre justo después de cargar.

---

## 5.3. Limpieza / normalización básica
```python
df.columns = df.columns.str.strip()
df["texto"] = df["texto"].astype(str).str.strip().str.lower()
df["fecha"] = pd.to_datetime(df["fecha"], errors="coerce")
```

Uso:
- para estandarizar nombres, texto y fechas.

---

## 5.4. Escalado de columnas numéricas
```python
from sklearn.preprocessing import StandardScaler

cols = ["col1", "col2"]
scaler = StandardScaler()
scaled = scaler.fit_transform(df[cols])
```

Regla:
- escalar solo columnas numéricas que de verdad lo necesiten.

---

## 5.5. Validación por reglas
```python
rules = {
    "col1": lambda x: x > 0,
    "col2": lambda x: x in ["A", "B"]
}

for col, rule in rules.items():
    invalid_rows = df[~df[col].apply(rule)].index
    if len(invalid_rows) > 0:
        df.loc[invalid_rows, col] = None
```

Uso:
- como patrón fuerte de examen.
- especialmente útil cuando hay reglas simples y defendibles.

---

## 5.6. Integración
```python
df_merge = pd.merge(df1, df2, on="id", how="left")
```

Regla:
- el agente debe explicar siempre por qué usa `left`, `inner` u otro tipo.

---

## 5.7. Agregación
```python
resumen = (
    df.groupby(["grupo"])
      .agg(media=("valor", "mean"),
           total=("valor", "sum"),
           n=("valor", "count"))
      .reset_index()
)
```

Uso:
- para resumir por categoría, país, episodio, año, etc.

---

## 5.8. Reducción
```python
df_red = df[["col1", "col2", "col3"]].copy()
```

O, si aporta de verdad:

```python
from sklearn.preprocessing import StandardScaler
from sklearn.decomposition import PCA

X = df[["col1", "col2", "col3"]]
X_scaled = StandardScaler().fit_transform(X)
X_pca = PCA(n_components=2).fit_transform(X_scaled)
```

Regla:
- no usar PCA si basta con seleccionar columnas.

---

## 5.9. Validación cruzada / contraste
```python
from sklearn.model_selection import cross_val_score
from sklearn.tree import DecisionTreeClassifier

modelo = DecisionTreeClassifier()
scores = cross_val_score(modelo, X, y, cv=5)
print(scores.mean())
```

Regla:
- usarlo como comprobación simple, no como modelado complejo.

---

## 5.10. Hipótesis
Preferir hipótesis **basadas en datos reales del dataset**:

```python
from scipy import stats

grupo1 = df[df["tipo"] == "A"]["valor"].dropna()
grupo2 = df[df["tipo"] == "B"]["valor"].dropna()

t_stat, p_val = stats.ttest_ind(grupo1, grupo2, equal_var=False)
print(t_stat, p_val)
```

También son válidos:

- `pearsonr` para correlación,
- `f_oneway` para ANOVA.

### Regla
No usar muestras sintéticas si el dataset permite formular una hipótesis real.

---

## 5.11. Anomalías
```python
from sklearn.neighbors import LocalOutlierFactor

X = df[["col1", "col2"]].dropna().values
lof = LocalOutlierFactor()
y_pred = lof.fit_predict(X)
```

### Regla
Elegir columnas numéricas con sentido conjunto.  
No copiar ejemplos sintéticos si se puede aplicar a datos reales.

---

## 5.12. Exportación final
```python
df_final.to_csv("salida.csv", index=False)
df_final.to_json("salida.json", orient="records", force_ascii=False)
records = df_final.to_dict(orient="records")
```

### MongoDB base
```python
# from pymongo import MongoClient
# client = MongoClient("mongodb://localhost:27017/")
# db = client["bda"]
# collection = db["dataset"]
# collection.insert_many(records)
```

### Neo4j base
```python
nodos = df_final[["id", "nombre"]].drop_duplicates()
relaciones = df_final[["origen", "destino", "tipo_rel"]]
```

### Regla
- si el resultado es documental o tabular, preferir **MongoDB**;
- si el valor está en relaciones claras, plantear **Neo4j**.

---

## 6. Qué NO debe hacer el agente en UD1

No debe:

- copiar un notebook entero,
- asumir que las mismas columnas del ejemplo existirán en el examen,
- reutilizar hipótesis o anomalías con datos sintéticos si ya tiene datos reales,
- meter conversiones JSON/XML si el examen no las necesita,
- elevar demasiado la complejidad,
- usar teoría como relleno,
- ni tomar decisiones fuertes basándose solo en teoría cuando las tareas muestran otro patrón.

---

## 7. Reglas de adaptación al dataset nuevo

Cuando el dataset del examen sea distinto, el agente debe adaptar como mínimo:

- nombres de archivos,
- nombres de columnas,
- tipos de datos,
- clave o claves de unión,
- reglas de validación,
- agrupaciones y métricas,
- hipótesis concreta,
- variables para anomalías,
- y modelo de salida final.

### Regla práctica
Reutilizar siempre el **patrón**, no la literalidad.

Ejemplos:
- reutilizar `merge`, pero no asumir que la clave será `id`;
- reutilizar `StandardScaler`, pero no asumir qué columnas serán numéricas;
- reutilizar `ttest_ind`, pero no asumir qué grupos comparar.

---

## 8. Decisiones de examen que debe guiar

El agente debe prestar atención especial a estas decisiones:

### 8.1. Qué mirar primero
1. columnas  
2. tamaño  
3. nulos  
4. tipos  
5. clave de unión probable  

### 8.2. Qué resolver antes
1. carga  
2. perfilado  
3. limpieza / normalización  
4. validación  
5. integración  
6. agregación  

### 8.3. Qué bloques son casi obligatorios
- carga con `read_csv` o equivalente,
- `head` / `info`,
- nulos,
- limpieza básica,
- `merge` si hay varios ficheros,
- `groupby` si el ejercicio pide resumen,
- salida final o explicación del destino NoSQL.

### 8.4. Qué suele cambiar según dataset
- nombres de columnas,
- clave de unión,
- filtros,
- métricas,
- hipótesis,
- outliers,
- si conviene MongoDB o Neo4j.

---

## 9. Qué debe vigilar del notebook ejemplo

Del notebook de evaluación bien estructurado debe recordar estas advertencias:

- hay bloques muy útiles,
- pero no conviene copiarlo ciegamente,
- porque contiene celdas de demostración genérica y algunos detalles mejorables.

Advertencias concretas:
- `drop(...)` sin reasignación no elimina realmente la columna,
- `read_csv(..., names=...)` puede romper cabeceras reales,
- una celda de visualización carga `creditsdf` desde `titles.csv` por error,
- hipótesis y anomalías del ejemplo usan datos sintéticos y deben adaptarse.

### Regla
Antes de reutilizar un bloque del notebook ejemplo, preguntarse:
- ¿trabaja con datos reales del dataset?
- ¿es patrón de examen o solo demostración técnica?
- ¿requiere adaptación obligatoria?

---

## 10. Cierre conceptual mínimo que sí debe conocer

Solo como apoyo, el agente debe recordar:

- UD1 gira alrededor de **ETL**: extracción, transformación y carga.
- La teoría oficial menciona como núcleo de limpieza y transformación:
  - perfilado,
  - normalización,
  - enriquecimiento,
  - validación,
  - agregación,
  - reducción,
  - integración.
- MongoDB encaja mejor con salida documental.
- Neo4j encaja mejor con estructura de nodos y relaciones.

Esto debe usarse para **justificar**, no para alargar respuestas.

---

## 11. Estado de confianza

### Confirmado por materiales
- estructura de 12 apartados del enunciado,
- lógica ETL completa,
- centralidad de carga, perfilado, normalización, validación, integración, agregación, reducción y análisis,
- existencia y prioridad del cuaderno 0112,
- patrones repetidos en tareas resueltas,
- cierre en MongoDB o Neo4j,
- y utilidad del notebook ejemplo para extraer bloques y advertencias.

### Inferido razonablemente
- que en examen compensa priorizar carga, perfilado, limpieza, integración, agregación y exportación antes que técnicas más decorativas,
- que MongoDB suele ser la salida más sencilla de defender si el resultado final es tabular o documental.

### Pendiente de revisión fina
- interior completo de todos los cuadernos 0109–0112,
- ejemplos más sólidos de cierre real a Neo4j.

---

## 12. Regla final

**UD1 debe tratarse como una tarea práctica de resolución secuencial con dataset nuevo, no como teoría suelta ni como copia literal de una tarea resuelta.**

Si el agente duda entre:
- explicar más teoría,
- copiar más código,
- o ayudar a adaptar el patrón al dataset real,

debe elegir siempre:

**adaptar el patrón al dataset real manteniendo el orden oficial de la tarea.**
