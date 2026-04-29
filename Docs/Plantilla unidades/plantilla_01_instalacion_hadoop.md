# Plantilla 01 · Instalación de Hadoop y configuración

> **Cuándo usar:** al inicio de la parte UD2 del cuaderno, una sola vez por sesión.
> **Cómo se inyecta:** el usuario pasa este `.md` por chat cuando el agente la pide.
> **Resultado esperado:** Hadoop 3.4.2 instalado, variables de entorno configuradas, `hadoop version` funcional.

---

## Celda Markdown (a insertar antes de la celda de código)

```markdown
## ⚙️ Instalación de Hadoop y configuración del entorno

Esta celda instala Hadoop 3.4.2 y configura las variables de entorno necesarias (`JAVA_HOME`, `HADOOP_HOME`, `PATH`). Se ejecuta **una sola vez** al inicio de la parte UD2 del cuaderno.

Java 17 viene preinstalado en Colab, así que solo hay que verificarlo.
```

---

## Celda de código (a insertar después de la markdown)

```python
import os

# 1. Verificar Java disponible (Colab trae Java 17 preinstalado)
!ls -l /usr/lib/jvm/

# 2. Descargar Hadoop 3.4.2
!wget -q https://downloads.apache.org/hadoop/common/hadoop-3.4.2/hadoop-3.4.2.tar.gz

# 3. Descomprimir y mover a /usr/local/hadoop
!tar -xzf hadoop-3.4.2.tar.gz
!mv hadoop-3.4.2/ /usr/local/hadoop

# 4. Configurar variables de entorno
os.environ["JAVA_HOME"] = "/usr/lib/jvm/java-17-openjdk-amd64"
os.environ["HADOOP_HOME"] = "/usr/local/hadoop"
os.environ["PATH"] += f":{os.environ['HADOOP_HOME']}/bin:{os.environ['HADOOP_HOME']}/sbin"

# 5. Verificar instalación
!hadoop version
```

---

## Reglas de uso

- **No regenerar.** Esta plantilla no tiene zonas adaptables. Se inserta tal cual.
- **No repetir** en la misma sesión. Si Hadoop ya está instalado (`hadoop version` funciona), saltar a la siguiente plantilla.
- Si la instalación falla (timeout en `wget`, error en `mv`), avisar al usuario y detenerse.
