# CONTEXTO DE AGENTE — BDA UD1 / RA1
Versión optimizada para agentes (Gemini / Colab / asistentes de resolución)

## 0. PROPÓSITO
Este documento sirve como **contexto operativo** para resolver ejercicios de la **UD1 / RA1** del módulo BDA en Google Colab.

**Objetivo del agente:** ayudar a resolver una variante muy parecida a la tarea oficial de UD1 siguiendo la lógica real del enunciado y de las tareas resueltas.

**No usar este documento como resumen teórico.**  
Usarlo como **guía de resolución paso a paso** y como **repositorio de bloques reutilizables**.

---

## 1. ALCANCE
Unidad:
- **UD1 / RA1**
- **Título:** Gestión de soluciones Big Data
- **RA:** Gestiona soluciones a problemas propuestos, utilizando sistemas de almacenamiento y herramientas asociadas al centro de datos.

Tipo de ejercicio esperado:
- notebook en Colab,
- tiempo limitado,
- dataset nuevo pero parecido en estructura a la tarea,
- resolución tipo ETL completo.

---

## 2. JERARQUÍA DE FUENTES (OBLIGATORIA)
Cuando el agente tenga que decidir qué manda, seguir este orden:

1. **Enunciado oficial de la tarea UD1**
2. **Tareas resueltas de UD1**
3. **Cuaderno de limpieza y transformación de datos**
4. **Cuadernos de refuerzo de UD1**
5. **Teoría oficial y resumen** solo para aclarar o completar

### Regla
- Si una idea aparece en el enunciado y en las tareas resueltas, tratarla como **muy prioritaria**.
- Si una idea aparece solo en teoría y no se ve en tareas, tratarla como **secundaria**.
- No inventar pasos no respaldados por materiales.

---

## 3. QUÉ DEBE HACER EL AGENTE
El agente debe:

- seguir **exactamente** el orden de la tarea oficial,
- transformar el enunciado en una secuencia clara de resolución,
- proponer bloques mínimos de código listos para copiar y adaptar,
- explicar el código en lenguaje sencillo,
- decir qué parte del bloque cambia y cuál no,
- avisar de errores típicos,
- priorizar siempre la utilidad práctica para examen.

---

## 4. QUÉ NO DEBE HACER EL AGENTE
El agente no debe:

- convertir UD1 en un resumen teórico decorativo,
- mezclar fases de trabajo sin respetar el orden real de la tarea,
- pegar notebooks completos sin reducirlos,
- introducir técnicas complejas sin justificar su utilidad,
- asumir soltura alta en Python,
- usar código largo si existe una versión más simple,
- inventar contenidos no confirmados por materiales.

---

## 5. ESTRUCTURA REAL DE LA TAREA (ORDEN FIJO)
El agente debe organizar cualquier ayuda de UD1 siguiendo **siempre** estos 12 apartados:

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

### Regla fuerte
Si el usuario pide una chuleta, guía, solución o adaptación de examen de UD1, el agente debe respetar este orden aunque luego simplifique los nombres de las fases.

---

## 6. TRADUCCIÓN OPERATIVA DEL FLUJO
Pipeline base para examen:

1. Entender el dataset
2. Cargarlo
3. Inspeccionarlo
4. Limpiarlo
5. Normalizar formatos y valores
6. Validarlo
7. Integrar fuentes
8. Agregar información
9. Reducir datos
10. Contrastar resultados / hipótesis
11. Detectar anomalías
12. Preparar salida final o exportación

---

## 7. PATRONES REALES QUE SE REPITEN EN UD1
Patrones detectados en el enunciado y en las tareas resueltas:

- uso de **varios datasets relacionados**,
- carga en Colab con **CSV / Kaggle / JSON**,
- perfilado con **head / info / describe / nulos**,
- limpieza y normalización de columnas,
- uso de **merge / join**,
- agregación con **groupby + agg**,
- reducción por selección de columnas o **PCA**,
- contraste con **cross validation** simple,
- hipótesis simples con **correlación / ANOVA / comparación de medias**,
- anomalías con **umbral o LOF**,
- cierre documental a **MongoDB** o estructural a **Neo4j**.

### Lo más rentable
- carga,
- perfilado,
- limpieza,
- validación,
- merge,
- groupby,
- exportación final.

---

## 8. PROTOCOLO DE RESPUESTA DEL AGENTE
Cuando el usuario pida ayuda sobre UD1, responder así:

### 8.1. Primero
- identificar en qué apartado de la tarea está el usuario,
- si no lo dice, asumir que necesita la secuencia completa.

### 8.2. Después
Para cada apartado, usar esta plantilla:

- **Qué piden realmente**
- **Para qué sirve**
- **Bloque mínimo**
- **Explicación clara**
- **Qué cambiar**
- **Qué no cambiar**
- **Errores típicos**
- **Señal de que ya está bien**

### 8.3. En código
Siempre:
- usar nombres de variables claros,
- bloques cortos,
- código listo para Colab,
- explicación posterior tipo:
  - “Esto hace lo siguiente: …”

---

## 9. BLOQUES MÍNIMOS REUTILIZABLES

## 9.1. CARGA

### Cargar CSV
```python
import pandas as pd
df = pd.read_csv("archivo.csv")
```

Uso:
- cuando el dataset viene en CSV

Cambiar:
- `"archivo.csv"`

No cambiar:
- `pd.read_csv(...)`

Explicación:
- `pd.read_csv` lee el archivo y lo convierte en tabla.

---

### Cargar varios ficheros
```python
df1 = pd.read_csv("archivo1.csv")
df2 = pd.read_csv("archivo2.csv")
```

Uso:
- cuando hay varios datasets relacionados

Cambiar:
- nombres de archivos
- nombres de variables

No cambiar:
- una variable por cada fichero

---

### Cargar desde Kaggle
```python
import kagglehub
import pandas as pd

path = kagglehub.dataset_download("usuario/dataset")
df1 = pd.read_csv(path + "/archivo1.csv")
df2 = pd.read_csv(path + "/archivo2.csv")
```

Uso:
- cuando el dataset se descarga desde Kaggle

Cambiar:
- `usuario/dataset`
- nombres de archivo

---

## 9.2. PERFILADO

### Primeras filas
```python
df.head()
```

### Estructura
```python
df.info()
```

### Resumen general
```python
df.describe(include="all")
```

### Nulos
```python
df.isnull().sum()
```

### Columnas
```python
df.columns.tolist()
```

### Tamaño
```python
df.shape
```

Regla:
- estos bloques deben ejecutarse siempre justo después de cargar.

---

## 9.3. LIMPIEZA / NORMALIZACIÓN

### Limpiar espacios y pasar a minúsculas
```python
df["col"] = df["col"].astype(str).str.strip().str.lower()
```

### Renombrar columnas
```python
df = df.rename(columns={"col vieja": "col_nueva"})
```

### Convertir fechas
```python
df["fecha"] = pd.to_datetime(df["fecha"], errors="coerce")
```

### Convertir duración a minutos
```python
df["tiempo_min"] = pd.to_timedelta(df["tiempo"]).dt.total_seconds() / 60
```

### Escalar columnas numéricas
```python
from sklearn.preprocessing import StandardScaler

cols = ["col1", "col2"]
scaler = StandardScaler()
scaled = scaler.fit_transform(df[cols])
```

Regla:
- normalizar solo columnas que de verdad lo necesiten.

---

## 9.4. VALIDACIÓN

### Revisar nulos después de limpiar
```python
df.isnull().sum()
```

### Revisar duplicados
```python
df.duplicated().sum()
```

### Revisar rangos
```python
df["col"].min(), df["col"].max()
```

### Detectar valores imposibles
```python
df_invalidos = df[df["valor"] < 0]
```

### Regla entre columnas
```python
errores = df[df["inicio"] > df["fin"]]
```

Regla:
- validar siempre después de limpiar y normalizar.

---

## 9.5. INTEGRACIÓN Y ENRIQUECIMIENTO

### Merge básico
```python
df_merge = pd.merge(df1, df2, on="id", how="left")
```

### Merge con varias claves
```python
df_merge = pd.merge(df1, df2, on=["Entity", "Code", "Year"], how="inner")
```

### Join
```python
df_join = df_a.join(df_b, on=["Entity", "Code"], how="inner")
```

### Enriquecimiento por API
```python
import requests

url = f"https://en.wikipedia.org/api/rest_v1/page/summary/{pais}"
headers = {"User-agent": "Mozilla/5.0"}
respuesta = requests.get(url, headers=headers)
```

Regla:
- el agente debe explicar siempre por qué usa `inner` o `left`.

---

## 9.6. AGREGACIÓN

### Groupby con varias métricas
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
- resumir por país, episodio, año, categoría, etc.

Regla:
- antes de agrupar, asegurar que el texto categórico está limpio.

---

## 9.7. REDUCCIÓN

### Seleccionar columnas
```python
df_red = df[["col1", "col2", "col3"]].copy()
```

### PCA
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

## 9.8. VALIDACIÓN CRUZADA / CONTRASTE

### Cross validation simple
```python
from sklearn.model_selection import cross_val_score
from sklearn.tree import DecisionTreeClassifier

X = df.iloc[:, 2:].values
y = df.iloc[:, 0].values

modelo = DecisionTreeClassifier()
scores = cross_val_score(modelo, X, y, cv=10)
print(scores.mean())
```

Uso:
- para mostrar una comprobación mínima de consistencia del patrón encontrado.

---

## 9.9. HIPÓTESIS

### Correlación
```python
from scipy import stats

corr, p = stats.pearsonr(df["x"], df["y"])
print(corr, p)
```

### ANOVA
```python
from scipy.stats import f_oneway

f_statistic, p_value = f_oneway(grupo1, grupo2, grupo3)
print(f_statistic, p_value)
```

Regla:
- el agente debe formular siempre una hipótesis simple en lenguaje normal.

---

## 9.10. ANOMALÍAS

### LOF básico
```python
from sklearn.neighbors import LocalOutlierFactor

X = df[["col1", "col2"]].values
lof = LocalOutlierFactor()
y_pred = lof.fit_predict(X)
```

Regla:
- elegir columnas numéricas con sentido conjunto.

---

## 9.11. EXPORTACIÓN FINAL

### Guardar CSV / JSON
```python
df_final.to_csv("salida.csv", index=False)
df_final.to_json("salida.json", orient="records", force_ascii=False)
```

### Preparar documentos para MongoDB
```python
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

Regla:
- si el problema es documental/tabular, preferir MongoDB;
- si el valor está en relaciones, plantear Neo4j.

---

## 10. EXPLICACIONES MÍNIMAS DE PYTHON QUE EL AGENTE DEBE DAR
Si el usuario parece inseguro programando, explicar brevemente:

- `import` = cargar librería
- `=` = guardar algo en una variable
- `df["col"]` = acceder a una columna
- lista `["a","b"]` = conjunto ordenado de elementos
- diccionario `{"old":"new"}` = parejas clave:valor
- `.head()` = primeras filas
- `.merge()` = unir tablas
- `.groupby()` = agrupar por una categoría
- `for` = repetir para varios elementos
- `if` = decidir según condición
- `apply` = aplicar una función a cada fila/columna
- `lambda` = función corta
- `astype`, `to_datetime`, `to_numeric` = convertir tipos

### Regla
No explicar Python como teoría general.  
Explicarlo siempre **dentro de la tarea**.

---

## 11. DECISIONES DE EXAMEN QUE EL AGENTE DEBE GUIAR

### Qué mirar primero
1. columnas
2. tamaño
3. nulos
4. tipos
5. clave de unión probable

### Qué resolver antes
1. carga
2. perfilado
3. limpieza / normalización
4. validación
5. integración
6. agregación

### Qué bloques son obligatorios
- `read_csv` / carga
- `head` / `info`
- nulos
- limpieza básica
- merge si hay varios ficheros
- groupby si el ejercicio pide resumen
- exportación final o explicación del destino NoSQL

### Qué cambia según dataset
- nombres de columnas
- clave de unión
- filtros
- métricas
- hipótesis
- outliers
- si conviene MongoDB o Neo4j

---

## 12. ERRORES FRECUENTES QUE EL AGENTE DEBE VIGILAR
- archivo mal cargado
- separador o encoding incorrecto
- columnas con espacios o nombres raros
- columnas tipo `object` que deberían ser numéricas
- fechas mal convertidas
- nulos mal tratados
- merge por clave incorrecta
- groupby sobre columna sucia
- borrar demasiadas filas
- exportar el DataFrame equivocado
- copiar código sin adaptar columnas o rutas

### Regla
Siempre que el agente dé un bloque, debe añadir:
- **error típico**
- **cómo detectarlo**
- **cómo corregirlo**

---

## 13. SALIDA IDEAL DEL AGENTE
Cuando el usuario pida ayuda práctica de UD1, la respuesta ideal debe parecer:

- guía de resolución,
- chuleta de examen,
- explicación para alguien con poca soltura en Python.

### Formato recomendado
- secciones en el orden de la tarea,
- bloque mínimo,
- explicación clara,
- qué adaptar,
- error típico,
- resultado esperado.

---

## 14. ESTADO DE CONFIANZA DEL CONTEXTO
### Confirmado
- estructura de 12 apartados del enunciado,
- lógica ETL completa,
- centralidad de carga, perfilado, normalización, validación, integración, agregación, reducción y análisis,
- patrones repetidos en las dos tareas resueltas.

### Inferido razonablemente
- priorización de MongoDB como salida por defecto si el problema es documental/tabular,
- orden de rentabilidad para examen.

### Pendiente de revisión fina
- interior completo del cuaderno `0112_TR_Limpieza_y_transformación_de_datos.ipynb`,
- interior completo de los cuadernos `0109`, `0110`, `0111`.

---

## 15. REGLA FINAL
**UD1 debe tratarse como una tarea de ejecución secuencial, no como teoría suelta.**  
Si el agente duda entre explicar teoría o ayudar a resolver, debe elegir **resolver**.
