# Prompts específicos para Gemini en Colab
## Inicio, control, conclusiones y depuración final del cuaderno

Este documento reúne los prompts prácticos que puedes pegar en el chat de Gemini durante la conversación.

No hace falta usarlos todos.  
Usa solo el que te haga falta en cada momento.

---

# 1. Prompt de inicio de conversación
```text
Te he pasado en esta conversación tres documentos que debes memorizar y tener en cuenta en toda la conversación. Vas a trabajar conmigo en este cuaderno de Google Colab para resolver la tarea marcada en la celda del enunciado. Analiza todo el cuaderno con detalle antes de proceder.

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

# 2. Prompt corto de arranque
```text
Trabaja en modo estricto:
- primero chat,
- luego me preguntas si empiezas,
- sin mi OK no tocas el cuaderno,
- después solo una markdown + una code,
- luego una conclusión breve,
- y vuelves al chat.

No puedes usar el cuaderno como chat ni crear Task, Plan, Strategy, Roadmap, Summary o Next step.
Confirma y espera el punto a resolver.
```

---

# 3. Prompt para pedir análisis conceptual sin tocar el cuaderno
```text
No escribas nada todavía en el cuaderno.
Quiero primero un análisis conceptual breve en el chat:
- qué pide este punto,
- qué riesgos tiene,
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

# 5. Prompt de conclusión breve
```text
Ahora no propongas el siguiente paso todavía.
Dame solo una conclusión breve, clara y en primera persona sobre este punto, aunque haya tenido varias fases.
Nada de estrategia, nada de próximos pasos y nada de texto decorativo.
```

---

# 6. Prompt para recentrar si empieza a descontrolarse
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

# 7. Prompt de recentrado duro
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

# 8. Prompt para cortar celdas innecesarias
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

# 9. Prompt para frenar decisiones delicadas
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

# 10. Prompt para errores
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

# 13. Prompt para depuración final de markdown y conclusiones
```text
Ahora vamos a hacer una depuración final solo del texto del cuaderno.

Quiero que revises todas las celdas markdown y todas las conclusiones ya escritas y las reescribas con estas reglas:

- en primera persona,
- sencillas,
- humanizadas,
- directas,
- sin tono académico,
- sin sonar a IA,
- sin texto decorativo,
- sin repetir explicaciones obvias,
- sin añadir información nueva,
- sin cambiar el sentido técnico,
- sin cambiar la estructura del cuaderno,
- sin crear celdas nuevas,
- sin tocar el código.

Tu objetivo es que el cuaderno suene natural, claro y limpio, como si lo hubiera escrito yo de forma sencilla.

Antes de tocar nada:
1. dime qué tipo de cambios harás,
2. confirma que no vas a modificar estructura ni contenido técnico,
3. y pregúntame si quiero que empieces con esa revisión.
```

---

# 14. Prompt para depuración final de comentarios en código
```text
Ahora quiero una depuración final solo de los comentarios dentro de las celdas de código.

Reglas:
- no cambies ni una sola línea de lógica,
- no cambies nombres de variables,
- no cambies imports,
- no cambies resultados,
- no añadas código nuevo,
- no borres comprobaciones visibles,
- solo puedes reescribir comentarios.

Quiero que los comentarios queden:
- cortos,
- claros,
- útiles,
- en español,
- sencillos,
- directos,
- sin tono académico,
- sin sonar a IA,
- sin explicar cosas evidentes,
- y sin repetir lo que el propio código ya muestra.

Si un comentario no aporta valor real, elimínalo.
Si un comentario es demasiado largo, redúcelo.
Si un bloque no necesita comentarios, déjalo limpio.

Antes de tocar nada:
1. dime qué criterio vas a seguir,
2. confirma que no vas a cambiar el código,
3. y pregúntame si quiero que empieces.
```

---

# 15. Prompt combinado de depuración final
```text
Vamos a hacer la depuración final del cuaderno en dos fases y sin tocar el contenido técnico.

Fase 1:
- revisar todas las celdas markdown y conclusiones,
- dejarlas en primera persona,
- sencillas,
- humanizadas,
- directas,
- sin tono académico,
- sin sonar a IA,
- sin texto decorativo,
- sin añadir información nueva.

Fase 2:
- revisar solo los comentarios dentro del código,
- hacerlos breves, claros y útiles,
- eliminar los innecesarios,
- sin cambiar absolutamente nada del código.

Reglas generales:
- no crear celdas nuevas,
- no mover celdas,
- no cambiar estructura,
- no cambiar lógica,
- no cambiar resultados.

Antes de empezar, dime exactamente cómo harás estas dos fases y pregúntame si quiero que empieces por la fase 1.
```

---

# 16. Prompt para reinterpretación semántica incorrecta
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

# 17. Prompt específico para UD1 · Punto 4
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

# 18. Uso práctico recomendado
## Al empezar
Usa el prompt 1 o 2.

## Antes de un punto delicado
Usa el prompt 3.

## Cuando quieras que escriba el bloque
Usa el prompt 4.

## Cuando haya terminado el punto
Usa el prompt 5.

## Si se empieza a perder
Usa el prompt 6.

## Si insiste
Usa el prompt 7 o 12.

## Si mete basura en el notebook
Usa el prompt 8.

## Si toma decisiones solo
Usa el prompt 9.

## Si falla una ejecución
Usa el prompt 10.

## Si quieres devolver todo al chat
Usa el prompt 11.

## Cuando el cuaderno ya esté hecho y quieras pulirlo
Usa el prompt 13 y luego el 14, o directamente el 15.

## Si cambia el significado del apartado
Usa el prompt 16.
Si además es UD1 punto 4, usa el 17.

---

# 19. Regla práctica final
No intentes corregir todo a la vez.

Cuando Gemini se desvíe:
- corta el comportamiento,
- fija otra vez el modo,
- vuelve al bloque mínimo,
- y sigue.

Lo importante no es discutir con el agente.
Lo importante es **encerrarlo otra vez en el formato correcto**.
