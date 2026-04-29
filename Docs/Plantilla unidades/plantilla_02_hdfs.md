# Plantilla 02 · Subida de archivos a HDFS

> **Cuándo usar:** apartado "Operaciones HDFS" de UD2.
> **Requisito previo:** plantilla 01 (Instalación) ya inyectada y ejecutada.
> **Cómo se inyecta:** el usuario pasa este `.md` por chat cuando el agente la pide.
> **Resultado esperado:** archivos físicos del dataset subidos a la carpeta HDFS `dataset/`.

---

## Celda Markdown (a insertar antes de la celda de código)

```markdown
## 📂 Operaciones HDFS · Subida de archivos al sistema distribuido

Para subir archivos a HDFS seguimos un patrón fijo de **4 pasos**:

1. **Estabilizar la ruta** copiando el archivo desde su ubicación original (cache de kagglehub, repositorio, etc.) a `/content/<nombre_corto>.csv`. Esto evita problemas con rutas que cambian entre ejecuciones (kagglehub cachea de forma distinta) y permite renombrar a algo manejable sin espacios.
2. **Crear el directorio HDFS** con `hdfs dfs -mkdir -p dataset` (siempre `dataset`, en singular).
3. **Copiar a HDFS** con `hdfs dfs -put -f` (`-f` sobrescribe si ya existe).
4. **Verificar** con `hdfs dfs -ls dataset/`.

Si PLAN_TAREA lista varios archivos, se repiten los pasos 1 y 3 una vez por cada archivo, dejando los pasos 2 y 4 una sola vez.
```

---

## Celda de código (a insertar después de la markdown)

> **Reglas de adaptación:** sustituir `[ADAPTAR_RUTA_ORIGEN]` y `[ADAPTAR_NOMBRE_CORTO]` por los valores reales según PLAN_TAREA. Si hay varios archivos, duplicar el bloque "Paso 1" y "Paso 3" una vez por cada archivo.

```python
import shutil

# Paso 1: estabilizar la ruta copiando a /content/ con nombre corto
# [ADAPTAR] sustituir <ruta_origen> y <nombre_corto>.csv por archivo concreto
# Si hay varios archivos, repite este bloque por cada uno cambiando los nombres
archivo_origen = "[ADAPTAR_RUTA_ORIGEN]"
archivo_local = "/content/[ADAPTAR_NOMBRE_CORTO].csv"
shutil.copy(archivo_origen, archivo_local)
print(f"Archivo copiado localmente a: {archivo_local}")

# Paso 2: crear directorio HDFS (siempre 'dataset', singular)
!hdfs dfs -mkdir -p dataset

# Paso 3: copiar a HDFS (repetir esta línea por cada archivo si hay varios)
!hdfs dfs -put -f /content/[ADAPTAR_NOMBRE_CORTO].csv dataset/

# Paso 4: verificar el contenido del directorio HDFS
print("\n--- Contenido del directorio HDFS dataset/ ---")
!hdfs dfs -ls dataset/
```

---

## Reglas de uso

- **No renombrar la carpeta HDFS.** Siempre `dataset` (singular), aunque el dataset original tenga otro nombre.
- **Pasar siempre por `/content/`.** Nunca hacer `put` directo desde la cache de kagglehub: las rutas cambian entre ejecuciones.
- **Nombres cortos en `/content/`:** sin espacios, sin numeración rara. Ejemplo: `tourist_trips.csv` en lugar de `4- international-tourist-trips.csv`.
- Si el `put` falla, avisar al usuario, no reintentar a ciegas.
