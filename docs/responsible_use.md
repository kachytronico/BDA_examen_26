# Uso responsable del agente

Este repositorio documenta un flujo de trabajo para usar Gemini como apoyo a la programación en Google Colab. No es un agente autónomo de producción ni sustituye la revisión humana.

## Principios de uso

- **Revisión humana:** todo código generado debe leerse, entenderse, ejecutarse y validarse antes de usarlo.
- **Privacidad:** no se deben compartir datos personales, datos sensibles, claves API ni credenciales.
- **Materiales del curso:** no se deben publicar enunciados privados, apuntes protegidos ni contenido del profesorado sin permiso.
- **Contexto propio:** el contexto utilizado debe estar formado por notas propias, resúmenes, criterios personales de trabajo y materiales que puedan usarse legalmente.
- **Transparencia:** si un bloque ha sido generado o mejorado con IA, debe revisarse y adaptarse antes de incorporarlo a un notebook.
- **Seguridad:** las claves de Gemini u otros servicios deben almacenarse como secretos o variables de entorno, nunca dentro del repositorio.

## Qué puede hacer bien

- Ayudar a depurar errores de Python, pandas, scikit-learn o Colab.
- Convertir teoría en pasos de código.
- Proponer estructuras para notebooks.
- Mejorar explicaciones Markdown.
- Simplificar código largo.
- Recordar comprobaciones básicas antes de entrenar un modelo.

## Qué no debe hacer sin revisión

- Decidir cómo tratar datos sensibles.
- Eliminar columnas o filas sin justificación.
- Inventar resultados o columnas inexistentes.
- Copiar materiales privados.
- Sustituir el criterio técnico del alumno.
- Tomar decisiones clínicas, legales o académicas por sí solo.

## Buen flujo de trabajo

1. Preparar un contexto mínimo y propio.
2. Pedir una tarea concreta.
3. Ejecutar el código en Colab.
4. Comprobar salidas, errores y métricas.
5. Simplificar lo necesario.
6. Documentar la decisión final.

## Nota final

La IA generativa es útil como apoyo al aprendizaje y a la programación, pero el valor profesional está en saber formular bien la tarea, revisar la respuesta y explicar por qué se acepta o se modifica una solución.