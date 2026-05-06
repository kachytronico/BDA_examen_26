# Ejemplo de tarea en Colab

## Objetivo

Crear una celda reutilizable para cargar un CSV, revisar su estructura y detectar problemas iniciales antes de entrenar cualquier modelo.

## Prompt

```text
Estoy en Google Colab y quiero preparar un bloque inicial para revisar un dataset CSV.

Necesito código para:
1. subir un archivo CSV o leerlo desde una ruta;
2. cargarlo con pandas;
3. mostrar las primeras filas;
4. revisar tipos, nulos y duplicados;
5. separar columnas numéricas y categóricas;
6. dejar una breve explicación Markdown para el notebook.

El código debe ser simple, reutilizable y fácil de adaptar.
No inventes nombres de columnas.
```

## Código base revisado

```python
import pandas as pd

ruta_csv = '/content/dataset.csv'  # Cambiar por la ruta real
df = pd.read_csv(ruta_csv)

print('Dimensiones:', df.shape)
display(df.head())

print('\nInformación general:')
df.info()

print('\nValores nulos por columna:')
print(df.isnull().sum().sort_values(ascending=False))

print('\nDuplicados exactos:', df.duplicated().sum())

columnas_numericas = df.select_dtypes(include='number').columns.tolist()
columnas_categoricas = df.select_dtypes(include='object').columns.tolist()

print('\nColumnas numéricas:', columnas_numericas)
print('Columnas categóricas:', columnas_categoricas)
```

## Qué revisar

- Si la ruta del CSV es correcta.
- Si el separador del archivo no es coma (`sep=';'`, por ejemplo).
- Si hay columnas con demasiados nulos.
- Si hay duplicados reales o registros repetidos válidos.
- Si las columnas numéricas están correctamente detectadas.

## Uso profesional

Este bloque es útil como primera celda técnica en notebooks de IA, ML o Big Data porque evita empezar a modelar sin conocer antes la calidad básica de los datos.