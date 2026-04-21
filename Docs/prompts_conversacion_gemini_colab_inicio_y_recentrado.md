# Prompts de conversación para usar con Gemini en Colab
## Inicio + recentrado + corrección de desvíos

Este documento contiene prompts cortos para pegar en el chat de Gemini cuando:
- empiezas una sesión,
- quieres fijar el modo de trabajo,
- se empieza a desviar,
- propone demasiados pasos,
- mete texto basura en el notebook,
- o deja de respetar las reglas.

Usa el bloque más corto que resuelva el problema.  
No hace falta pegar siempre todo.

---

# 1. Prompt de inicio de sesión
Pégalo al empezar una conversación nueva.

```text
Vas a trabajar conmigo dentro de Google Colab para resolver una tarea o examen práctico de BDA.

Debes seguir estas reglas sin excepción:

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
7. No puedes crear celdas de Task, Plan, Strategy, Roadmap, Summary o similares.
8. No puedes hacer varios pasos a la vez.
9. Si una decisión cambia el sentido del análisis o elimina información, debes consultarme antes.

Confirma que trabajarás exactamente así.
```

---

# 2. Prompt de inicio más corto
Útil cuando ya cargaste los documentos y solo quieres fijar el modo.

```text
Trabaja en modo estricto de cuaderno mínimo:
- primero chat,
- luego me preguntas si empiezas,
- sin mi OK no tocas el cuaderno,
- después solo una markdown + una code,
- luego una conclusión breve,
- y vuelves al chat.

Prohibido crear Task, Plan, Strategy, Roadmap, Summary o varias markdown seguidas.
Confirma y espera el punto a resolver.
```

---

# 3. Prompt para pedir análisis conceptual sin tocar el cuaderno
Útil antes de empezar un punto delicado.

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
Úsalo cuando ya quieres que pase del chat al notebook.

```text
Tienes permiso para resolver solo este punto.
Haz exactamente esto:
- una única celda markdown con el texto literal del punto y una explicación simple,
- una única celda de código con comprobación visible,
- nada más.
No añadas conclusiones, planes ni siguientes pasos todavía.
```

---

# 5. Prompt para recentrar si empieza a descontrolarse
Úsalo cuando proponga muchos pasos o se alargue.

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
Úsalo cuando ignore varias veces las reglas.

```text
No estás siguiendo las instrucciones.
Reinicia tu comportamiento desde este momento:

- no toques el cuaderno,
- no propongas varios pasos,
- no generes texto de gestión,
- no conviertas tus intenciones en celdas,
- no sigas por inercia.

Responde ahora solo con:
1. qué punto estamos resolviendo,
2. qué bloque mínimo toca hacer,
3. qué vas a comprobar,
4. y si quiero que empieces.
```

---

# 7. Prompt para cortar celdas innecesarias
Úsalo cuando mete markdown de más o texto basura.

```text
No añadas más texto al cuaderno.
Has metido contenido que no corresponde al notebook final.

Desde ahora:
- el cuaderno no es un chat,
- no escribas Task, Plan, Summary, Next step ni explicaciones de gestión,
- solo una markdown + una code por paso,
- todo lo demás va en el chat.
Confirma y espera instrucciones.
```

---

# 8. Prompt para frenar decisiones delicadas
Úsalo cuando toma decisiones solo.

```text
Detente.
Esa decisión no puedes tomarla solo.

Si una decisión cambia el análisis, elimina información o tiene varias opciones razonables, debes consultarme con este formato:
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
Útil cuando falla una celda y quieres evitar que improvise.

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

# 10. Prompt para pedir cierre limpio de un punto
Úsalo cuando ya ejecutó y quieres que no alargue la conclusión.

```text
Ahora no propongas el siguiente paso todavía.
Dame solo una conclusión breve, clara y en primera persona sobre este punto.
Nada de estrategia, nada de próximos pasos y nada de texto decorativo.
```

---

# 11. Prompt para volver del cuaderno al chat
Útil cuando después de ejecutar sigue escribiendo cosas en el notebook.

```text
Para aquí en el cuaderno.
A partir de este momento, los comentarios, interpretación y siguientes pasos van solo en el chat.
El notebook se limpiará y quedará solo con:
- markdown mínima,
- código,
- conclusión final del punto.
```

---

# 12. Prompt de bloqueo máximo
Úsalo si ya se ha perdido varias veces.

```text
Modo estricto máximo:

- No puedes crear ninguna celda hasta mi OK explícito.
- No puedes proponer más de un punto por mensaje.
- No puedes escribir Task, Plan, Strategy, Roadmap, Summary ni Next step.
- No puedes narrar lo que harás dentro del cuaderno.
- No puedes resolver dos cosas a la vez.
- No puedes tomar decisiones discutibles sin consultarme.

Si incumples cualquiera de estas reglas, debes volver al chat y esperar nuevas instrucciones.
Confirma en una sola frase.
```

---

# 13. Secuencia recomendada de uso
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

---

# 14. Regla práctica final
No intentes corregir todo a la vez.

Cuando Gemini se desvíe:
- corta el comportamiento,
- fija de nuevo el modo,
- devuelve el trabajo al bloque mínimo,
- y sigue.

Lo importante no es discutir con el agente.  
Lo importante es **encerrarlo otra vez en el formato correcto**.
