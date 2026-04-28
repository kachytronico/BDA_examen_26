# Prompts específicos · Sistema BDA en Colab v4

Prompts listos para pegar en el chat de Gemini. Usa solo el que necesites en cada momento.

---

## CICLO PRINCIPAL (los tres prompts del flujo normal)

### 1. Iniciar / pasar al siguiente punto
```text
Pasa al siguiente punto del enunciado.

Antes de proponer nada:
- consulta el contexto de la unidad (celda CONTEXTO_UD# del cuaderno) y la regla aplicable a este punto,
- cítala (literal o casi literal),
- y solo entonces dime qué punto toca, qué vas a comprobar y si quieres empezar.

No toques el cuaderno todavía.
```

### 2. Autorizar el bloque
```text
Tienes permiso para resolver solo este punto.

Crea exactamente:
- una celda markdown con el texto literal del punto y una explicación simple,
- una celda de código mínima con comprobación visible.

Nada más. No añadas conclusión, ni propongas el siguiente paso, ni anuncies lo que harás después.
```

### 3. Pedir la conclusión
```text
Ahora escribe solo la conclusión de este punto.

Reglas:
- celda de texto del cuaderno (no solo en chat),
- breve (1-3 líneas),
- en primera persona,
- ligada al resultado real,
- termina con un dato concreto, no con frases de cierre tipo "asegurando que..." o "garantizando la...".

No propongas el siguiente paso. No añadas estrategia. Solo la conclusión.
```

---

## PROMPT DE INICIO DE SESIÓN

### 4. Confirmación inicial
```text
Te he pasado por chat el documento maestro y la guía de estilo. El contexto de la unidad está en una celda CONTEXTO_UD# del cuaderno.

Antes de empezar:
1. Confirma que has recibido los tres materiales.
2. Lee el enunciado del cuaderno.
3. Dime qué has entendido del ejercicio en general.
4. No propongas todavía qué punto resolver.

Espera mi señal para empezar.
```

---

## RECENTRADO (cuando se descontrola)

### 5. Recentrado suave
```text
Te estás adelantando.

Vuelve al protocolo:
- nada de varios pasos,
- nada de planes,
- nada de proponer el siguiente punto sin que yo lo pida,
- solo el punto actual.

Reformula en el chat qué punto estás resolviendo, qué regla del contexto aplica y si quiero que sigas.
```

### 6. Recentrado duro
```text
No estás siguiendo las instrucciones.

Reinicia tu comportamiento:
- no toques el cuaderno,
- no propongas varios pasos,
- no generes texto de gestión,
- no resuelvas dos cosas a la vez.

Responde solo con:
1. qué punto estamos resolviendo,
2. qué dice el contexto de la unidad sobre ese punto (cítalo),
3. qué bloque mínimo toca,
4. si quiero que empieces.
```

### 7. Cortar celdas innecesarias
```text
Has metido contenido que no corresponde al cuaderno final.

Desde ahora:
- el cuaderno no es un chat,
- no escribas Task, Plan, Strategy, Roadmap, Checklist, Summary ni Next step (en español ni en inglés),
- solo una markdown + una code + una conclusión por punto,
- todo lo demás va en el chat.

Confirma y espera instrucciones.
```

---

## DECISIONES Y ERRORES

### 8. Frenar decisiones delicadas
```text
Detente. Esa decisión no puedes tomarla solo.

Si una decisión cambia el análisis, elimina información, fija un criterio discutible o tiene varias opciones razonables, debes consultarme con este formato:
- situación detectada,
- decisión pendiente,
- opciones posibles,
- consecuencia de cada una,
- recomendación provisional,
- pregunta final.

No sigas hasta que yo responda.
```

### 9. Errores
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

## REINTERPRETACIÓN Y ALUCINACIÓN

### 10. Reinterpretación incorrecta
```text
Estás reinterpretando mal este apartado.

No uses una definición genérica.

Cita textualmente la regla del contexto de la unidad (celda CONTEXTO_UD# del cuaderno) que define qué significa este punto en esta unidad concreta.

Después dime qué bloque mínimo corresponde según esa regla. No toques el cuaderno todavía.
```

### 11. Verificar contra el cuaderno (anti-alucinación)
```text
Para. Antes de afirmar nada más:

1. Mira las celdas del cuaderno y dime qué archivos, DataFrames o variables reales hay, en qué celda se cargan y con qué nombre exacto.
2. Si para alguna afirmación no puedes citar una celda concreta, dilo claro y no la afirmes.

No propongas todavía ningún bloque. Solo verifica.
```

---

## DEPURACIÓN FINAL

### 12. Depurar markdown y conclusiones
```text
Vamos a hacer una depuración final solo del texto del cuaderno.

Revisa todas las celdas markdown y todas las conclusiones y reescríbelas con estas reglas:
- en primera persona,
- sencillas, humanizadas, directas,
- sin tono académico, sin sonar a IA, sin texto decorativo,
- sin gerundios decorativos al final ("asegurando que...", "garantizando la..."),
- sin añadir información nueva,
- sin cambiar el sentido técnico,
- sin crear celdas nuevas,
- sin tocar el código.

Antes de empezar:
1. dime qué cambios concretos vas a hacer,
2. confirma que no modificarás estructura ni lógica,
3. pregúntame si empiezo.
```

### 13. Depurar comentarios del código
```text
Ahora depuración solo de los comentarios dentro del código.

Reglas:
- no cambies ni una línea de lógica,
- no cambies nombres de variables ni imports,
- no añadas código nuevo, no borres comprobaciones visibles,
- solo reescribe comentarios.

Quiero comentarios cortos, claros, útiles, en español, sin tono académico, sin explicar lo evidente.

Si un comentario no aporta valor, elimínalo.

Antes de empezar, dime qué criterio seguirás y pregúntame si empiezo.
```

---

## USO RECOMENDADO

| Momento | Prompt |
|---|---|
| Inicio de sesión | 4 |
| Empezar / pasar al siguiente punto | 1 |
| Autorizar bloque tras OK | 2 |
| Pedir conclusión tras ejecutar | 3 |
| Empieza a descontrolarse | 5 |
| Insiste en descontrolarse | 6 |
| Mete celdas Task/Plan/etc | 7 |
| Va a tomar decisión delicada solo | 8 |
| Hay un error | 9 |
| Reinterpreta el apartado | 10 |
| Inventa archivos/columnas | 11 |
| Cuaderno terminado, pulir texto | 12 → 13 |

---

## Regla práctica final

No intentes corregir todo a la vez. Cuando Gemini se desvíe:
- corta el comportamiento,
- fija otra vez el modo,
- vuelve al bloque mínimo,
- y sigue.

Lo importante no es discutir con el agente sino **encerrarlo otra vez en el formato correcto**.
