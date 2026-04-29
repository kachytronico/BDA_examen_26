# Plantilla 05 · Instalación de Pig 0.17.0

> **Cuándo usar:** antes de empezar la fase Pig de UD3 (apartado 3 del enunciado), una sola vez por sesión.
> **Requisito previo:** Java disponible en Colab (Java 17 viene preinstalado).
> **Cómo se inyecta:** el usuario pasa este `.md` por chat cuando el agente la pide.
> **Resultado esperado:** Pig 0.17.0 instalado, `PIG_HOME`, `PATH`, `PIG_CLASSPATH` configurados, `pig -h -version` funcional.

---

## Celda Markdown (a insertar antes de las celdas de código)

```markdown
## 🐷 Instalación de Pig 0.17.0 y configuración del entorno

Esta celda instala Apache Pig 0.17.0 y configura las variables de entorno necesarias (`PIG_HOME`, `PATH`, `PIG_CLASSPATH`). Se ejecuta **una sola vez** al inicio de la fase Pig del cuaderno.

`PIG_CLASSPATH` apunta a la configuración de Hadoop, lo que permite a Pig usar el `core-site.xml` y `hdfs-site.xml` instalados con Hadoop. Si Hadoop no está instalado en `/usr/local/hadoop-3.4.2/etc/hadoop`, ajustar la ruta o eliminar la variable (Pig funciona en local sin ella).

Verificación final: `!pig -h -version` debe mostrar `Apache Pig version 0.17.0`.
```

---

## Celda de código 1 · Descarga e instalación

```python
%%bash
wget https://downloads.apache.org/pig/pig-0.17.0/pig-0.17.0.tar.gz
tar -xzf pig-0.17.0.tar.gz
cp -r pig-0.17.0/ /usr/local/
```

---

## Celda de código 2 · Variables de entorno

```python
import os

os.environ["PIG_HOME"] = "/usr/local/pig-0.17.0"
os.environ["PATH"] = os.environ["PATH"] + ":" + "/usr/local/pig-0.17.0/bin"
os.environ["PIG_CLASSPATH"] = "/usr/local/hadoop-3.4.2/etc/hadoop"
```

---

## Celda de código 3 · Verificación

```python
!pig -h -version
```

La salida esperada incluye `Apache Pig version 0.17.0` (entre otra información).

---

## Reglas de uso

- **No regenerar.** Esta plantilla no tiene zonas adaptables. Se inserta tal cual.
- **No repetir** en la misma sesión. Si Pig ya está instalado (`!pig -h -version` funciona y muestra `0.17.0`), saltar a la siguiente fase.
- **No instalar con `apt install pig`** ni desde otras fuentes. Solo el binario oficial de `downloads.apache.org`.
- **No mezclar** versiones de Pig (en UD3 se trabaja siempre con 0.17.0; sintaxis y reglas anti-bug del CONTEXTO_UD3 asumen esta versión).
- Si el `wget` falla (timeout, mirror caído), avisar al usuario y detenerse. **No reintentar a ciegas.**
- `PIG_CLASSPATH` apunta al directorio de configuración de Hadoop. Si Hadoop no está instalado o está en otra ruta, ajustar o comentar esa línea (Pig funciona en local sin Hadoop, usando `!pig` sobre rutas relativas — modo de referencia en UD3).
