# Colab Gemini Course Agent

Flujo de trabajo para usar **Gemini** como asistente de programación en **Google Colab**, contextualizado con materiales propios de estudio del Curso de Especialización en **Inteligencia Artificial y Big Data**.

> Nota importante: este proyecto no afirma que Gemini esté entrenado o fine-tuneado. El enfoque correcto es: **asistente contextualizado con apuntes, criterios y materiales propios de estudio**.

---

## Qué problema resuelve

Cuando trabajo con notebooks de IA y Big Data en Colab, necesito:

- convertir teoría en pasos de programación;
- depurar errores de Python, pandas, scikit-learn o Colab;
- simplificar bloques de código largos;
- documentar mejor los notebooks;
- preparar soluciones reutilizables para prácticas y estudio;
- revisar si una propuesta de código tiene sentido antes de usarla.

Este repo documenta un flujo para trabajar con IA generativa de forma práctica, responsable y revisada.

---

## Qué demuestra

Este proyecto muestra competencias en:

- uso de **IA generativa aplicada a programación**;
- diseño de prompts para tareas técnicas;
- trabajo con contexto de estudio;
- programación asistida en Google Colab;
- revisión humana del código generado;
- documentación técnica clara;
- uso responsable de herramientas de IA.

---

## Cómo funciona el flujo

1. **Preparar contexto**: apuntes propios, objetivos del módulo, criterios de evaluación o resumen del problema.
2. **Definir la tarea**: pedir una acción concreta, no una respuesta genérica.
3. **Pedir ayuda a Gemini**: código, explicación, depuración o simplificación.
4. **Probar en Colab**: ejecutar y comprobar errores.
5. **Revisar manualmente**: validar lógica, resultados, librerías y riesgos.
6. **Documentar el resultado**: dejar el notebook claro para otra persona.

---

## Prompts incluidos

- [`prompts/system_prompt_template.md`](prompts/system_prompt_template.md): plantilla base para pedir ayuda técnica en Colab.
- [`prompts/colab_debugging_prompt.md`](prompts/colab_debugging_prompt.md): prompt para depurar errores de Python/Colab.
- [`prompts/notebook_documentation_prompt.md`](prompts/notebook_documentation_prompt.md): prompt para mejorar explicaciones Markdown en notebooks.

---

## Ejemplos incluidos

- [`examples/example_workflow.md`](examples/example_workflow.md): ejemplo de flujo tarea → prompt → código → revisión humana.
- [`examples/example_colab_task.md`](examples/example_colab_task.md): ejemplo de bloque inicial para revisar un CSV en Colab.

---

## Estructura del repositorio

```text
colab-gemini-course-agent/
├── README.md
├── LICENSE
├── .gitignore
├── prompts/
│   ├── system_prompt_template.md
│   ├── colab_debugging_prompt.md
│   └── notebook_documentation_prompt.md
├── examples/
│   ├── example_workflow.md
│   └── example_colab_task.md
└── docs/
    └── responsible_use.md
```

---

## Límites y seguridad

Este repositorio no incluye:

- claves API;
- materiales privados del curso;
- datos personales o sensibles;
- contenido protegido del profesorado;
- enunciados internos no publicables.

La salida de una IA no se acepta sin más: se prueba, se revisa y se adapta. El objetivo no es sustituir el aprendizaje, sino usar la IA como apoyo para programar, estudiar y documentar mejor.

Consulta [`docs/responsible_use.md`](docs/responsible_use.md) para ver las reglas de uso responsable.

---

## Relación con mi perfil profesional

Este proyecto forma parte de mi portfolio como estudiante de **IA & Big Data** y futuro formador en **IA aplicada**.

Muestra una competencia cada vez más importante: saber integrar herramientas de IA generativa en un flujo de trabajo técnico, sin perder criterio, revisión humana ni responsabilidad.

---

## English summary

This repository documents a workflow for using Gemini as an AI-assisted programming helper for Google Colab notebooks. It is contextualized with my own AI & Big Data study materials, but it does not claim model fine-tuning. The goal is to show prompt engineering, AI-assisted coding, human review, notebook documentation and responsible use of generative AI in technical learning workflows.
