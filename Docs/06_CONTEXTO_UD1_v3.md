# CONTEXTO_UD1 · Gestión de soluciones Big Data · ETL completo

> **Versión:** v3
> **Cómo usar este documento:** pégalo en una celda markdown del cuaderno. Quédate en el cuaderno como referencia permanente para el agente. **No la borres ni la escondas.**

> **Cambios respecto a v2:**
> - Eliminada la sección "Qué evalúa esta unidad" con requisitos del enunciado concreto. Esos requisitos viven ahora en la celda PLAN_TAREA, no aquí.
> - Eliminada la sección 9 "Reglas específicas del dataset". Esa información también vive ahora en PLAN_TAREA.
> - Este documento queda con **reglas conceptuales fijas de UD1**, reutilizables para cualquier enunciado de tarea o examen.

---

## ÍNDICE

1. [Qué se evalúa en UD1](#1-qué-se-evalúa-en-ud1)
2. [Qué manda en caso de duda](#2-qué-manda-en-caso-de-duda)
3. [Mapa conceptual de los apartados típicos de UD1](#3-mapa-conceptual-de-los-apartados-típicos-de-ud1)
4. [Reglas semánticas críticas por apartado](#4-reglas-semánticas-críticas-por-apartado)
5. [Decisiones que el agente NO puede tomar solo](#5-decisiones-que-el-agente-no-puede-tomar-solo)
6. [Bloques operativos mínimos de referencia](#6-bloques-operativos-mínimos-de-referencia)
7. [Señales de que un apartado está bien resuelto](#7-señales-de-que-un-apartado-está-bien-resuelto)
8. [Señales de que el agente se está equivocando](#8-señales-de-que-el-agente-se-está-equivocando)

> **Nota:** los detalles concretos del enunciado actual (datasets a usar, claves de integración, requisitos específicos del profesor, mapa apartado↔unidad) están en la celda PLAN_TAREA del cuaderno, no aquí.

---

## 1. Qué se evalúa en UD1

UD1 evalúa un **proceso ETL completo** en Google Colab: extracción, transformación y carga de datos, con técnicas que cubren limpieza, normalización, integración, reducción, validación cruzada, hipótesis estadísticas y carga a NoSQL.

Los apartados típicos en una tarea de UD1 son:
explicación del dataset, carga, perfilado, normalización numérica, validación, integración con otro dataset, agregación, reducción de dimensionalidad, validación cruzada, testeo de hipótesis, detección de anomalías, importación a NoSQL.

**No todo enunciado pedirá los 12.** El usuario debe identificar qué apartados pertenecen a UD1 en el enunciado concreto (esto se anota en la celda PLAN_TAREA).

---

## 2. Qué manda en caso de duda

Cuando haya conflicto entre fuentes, sigue este orden:

1. **enunciado oficial** del ejercicio actual,
2. **PLAN_TAREA** (lectura concreta del enunciado y estado de avance),
3. **este contexto de unidad** (reglas conceptuales de UD1),
4. **tareas resueltas y aprobadas** de compañeros como patrón,
5. cuadernos de ejemplo del profesor,
6. teoría y resumen de la unidad,
7. inferencia técnica general.

Si una definición genérica contradice el patrón operativo de UD1, **manda el patrón de UD1**.

---

## 3. Mapa conceptual de los apartados típicos de UD1

| Apartado típico | Bloque ETL | Lo crítico |
|---|---|---|
| Explicación del dataset | I. Configuración | Datasets relacionados, campos comunes, volumen |
| Carga de datos | I. Configuración | URL preferente (Kaggle / Drive) |
| Perfilado | II. Limpieza | head, info, describe, nulos, **al menos una visualización** |
| **Normalización numérica** | II. Limpieza | **Escalado con StandardScaler / MinMaxScaler** |
| Validación | II. Limpieza | Limpieza, reglas de validación, outliers evidentes |
| Integración y enriquecimiento | II. Limpieza | merge entre datasets + tercer dataset o API |
| Agregación | II. Limpieza | groupby + función estadística |
| Reducción | III. Avanzada | **PCA**, no eliminar columnas auxiliares |
| Validación cruzada | III. Avanzada | **cross_val_score con k-fold** sobre un modelo simple |
| Testeo de hipótesis | III. Avanzada | **ttest_ind o ANOVA**, interpretar p-valor vs 0.05 |
| Detección de anomalías | III. Avanzada | **IsolationForest** (preferente) o IQR |
| NoSQL | IV. Carga | MongoDB (`to_dict('records')`) o Neo4j conceptual |

> **Numeración de apartados en PLAN_TAREA:** los apartados anteriores son los **temas típicos** de UD1. Su numeración real (apartado 1, 2, 3...) depende del enunciado concreto y se registra en el "Mapa apartado ↔ unidad" de la celda PLAN_TAREA.

---

## 4. Reglas semánticas críticas por apartado

Estas son las interpretaciones obligatorias de UD1. Si el agente quiere desviarse de alguna, debe consultar antes.

### Apartado: Perfilado
**Significa:** explorar la calidad y distribución de los datos.

**Patrón típico:** head, info, describe, dtypes, nunique, isnull, duplicados.

**🔍 Riqueza requerida:** además de las funciones básicas, incluir **al menos una visualización** que aporte valor exploratorio (heatmap de correlación, boxplot, histograma de la variable objetivo). El perfilado sin visualización queda incompleto.

### Apartado: Normalización de datos 🚨 punto más sensible
**Significa:** **escalado de variables numéricas** con `StandardScaler` (estandarización a media 0, std 1) o `MinMaxScaler` (rango 0-1). El objetivo es que columnas con magnitudes muy diferentes no sesguen el análisis posterior.

**No es:**
- limpieza de strings (`.strip()`, `.lower()`),
- renombrar columnas,
- arreglar espacios en blanco,
- quitar puntuación,
- "normalizar texto" en sentido genérico.

Esas operaciones son válidas como parte del apartado de Validación, pero **no sustituyen** al apartado de Normalización.

**Patrón típico:**
1. detectar columnas numéricas,
2. separar identificadores (consultar PLAN_TAREA) de variables continuas con sentido analítico,
3. aplicar `StandardScaler` o `MinMaxScaler` a las continuas,
4. mostrar evidencia visible: `head()`, `mean()`, `std()` o `min()/max()`.

**🔍 Variante reforzada (recomendada para asegurar puntuación):** se pueden hacer **las dos** (estandarización + normalización min-max) en bloques separados, dando explicación distinta a cada una. Si hay varias variables numéricas continuas, escalarlas todas, no solo la variable objetivo.

### Apartado: Validación de datos
**Significa:** comprobar que los datos son consistentes y aplicar reglas de validación.

**Incluye:**
- transformaciones de limpieza menores (estandarizar formatos, comas vs puntos),
- definir reglas de validación con `lambda` o asserts (rangos válidos, no nulos en claves, formato),
- imprimir un resumen con ✅/❌ por cada regla,
- imputar o eliminar outliers evidentes solo si es necesario (consultar antes).

**No es:**
- el escalado numérico (eso es Normalización),
- normalización en sentido estadístico.

### Apartado: Integración y enriquecimiento
**Significa:** dos cosas distintas en el mismo apartado.

- **Integración:** unir los **dos datasets principales** con `pd.merge` o `join` por la clave común identificada en PLAN_TAREA.
- **Enriquecimiento:** añadir información de un **tercer dataset** o de una **API externa** al DataFrame ya integrado.

**Si solo integras sin enriquecer, te falta media puntuación.** Si la clave de unión necesita preparación previa (renombrar columnas, calcular un campo derivado), eso entra aquí o se puede hacer en validación.

### Apartado: Reducción de datos
**Significa:** condensar información de **muchas variables en pocas** mediante una técnica algorítmica de reducción de dimensionalidad.

**Técnica preferente:** `PCA` (`sklearn.decomposition.PCA`) reduciendo a 2 componentes para poder visualizarlas en scatter plot.

**No es:**
- eliminar columnas auxiliares,
- borrar duplicados,
- hacer `drop()` sobre columnas con muchos nulos.

Esas son limpiezas menores que pueden estar en Validación, pero **no constituyen reducción** en el sentido del enunciado.

**🔍 Granularidad correcta:** PCA debe aplicarse al **nivel agregado** (una fila por episodio, por usuario, por unidad de análisis), no al dataset principal de millones de líneas. Aplicarlo a un dataset masivo cuando la pregunta es sobre la unidad agregada produce un PCA trivial. La granularidad concreta está fijada en PLAN_TAREA.

**🔍 Riqueza requerida:** el scatter plot de los 2 componentes debe estar **coloreado por la variable objetivo** cuando exista, no monocromático. Esto permite interpretar la separación visual.

**Patrón típico:**
- aplicar `StandardScaler` previo,
- imputar nulos si los hay,
- aplicar PCA a 2 componentes,
- mostrar varianza explicada por cada componente y total,
- scatter plot coloreado por variable objetivo.

### Apartado: Validación cruzada
**Significa:** **k-fold cross-validation** sobre un modelo simple usando `sklearn.model_selection.cross_val_score`.

**Patrón típico:**
- elegir un modelo simple (`DecisionTreeRegressor` o `DecisionTreeClassifier`),
- aplicar `cross_val_score` con `cv=10` (k-fold estándar),
- usar `scoring='neg_mean_squared_error'` para regresión o `'accuracy'` para clasificación,
- mostrar el MSE/accuracy de cada fold + media + desviación estándar,
- interpretar si el modelo es estable o varía mucho entre folds.

**No es:**
- comparar consistencia entre dos fuentes de datos,
- "validar" la integridad del merge,
- verificar que dos cálculos coinciden.

Esa interpretación de "validación cruzada como verificación cruzada" es **incorrecta** en UD1.

### Apartado: Testeo de hipótesis
**Significa:** prueba estadística con `scipy.stats` para comparar grupos o validar suposiciones.

**🔍 Granularidad correcta:** la prueba estadística debe aplicarse al **nivel de la pregunta**, no al dataset principal de líneas. Si la hipótesis es sobre ratings entre temporadas, comparar los ratings agregados por episodio (8-9 valores por temporada), NO los miles de líneas que comparten el mismo Rating.

**Por qué importa:** aplicar t-test sobre miles de observaciones que comparten valor produce p-valores artificialmente bajos (rechazo trivial por inflación de tamaño muestral). El resultado parece significativo pero no responde a la pregunta real.

**Patrón típico:**
- formular hipótesis nula (H0) y alternativa (H1),
- agregar al nivel correcto si hace falta,
- elegir test: `ttest_ind` para 2 grupos, `f_oneway` (ANOVA) para 3+ grupos,
- fijar `alpha = 0.05`,
- calcular estadístico y p-valor,
- interpretar: si p < 0.05 → rechazo H0 (diferencia significativa); si no → no hay evidencia.

### Apartado: Detección de anomalías
**Significa:** identificar puntos atípicos con un algoritmo dedicado.

**Técnica preferente del profesor:** `IsolationForest` de `sklearn.ensemble`.
**Alternativa válida:** método del IQR (`Q1 - 1.5*IQR`, `Q3 + 1.5*IQR`).

**🔍 Granularidad correcta:** las anomalías deben buscarse al **nivel agregado**, no al nivel del dataset principal. Si lo aplicas a líneas, todas las líneas del mismo episodio comparten el mismo valor y se detectan en bloque, lo cual no es información nueva.

**Patrón típico:**
- agregar al nivel correcto si hace falta,
- aplicar el algoritmo sobre 2-3 variables informativas,
- añadir columna de predicción (1 normal, -1 anomalía),
- mostrar cuántas anomalías y **cuáles son** (identificarlas, no solo contarlas),
- visualizar con scatter plot diferenciando por color/marcador.

### Apartado: Importación a NoSQL
**Significa:** explicar el **proceso conceptual** de cargar el DataFrame final en MongoDB o Neo4j. **No se exige conexión real**.

**MongoDB:**
- transformar con `df.to_dict(orient='records')`,
- mostrar el código conceptual de inserción con `pymongo`,
- comentar la estructura documental.

**Neo4j:**
- explicar la transformación a nodos y relaciones,
- mostrar el formato de exportación (CSV/JSON),
- comentar conceptualmente el proceso con Cypher.

---

## 5. Decisiones que el agente NO puede tomar solo

Detente y consulta antes de:

### Sobre Normalización
- elegir entre `StandardScaler` y `MinMaxScaler` si el dataset admite ambos,
- decidir si hacer las dos (variante reforzada) o solo una,
- decidir si una columna ordinal/identificador entra como numérica para escalar,
- decidir cuántas variables numéricas escalar.

### Sobre Validación
- imputar o eliminar nulos,
- borrar filas detectadas como problemáticas.

### Sobre Integración
- elegir clave de unión si hay varias,
- elegir tipo de merge (`inner`, `left`, `outer`),
- decidir qué tercer dataset o API usar para enriquecer.

### Sobre Reducción
- decidir nivel de granularidad (agregado vs principal),
- decidir cuántos componentes principales mantener,
- decidir si imputar nulos antes de PCA.

### Sobre Hipótesis
- formular la hipótesis a contrastar,
- elegir nivel de granularidad (agregado vs líneas),
- elegir entre t-test y ANOVA.

### Sobre Anomalías
- decidir nivel de granularidad,
- decidir variables sobre las que aplicar el algoritmo.

### Estrategia general
- saltarse un apartado,
- resolver dos apartados en el mismo bloque,
- sustituir una técnica algorítmica por una limpieza simple "porque parecía cumplir lo mismo".

---

## 6. Bloques operativos mínimos de referencia

Estos son patrones de código que el agente debe **adaptar al dataset real** (consultando PLAN_TAREA), no copiar a ciegas.

### 6.1 Detectar columnas numéricas
```python
num_cols = df.select_dtypes(include=['number']).columns.tolist()
print(num_cols)
```

### 6.2 StandardScaler
```python
from sklearn.preprocessing import StandardScaler
import pandas as pd

num_cols = ['col_continua_1', 'col_continua_2']  # adaptar (consultar PLAN_TAREA)
scaler = StandardScaler()
scaled = scaler.fit_transform(df[num_cols])
scaled_df = pd.DataFrame(scaled, columns=num_cols)

print(scaled_df.head())
print("\nMedia:", scaled_df.mean().to_dict())
print("Std:",   scaled_df.std().to_dict())
```

### 6.3 MinMaxScaler (alternativa o complemento)
```python
from sklearn.preprocessing import MinMaxScaler
scaler = MinMaxScaler()
norm = pd.DataFrame(scaler.fit_transform(df[num_cols]), columns=num_cols)
print(norm.head())
print("\nMin:", norm.min().to_dict())
print("Max:", norm.max().to_dict())
```

### 6.4 Reglas de validación con resumen ✅/❌
```python
reglas = [
    ("No nulos en clave", lambda d: d['clave'].isnull().sum() == 0),
    ("Variable objetivo en rango válido", lambda d: d[(d['target'].notna()) & ((d['target']<MIN)|(d['target']>MAX))].empty),
]
for nombre, check in reglas:
    print(f"{'✅' if check(df) else '❌'} {nombre}")
```

### 6.5 Merge entre dos datasets
```python
df_merged = pd.merge(df_principal, df_secundario, on=['campo_comun'], how='left')
print("Antes:", df_principal.shape, "Después:", df_merged.shape)
print("Nulos en columna nueva:", df_merged['col_nueva'].isnull().sum())
```

### 6.6 Agregación al nivel correcto
```python
df_agg = df_final.groupby('clave_agregacion').agg(
    metrica_1=('col1', 'mean'),
    metrica_2=('col2', 'count'),
).reset_index()
print(df_agg.head())
```

### 6.7 PCA (sobre datos agregados)
```python
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler
import seaborn as sns
import matplotlib.pyplot as plt

X = df_agg[num_cols].dropna()
X_scaled = StandardScaler().fit_transform(X)
pca = PCA(n_components=2)
pcs = pca.fit_transform(X_scaled)

print(f"Varianza explicada: PC1={pca.explained_variance_ratio_[0]*100:.2f}% "
      f"PC2={pca.explained_variance_ratio_[1]*100:.2f}%")
print(f"Total: {pca.explained_variance_ratio_.sum()*100:.2f}%")

df_pcs = pd.DataFrame(pcs, columns=['PC1', 'PC2'])
df_pcs['target'] = df_agg['variable_objetivo'].reset_index(drop=True)
sns.scatterplot(data=df_pcs, x='PC1', y='PC2', hue='target', palette='viridis')
plt.show()
```

### 6.8 Validación cruzada k-fold
```python
from sklearn.model_selection import cross_val_score
from sklearn.tree import DecisionTreeRegressor
import numpy as np

modelo = DecisionTreeRegressor(random_state=42)
scores = cross_val_score(modelo, X, y, cv=10, scoring='neg_mean_squared_error')
mse = -scores
print(f"MSE medio: {mse.mean():.4f}  ±  {mse.std():.4f}")
```

### 6.9 Test de hipótesis (ANOVA, sobre datos agregados)
```python
from scipy.stats import f_oneway

# IMPORTANTE: agrupar al nivel correcto, no al dataset principal
grupo_1 = df_agg[df_agg['categoria'] == 1]['metrica']
grupo_2 = df_agg[df_agg['categoria'] == 2]['metrica']
grupo_3 = df_agg[df_agg['categoria'] == 3]['metrica']

f_stat, p_value = f_oneway(grupo_1, grupo_2, grupo_3)
alpha = 0.05
print(f"F={f_stat:.4f}  p={p_value:.4f}")
print("Rechazo H0" if p_value < alpha else "No hay evidencia para rechazar H0")
```

### 6.10 IsolationForest (sobre datos agregados)
```python
from sklearn.ensemble import IsolationForest

# Sobre el agregado, no sobre el principal
iso = IsolationForest(contamination=0.05, random_state=42)
df_agg['anomaly'] = iso.fit_predict(df_agg[num_cols])
print(f"Anomalías detectadas: {(df_agg['anomaly']==-1).sum()} de {len(df_agg)}")
print(df_agg[df_agg['anomaly']==-1])  # mostrar cuáles son
```

### 6.11 MongoDB (conceptual)
```python
docs = df_final.to_dict(orient='records')
print(f"Documentos preparados: {len(docs)}")
print("Ejemplo:", docs[0])

# Inserción conceptual (no se ejecuta sin conexión):
# from pymongo import MongoClient
# client = MongoClient('mongodb://localhost:27017/')
# db = client['mi_bd']
# db['mi_coleccion'].insert_many(docs)
```

---

## 7. Señales de que un apartado está bien resuelto

- El apartado **Perfilado** incluye al menos una visualización (heatmap, boxplot, histograma).
- El apartado **Normalización** usa `StandardScaler` o `MinMaxScaler` (o ambos) sobre **todas las columnas continuas relevantes**, no solo una.
- El apartado **Integración** une **al menos dos datasets** y enriquece con uno tercero o una API.
- El apartado **Reducción** usa `PCA` sobre datos agregados, muestra varianza explicada y scatter coloreado por la variable objetivo.
- El apartado **Validación cruzada** usa `cross_val_score` con `cv >= 5`.
- El apartado **Hipótesis** usa el nivel de granularidad correcto (agregado, no principal) y muestra p-valor + interpretación frente a alpha.
- El apartado **Anomalías** usa `IsolationForest` o IQR sobre datos agregados, identifica las anomalías concretas (no solo cuenta).
- Cada apartado tiene **comprobación visible** por pantalla (no bloques ciegos).
- La conclusión de cada apartado termina con un **dato concreto del resultado**.

---

## 8. Señales de que el agente se está equivocando

- En **Perfilado**, no incluye ninguna visualización → falta media puntuación.
- En **Normalización**, está haciendo `.strip()` o `.lower()` en lugar de escalar números → **fallo grave, no cuenta como normalización**.
- En **Normalización**, escala solo una variable cuando hay varias continuas relevantes → puntuación reducida.
- En **Integración**, integra pero **no enriquece** con un tercer dataset/API → falta media puntuación.
- En **Reducción**, está haciendo `df.drop(columns=[...])` y llamándolo "reducción" → **no es reducción**, eso es limpieza.
- En **Reducción**, aplica PCA al dataset principal de millones de filas en lugar de al agregado → análisis trivial.
- En **Validación cruzada**, está comparando consistencia entre dos cálculos en lugar de hacer k-fold → **no es validación cruzada**.
- En **Hipótesis**, aplica el test sobre el dataset principal en lugar del agregado → p-valor artificialmente bajo, rechazo trivial.
- En **Anomalías**, dice que detecta anomalías "porque están fuera del rango lógico" sin aplicar algoritmo → falta el algoritmo.
- En **Anomalías**, aplica el algoritmo al dataset principal y detecta cientos de "anomalías" que son líneas del mismo episodio → falsa precisión.
- Está copiando la solución de un compañero literal con sus columnas sin adaptar al dataset propio (consultar PLAN_TAREA).
- Está saltando del apartado X al X+2 sin avisar, o agrupando dos apartados en un mismo bloque.
- Conclusiones que terminan con frases tipo *"asegurando la consistencia..."*, *"garantizando la integridad..."* (eso lo prohíbe la guía de estilo).
