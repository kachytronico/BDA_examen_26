# Prompts de conversación para Gemini en Colab
## Inicio, recentrado y corrección semántica de apartados

Este documento reúne prompts cortos para:

- arrancar una sesión,
- fijar el modo estricto,
- recentrar a Gemini si se descontrola,
- y corregirlo si empieza a reinterpretar apartados del enunciado.

---

# 1. Prompt de inicio de sesión
```text
Vas a trabajar conmigo en Google Colab para resolver una tarea o examen práctico de BDA.

Reglas obligatorias:
1. Primero analizas el enunciado y respondes solo en el chat.
2. Antes de tocar el cuaderno debes decir:
   - qué has entendido,
   - qué punto toca resolver,
   - qué vas a comprobar,
   - y preguntarme si quiero que empieces por ese punto.
3. Sin mi OK no puedes crear ninguna celda.
4. Cuando te autorice, solo puedes crear:
   - una celda markdown con el texto literal del punto y una explicación muy breve,
   - una celda de código mínima con evidencia visible.
5. Después de ejecutar, escribes solo una conclusión breve y vuelves al chat.
6. No puedes usar el cuaderno como chat.
7. No puedes crear Task, Plan, Strategy, Roadmap, Summary ni Next step.
8. No puedes resolver varios puntos a la vez.
9. Si un apartado admite varias interpretaciones, debes seguir primero el contexto de la unidad y las tareas resueltas, no una definición genérica.

Confirma que trabajarás exactamente así.
```

---

# 2. Prompt de arranque corto
```text
Modo estricto:
- primero chat,
- luego me preguntas si empiezas,
- sin mi OK no tocas el cuaderno,
- después solo una markdown + una code,
- luego una conclusión breve,
- y vuelves al chat.

Además:
- no reinterpretes apartados del enunciado por tu cuenta,
- sigue primero el contexto de la unidad.
Confirma y espera el punto a resolver.
```

---

# 3. Prompt para pedir análisis conceptual sin tocar el cuaderno
```text
No escribas nada todavía en el cuaderno.
Quiero primero un análisis conceptual breve en el chat:
- qué pide este punto,
- qué significado tiene en esta unidad,
- qué bloque mínimo tocaría hacer,
- y si quieres empezar por ese punto.
Nada más.
```

---

# 4. Prompt para autorizar un único bloque
```text
Tienes permiso para resolver solo este punto.
Haz exactamente esto:
- una única celda markdown con el texto literal del punto y una explicación simple,
- una única celda de código con comprobación visible,
- nada más.
No añadas conclusiones, planes ni siguientes pasos todavía.
```

---

# 5. Prompt de recentrado si empieza a proponer varios pasos
```text
Te estás adelantando.
Vuelve al protocolo estricto:
- nada de varios pasos,
- nada de planes largos,
- nada de strategy,
- nada de roadmap,
- solo el punto actual.
Reformula en el chat qué has entendido y pregúntame si quieres que empieces por ese único punto.
```

---

# 6. Prompt de recentrado duro
```text
No estás siguiendo las instrucciones.
Reinicia tu comportamiento ahora:

- no toques el cuaderno,
- no propongas varios pasos,
- no generes texto de gestión,
- no conviertas tus intenciones en celdas,
- no sigas por inercia.

Responde solo con:
1. qué punto estamos resolviendo,
2. qué bloque mínimo toca hacer,
3. qué vas a comprobar,
4. y si quiero que empieces.
```

---

# 7. Prompt para cortar celdas innecesarias
```text
No añadas más texto al cuaderno.
Has metido contenido que no corresponde al notebook final.

Desde ahora:
- el cuaderno no es un chat,
- no escribas Task, Plan, Summary, Roadmap ni Next step,
- solo una markdown + una code por paso,
- todo lo demás va en el chat.
Confirma y espera instrucciones.
```

---

# 8. Prompt para frenar decisiones delicadas
```text
Detente.
Esa decisión no puedes tomarla solo.

Si una decisión cambia el análisis, elimina información, fija un criterio discutible o tiene varias opciones razonables, debes consultarme con este formato:
- situación detectada,
- decisión pendiente,
- opciones,
- consecuencia de cada una,
- recomendación provisional,
- pregunta final.

No sigas hasta que yo responda.
```

---

# 9. Prompt para errores
```text
No rehagas nada todavía.
Describe el error de forma breve:
- qué ha fallado,
- causa probable,
- corrección propuesta,
- y si esa corrección implica una decisión importante.
Si implica una decisión relevante, espera mi confirmación.
```

---

# 10. Prompt para cierre limpio de un punto
```text
Ahora no propongas el siguiente paso todavía.
Dame solo una conclusión breve, clara y en primera persona sobre este punto.
Nada de estrategia, nada de próximos pasos y nada de texto decorativo.
```

---

# 11. Prompt para devolver comentarios al chat
```text
Para aquí en el cuaderno.
A partir de ahora, interpretación, siguientes pasos y comentarios van solo en el chat.
El notebook se limpiará y quedará solo con:
- markdown mínima,
- código,
- conclusión final del punto.
```

---

# 12. Prompt de bloqueo máximo
```text
Modo estricto máximo:

- No puedes crear ninguna celda hasta mi OK explícito.
- No puedes proponer más de un punto por mensaje.
- No puedes escribir Task, Plan, Strategy, Roadmap, Summary ni Next step.
- No puedes narrar lo que harás dentro del cuaderno.
- No puedes resolver dos cosas a la vez.
- No puedes tomar decisiones discutibles sin consultarme.
- No puedes reinterpretar un apartado usando una definición genérica si el contexto de la unidad ya fija su significado.

Si incumples cualquiera de estas reglas, debes volver al chat y esperar nuevas instrucciones.
Confirma en una sola frase.
```

---

# 13. Prompt específico para corregir reinterpretación semántica
Úsalo cuando Gemini cambia el significado de un punto.

```text
Estás reinterpretando mal este apartado.

No uses una definición genérica.
Debes seguir el significado operativo fijado por:
1. el enunciado,
2. el contexto de la unidad,
3. las tareas resueltas.

Reformula qué significa este punto en esta unidad concreta y dime qué bloque mínimo corresponde.
No toques todavía el cuaderno.
```

---

# 14. Prompt específico para UD1 · Punto 4
Úsalo exactamente para el caso que has detectado.

```text
En UD1, el punto 4 no se resuelve con limpieza textual.

Debes seguir la interpretación operativa del contexto de UD1:
- normalización = escalado de variables numéricas,
- normalmente con StandardScaler o MinMaxScaler.

La limpieza textual no sustituye este punto.
Si el dataset tiene pocas numéricas útiles, identifícalas, separa identificadores de variables continuas y pregúntame antes de decidir.

Reformula el sentido del punto 4 de UD1 y propón solo el bloque mínimo correcto.
No escribas nada todavía en el cuaderno.
```

---

# 15. Prompt para cuando el dataset complica el patrón estándar
```text
No cambies el significado del apartado por culpa del dataset.
Si el dataset no encaja del todo en el patrón habitual:
- explica qué columnas reales ves,
- qué opciones hay,
- qué parte del patrón sigue siendo obligatoria,
- y qué decisión necesitas que valide.
No resuelvas el punto por tu cuenta con una reinterpretación cómoda.
```

---

# 16. Secuencia recomendada de uso
## Al empezar
1. usa el Prompt 1 o 2

## Antes de un punto delicado
2. usa el Prompt 3

## Cuando quieras que escriba el bloque
3. usa el Prompt 4

## Si se empieza a perder
4. usa el Prompt 5

## Si insiste
5. usa el Prompt 6 o 12

## Si mete basura en el notebook
6. usa el Prompt 7

## Si toma decisiones solo
7. usa el Prompt 8

## Si falla una ejecución
8. usa el Prompt 9

## Si quieres cierre limpio
9. usa el Prompt 10 y luego el 11

## Si cambia el significado del apartado
10. usa el Prompt 13  
11. y, si es UD1 punto 4, usa el Prompt 14

---

# 17. Regla práctica final
No discutas demasiado con el agente.

Cuando se desvíe:
- corta el comportamiento,
- fija otra vez el modo,
- corrige el significado del punto,
- vuelve al bloque mínimo,
- y sigue.

Lo importante no es convencerlo.  
Lo importante es **encerrarlo otra vez en el formato y en el significado correctos**.
