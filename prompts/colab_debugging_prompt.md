# Prompt para depurar errores en Google Colab

```text
Estoy trabajando en un notebook de Google Colab y tengo este error.

Objetivo de la celda:
[explica qué querías conseguir]

Código ejecutado:
```python
[pega aquí el código]
```

Error completo:
```text
[pega aquí el traceback o mensaje de error]
```

Contexto del dataset:
- Columnas principales:
- Tipo de datos:
- Tamaño aproximado:
- Librerías usadas:

Quiero que me ayudes así:
1. explica la causa probable del error en lenguaje claro;
2. marca la línea o bloque problemático;
3. propón una corrección mínima;
4. dame una versión corregida para Colab;
5. añade una comprobación simple para verificar que funciona;
6. indica si la corrección puede cambiar los resultados.

No inventes columnas ni variables. Si falta contexto, dilo.
```

## Consejo

No pegues solo el error. Incluye también qué querías conseguir y una muestra de `df.head()`, `df.info()` o `df.columns` cuando sea útil.
