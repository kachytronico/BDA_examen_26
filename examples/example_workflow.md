# Ejemplo de flujo de trabajo

Este ejemplo muestra cómo usar un asistente de IA para mejorar un notebook de Google Colab sin delegar todo el criterio técnico.

## 1. Tarea inicial

Quiero crear una sección de análisis exploratorio de datos para un CSV que voy a usar en un notebook de IA.

## 2. Prompt usado

```text
Estoy trabajando en Google Colab con un CSV cargado en un DataFrame llamado df.

Quiero crear un bloque inicial de análisis exploratorio que me permita entender:
- tamaño del dataset;
- columnas disponibles;
- tipos de datos;
- valores nulos;
- duplicados;
- variables numéricas y categóricas.

Dame código sencillo para Colab y una breve explicación de qué debo mirar en cada salida.
No inventes columnas concretas.
```

## 3. Respuesta esperada

El asistente puede proponer código de perfilado general:

```python
print(df.shape)
display(df.head())
print(df.info())
print(df.isnull().sum())
print('Duplicados:', df.duplicated().sum())

numeric_cols = df.select_dtypes(include='number').columns
categorical_cols = df.select_dtypes(include='object').columns

print('Numéricas:', list(numeric_cols))
print('Categóricas:', list(categorical_cols))
```

## 4. Revisión humana

Antes de dejar este bloque en el notebook, conviene comprobar:

- si el DataFrame realmente se llama `df`;
- si hay columnas con nombres sensibles;
- si las salidas son demasiado largas;
- si hace falta explicar alguna variable clave;
- si el análisis responde al objetivo del notebook.

## 5. Resultado final

El código se incorpora solo después de probarlo en Colab y añadir una explicación Markdown que diga qué se observa y qué decisión se toma a partir de los datos.