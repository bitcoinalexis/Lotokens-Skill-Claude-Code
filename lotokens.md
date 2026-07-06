---
name: lotokens
description: Menu para ahorrar tokens — bloquea escritura de .md, desactiva emojis, prohibe comentarios, activa respuestas cortas, optimiza lectura de archivos, bloquea sub-agentes triviales, restringe busquedas web y elimina verificaciones decorativas. Cada funcion es independiente y activable por separado. Permite guardar preferencias de forma persistente en CLAUDE.md.
user-invocable: true
---

Cuando el usuario invoca esta skill, sigue estos pasos en orden:

## Paso 0 — Idioma (solo la primera vez)

Antes de mostrar el menu, determina el idioma de la interfaz de LoTokens:

1. Lee el CLAUDE.md global y busca un marcador de idioma con el formato:
   `<!-- LoTokens-lang: es -->` o `<!-- LoTokens-lang: en -->`
   - Linux / macOS: `~/.claude/CLAUDE.md`
   - Windows: `%USERPROFILE%\.claude\CLAUDE.md`
2. Si el marcador EXISTE: usa ese idioma (`es` = Español, `en` = English) y NO preguntes. Salta directamente al Paso 1.
3. Si el marcador NO existe (primera vez): usa AskUserQuestion (single select):
   - Pregunta: `Idioma / Language`
   - Opciones:
     - `Español` — Mostrar LoTokens en español.
     - `English` — Show LoTokens in English.
4. Tras la eleccion, escribe el marcador en el CLAUDE.md global para recordarlo en futuras sesiones:
   - Si la seccion `## LoTokens` ya existe, agrega o actualiza la linea del marcador dentro de ella.
   - Si no existe, crea la seccion `## LoTokens` con solo el marcador (las reglas se agregan despues, en el Paso 2).
   - Escribe SIEMPRE el marcador, incluso si despues el usuario elige "Solo esta sesion". El marcador es solo la preferencia de idioma, no las reglas.
   - Este Write sobre CLAUDE.md esta permitido aunque "Bloquear .md" se active luego (es configuracion inicial).

A partir de aqui, presenta TODOS los textos de LoTokens (preguntas, opciones, descripciones, confirmaciones y bloques de reglas guardados) en el idioma elegido.

- Si el idioma es `es` (Español): usa los textos de los Pasos 1 y 2 tal como estan escritos abajo.
- Si el idioma es `en` (English): usa las versiones traducidas de la seccion `## English mode` al final de este documento.

---

## Paso 1 — Menu principal

Usa AskUserQuestion con multiSelect: true.

Pregunta: "LoTokens — selecciona las funciones que deseas activar:"

Opciones disponibles (el usuario puede elegir varias, una, o ninguna):

- **Bloquear .md** — Impide crear o editar archivos `.md` durante esta sesion (excepto `CLAUDE.md`, `AGENTS.md` y `.md` dentro de `memory/` o `.claude/`, que siempre estan permitidos).
- **Sin emojis** — Prohibe emojis en todas tus respuestas y en el codigo que generes.
- **Sin comentarios** — Prohibe escribir comentarios en el codigo generado (`#`, `//`, `/* */`, `<!-- -->`, docstrings, headers decorativos, etc.).
- **Respuesta corta** — Al completar cualquier tarea (cuando usas herramientas), responde unicamente: `Listo`
- **Lectura eficiente** — Evita releer archivos en contexto, limita `Read` de archivos grandes y siempre usa `head_limit` en `Grep`/`Glob`.
- **Sin sub-agentes triviales** — Bloquea `Agent`/`Explore` cuando un `Grep`, `Glob` o `Read` directo basta (un sub-agente consume todo su propio contexto).
- **Sin web innecesaria** — Usa `WebSearch`/`WebFetch` solo cuando el usuario lo pida o sea estrictamente necesario.
- **Sin verificaciones decorativas** — No releer ni reejecutar para "confirmar" despues de editar o ejecutar; confia en el resultado de la herramienta.

---

## Paso 2 — Persistencia

Si el usuario selecciono al menos una funcion, usa AskUserQuestion (single select) para preguntar:

Pregunta: "Guardar estas preferencias para futuras sesiones?"

Opciones:
- **Solo esta sesion** — Las reglas aplican a TODA la sesion hasta que termines. No se guarda nada en disco.
- **Guardar globalmente** — Escribe las reglas en el CLAUDE.md global (aplica en todos los proyectos).
- **Guardar para este proyecto** — Escribe las reglas en `.claude/CLAUDE.md` (solo este proyecto).

### Rutas segun sistema operativo

Detecta el sistema operativo antes de escribir:

- Linux / macOS — Global: `~/.claude/CLAUDE.md` | Proyecto: `.claude/CLAUDE.md`
- Windows — Global: `%USERPROFILE%\.claude\CLAUDE.md` | Proyecto: `.claude\CLAUDE.md`

Para detectar Windows: verifica si la variable de entorno `USERPROFILE` existe o si el path separator es `\`. Si no puedes detectarlo, usa `~/.claude/CLAUDE.md` como fallback y advierte al usuario que en Windows debe usar `%USERPROFILE%\.claude\CLAUDE.md`.

### Si elige guardar (global o proyecto):

Lee el archivo CLAUDE.md si existe. Luego agrega (o reemplaza si ya existe una seccion `## LoTokens`) el siguiente bloque con solo las reglas de las funciones seleccionadas. Conserva el marcador `<!-- LoTokens-lang: ... -->` dentro de la seccion:

```
## LoTokens

<!-- Generado automaticamente por la skill LoTokens. Edita o elimina esta seccion para cambiar las preferencias. -->
<!-- LoTokens-lang: es -->

[incluye solo las reglas de las funciones que el usuario selecciono]
```

Reglas por funcion para escribir en CLAUDE.md:

**Bloquear .md:**
```
### Bloquear .md
- NO uses Write, Edit, ni Bash con comandos que escriban a archivos `.md` (`>`, `>>`, `sed -i`, `tee`, `cp`, `mv`, `awk -i inplace`, etc.)
- Incluye README.md y cualquier otro Markdown
- EXCEPCIONES (permitidos siempre):
  - `CLAUDE.md` (global o de proyecto) — para guardar preferencias e instrucciones persistentes
  - `AGENTS.md` — instrucciones del proyecto
  - `MEMORY.md` y cualquier `.md` dentro de un directorio `memory/` — para el sistema auto-memory
  - Cualquier `.md` dentro del directorio `.claude/` (planes, settings en MD, etc.)
- Si se intenta escribir otro .md, responde exactamente: `Bloqueado por LoTokens: escritura de .md desactivada.`
```

**Sin emojis:**
```
### Sin emojis
- NO incluyas emojis en respuestas de texto
- NO incluyas emojis en comentarios de codigo, strings ni documentacion inline
```

**Sin comentarios:**
```
### Sin comentarios
- NO escribas comentarios de ningun tipo en el codigo generado
- Incluye: `#` (Python, shell, YAML), `//` (JS, TS, Go, Java, C/C++, Rust), `/* */`, `<!-- -->` (HTML/XML), `--` (SQL, Lua), `;` (Lisp, ASM)
- NO escribas docstrings de Python (`"""..."""` o `'''...'''`) ni JSDoc (`/** */`)
- NO escribas headers decorativos tipo `# ===== CONFIGURACION =====` ni separadores de seccion
- NO escribas comentarios de "que hace este archivo" al inicio del script
- NO escribas comentarios al final de linea explicando codigo (`x = 5  # contador`)
- Si modificas un archivo existente que YA tiene comentarios, no agregues nuevos pero deja los existentes intactos (a menos que el usuario pida quitarlos)
- EXCEPCION UNICA: solo escribe comentarios si el usuario los solicita EXPLICITAMENTE en esa peticion concreta
- Si la sintaxis del lenguaje exige texto en cierta posicion (ej. shebang `#!/usr/bin/env python`), eso NO es comentario y SI va
```

**Respuesta corta:**
```
### Respuesta corta
- Una "tarea" es cualquier accion que requiera usar herramientas (Write, Edit, Bash, Read, Glob, Grep, etc.)
- Una "pregunta" es cualquier solicitud de informacion o explicacion sin uso de herramientas
- Al completar una TAREA: responde UNICAMENTE la palabra `Listo` — sin resumen, sin lista de cambios, sin contexto
- Al responder una PREGUNTA: responde de forma concisa (maximo 2-3 oraciones), sin detalle excesivo
- Si el usuario pide explicitamente que expliques algo: es una pregunta, responde concisamente
- Esta regla aplica a TODAS las respuestas siguientes en la sesion
```

**Lectura eficiente:**
```
### Lectura eficiente
- NO releas archivos que ya estan en tu contexto de la sesion; reutiliza lo que ya cargaste
- Si debes leer un archivo grande (>500 lineas), usa `offset` y `limit` para leer solo lo necesario
- Usa SIEMPRE `head_limit` en `Grep` y `Glob` para acotar resultados (ej. `head_limit: 50`)
- Prefiere `Grep` con patron especifico antes que leer archivos completos
- Si una sola herramienta resuelve la duda, no lances varias redundantes
```

**Sin sub-agentes triviales:**
```
### Sin sub-agentes triviales
- NO uses `Agent` ni `Explore` para tareas que un `Grep`, `Glob` o `Read` directo resuelve
- Un sub-agente consume TODO su propio contexto: reservalo para busquedas amplias o paralelas reales
- Antes de lanzar un sub-agente, preguntate: ¿puedo resolverlo con una herramienta directa?
- Si la tarea es un solo lookup (un simbolo, un valor, un archivo), hazlo tu mismo
```

**Sin web innecesaria:**
```
### Sin web innecesaria
- NO uses `WebSearch` ni `WebFetch` a menos que el usuario lo pida o sea estrictamente necesario
- Resuelve primero con el codigo, la documentacion local y tu conocimiento
- Si dudas si vale la pena buscar en web, prefiero NO hacerlo y preguntar al usuario
```

**Sin verificaciones decorativas:**
```
### Sin verificaciones decorativas
- NO releas un archivo despues de editarlo para "confirmar" que cambio (Edit falla si no coincide)
- NO reejecutes un comando despues de ver su salida exitosa para "asegurarte"
- NO repitas en texto lo que ya se hizo; ve directo a la siguiente accion
- Confia en el resultado que reporta la herramienta
```

### Caso especial: "Bloquear .md" + guardar en CLAUDE.md

Si el usuario selecciona "Bloquear .md" y tambien elige guardar (global o proyecto):
- Permite escribir en CLAUDE.md UNA SOLA VEZ como operacion de configuracion inicial
- Despues de guardar, la regla "Bloquear .md" entra en efecto para el resto de la sesion
- Esto es el unico caso donde Write sobre un `.md` esta permitido mientras "Bloquear .md" esta activo

Si el usuario elige "Solo esta sesion", omite escribir las reglas (pero el marcador de idioma del Paso 0 ya quedo guardado).

---

## Reglas por funcion (para esta sesion)

Las siguientes reglas aplican INMEDIATAMENTE desde el momento en que el usuario las selecciona, para toda la sesion hasta que termine.

### Bloquear .md
- NO uses Write, Edit, ni Bash con comandos que escriban a archivos `.md` (`>`, `>>`, `sed -i`, `tee`, `cp`, `mv`, `awk -i inplace`, etc.)
- Incluye README.md y cualquier otro Markdown
- EXCEPCIONES (permitidos siempre):
  - `CLAUDE.md` (global o de proyecto) — para guardar preferencias e instrucciones persistentes
  - `AGENTS.md` — instrucciones del proyecto
  - `MEMORY.md` y cualquier `.md` dentro de un directorio `memory/` — para el sistema auto-memory
  - Cualquier `.md` dentro del directorio `.claude/` (planes, settings en MD, etc.)
- Si se intenta, responde exactamente: `Bloqueado por LoTokens: escritura de .md desactivada.`

### Sin emojis
- NO incluyas emojis en respuestas de texto
- NO incluyas emojis en comentarios de codigo, strings ni documentacion inline

### Sin comentarios
- NO escribas comentarios de ningun tipo en el codigo generado
- Incluye: `#`, `//`, `/* */`, `<!-- -->`, `--`, `;`, docstrings `"""..."""` / `'''...'''`, JSDoc `/** */`
- NO escribas headers decorativos tipo `# ===== CONFIGURACION =====` ni separadores de seccion
- NO escribas comentarios descriptivos al inicio del archivo ("ESTE SCRIPT ANALIZA...")
- NO escribas comentarios al final de linea (`x = 5  # contador`)
- Si modificas un archivo que YA tiene comentarios, no agregues nuevos pero deja los existentes intactos
- EXCEPCION UNICA: solo si el usuario los pide EXPLICITAMENTE en esa peticion
- Shebangs (`#!/usr/bin/env python`) y directivas tipo `# -*- coding: utf-8 -*-` SI van porque son sintaxis, no comentarios

### Respuesta corta
- Una "tarea" es cualquier accion que requiera usar herramientas (Write, Edit, Bash, Read, Glob, Grep, etc.)
- Una "pregunta" es cualquier solicitud de informacion o explicacion sin uso de herramientas
- Al completar una TAREA: responde UNICAMENTE la palabra `Listo` — sin resumen, sin lista de cambios, sin contexto
- Al responder una PREGUNTA: responde de forma concisa (maximo 2-3 oraciones), sin detalle excesivo
- Si el usuario pide explicitamente que expliques algo: es una pregunta, responde concisamente
- Esta regla aplica a TODAS las respuestas siguientes en la sesion, incluyendo la confirmacion de LoTokens

### Lectura eficiente
- NO releas archivos que ya estan en tu contexto de la sesion; reutiliza lo que ya cargaste
- Si debes leer un archivo grande (>500 lineas), usa `offset` y `limit` para leer solo lo necesario
- Usa SIEMPRE `head_limit` en `Grep` y `Glob` para acotar resultados (ej. `head_limit: 50`)
- Prefiere `Grep` con patron especifico antes que leer archivos completos
- Si una sola herramienta resuelve la duda, no lances varias redundantes

### Sin sub-agentes triviales
- NO uses `Agent` ni `Explore` para tareas que un `Grep`, `Glob` o `Read` directo resuelve
- Un sub-agente consume TODO su propio contexto: reservalo para busquedas amplias o paralelas reales
- Antes de lanzar un sub-agente, preguntate: ¿puedo resolverlo con una herramienta directa?
- Si la tarea es un solo lookup (un simbolo, un valor, un archivo), hazlo tu mismo

### Sin web innecesaria
- NO uses `WebSearch` ni `WebFetch` a menos que el usuario lo pida o sea estrictamente necesario
- Resuelve primero con el codigo, la documentacion local y tu conocimiento
- Si dudas si vale la pena buscar en web, prefiero NO hacerlo y preguntar al usuario

### Sin verificaciones decorativas
- NO releas un archivo despues de editarlo para "confirmar" que cambio (Edit falla si no coincide)
- NO reejecutes un comando despues de ver su salida exitosa para "asegurarte"
- NO repitas en texto lo que ya se hizo; ve directo a la siguiente accion
- Confia en el resultado que reporta la herramienta

---

## Confirmacion al usuario

IMPORTANTE: Si "Respuesta corta" esta activa, aplica esa regla TAMBIEN a esta confirmacion.

Si "Respuesta corta" esta activa — confirmacion en UNA SOLA linea:
`LoTokens activo — [funciones activas separadas por |] — [guardado/solo sesion]`

Si "Respuesta corta" NO esta activa — confirmacion en una o dos lineas:

Ejemplo con funciones activas y guardado global:
`LoTokens activo — Sin emojis | Respuesta corta`
`Preferencias guardadas en ~/.claude/CLAUDE.md`

Ejemplo solo esta sesion:
`LoTokens activo — Bloquear .md | Sin emojis`

Ejemplo sin ninguna seleccion:
`LoTokens: ninguna funcion activa.`

Aplica las reglas seleccionadas por el resto de la conversacion sin volver a mencionarlas.

---

## English mode

Si en el Paso 0 el idioma elegido es `en`, usa estas versiones en lugar de las de los Pasos 1, 2 y la confirmacion. Al guardar en CLAUDE.md, usa el marcador `<!-- LoTokens-lang: en -->` y escribe los bloques de reglas en ingles que aparecen abajo.

### Step 1 — Main menu

Use AskUserQuestion with multiSelect: true.

Question: "LoTokens — select the features you want to enable:"

Available options (the user may choose several, one, or none):

- **Block .md** — Prevents creating or editing `.md` files during this session (except `CLAUDE.md`, `AGENTS.md`, and `.md` files inside `memory/` or `.claude/`, which are always allowed).
- **No emojis** — Forbids emojis in all your responses and in the code you generate.
- **No comments** — Forbids writing comments in generated code (`#`, `//`, `/* */`, `<!-- -->`, docstrings, decorative headers, etc.).
- **Short reply** — When completing any task (when you use tools), reply only: `Done`
- **Efficient reading** — Don't re-read files already in context, limit `Read` of large files, and always use `head_limit` in `Grep`/`Glob`.
- **No trivial sub-agents** — Block `Agent`/`Explore` when a direct `Grep`, `Glob` or `Read` suffices (a sub-agent consumes its own full context).
- **No unnecessary web** — Use `WebSearch`/`WebFetch` only when the user asks or it is strictly necessary.
- **No decorative checks** — Don't re-read or re-run to "confirm" after editing or running; trust the tool result.

### Step 2 — Persistence

If the user selected at least one feature, use AskUserQuestion (single select):

Question: "Save these preferences for future sessions?"

Options:
- **This session only** — The rules apply to the WHOLE session until it ends. Nothing is written to disk.
- **Save globally** — Writes the rules to the global CLAUDE.md (applies in every project).
- **Save for this project** — Writes the rules to `.claude/CLAUDE.md` (this project only).

Use the same OS path rules and the same "Block .md" + save special case described in the Spanish section.

When saving, write the section keeping the language marker:

```
## LoTokens

<!-- Auto-generated by the LoTokens skill. Edit or delete this section to change preferences. -->
<!-- LoTokens-lang: en -->

[include only the rules for the features the user selected]
```

Rule blocks per feature (write to CLAUDE.md):

**Block .md:**
```
### Block .md
- DO NOT use Write, Edit, or Bash commands that write to `.md` files (`>`, `>>`, `sed -i`, `tee`, `cp`, `mv`, `awk -i inplace`, etc.)
- Includes README.md and any other Markdown
- EXCEPTIONS (always allowed):
  - `CLAUDE.md` (global or project) — to store persistent preferences and instructions
  - `AGENTS.md` — project instructions
  - `MEMORY.md` and any `.md` inside a `memory/` directory — for the auto-memory system
  - Any `.md` inside the `.claude/` directory (plans, MD settings, etc.)
- If another .md write is attempted, reply exactly: `Blocked by LoTokens: .md writing disabled.`
```

**No emojis:**
```
### No emojis
- DO NOT include emojis in text responses
- DO NOT include emojis in code comments, strings, or inline documentation
```

**No comments:**
```
### No comments
- DO NOT write comments of any kind in generated code
- Includes: `#` (Python, shell, YAML), `//` (JS, TS, Go, Java, C/C++, Rust), `/* */`, `<!-- -->` (HTML/XML), `--` (SQL, Lua), `;` (Lisp, ASM)
- DO NOT write Python docstrings (`"""..."""` or `'''...'''`) or JSDoc (`/** */`)
- DO NOT write decorative headers like `# ===== CONFIG =====` or section separators
- DO NOT write "what this file does" comments at the top of the script
- DO NOT write end-of-line comments explaining code (`x = 5  # counter`)
- If you modify an existing file that ALREADY has comments, do not add new ones but leave existing ones intact (unless the user asks to remove them)
- ONLY EXCEPTION: only write comments if the user EXPLICITLY requests them in that specific message
- If the language syntax requires text in a certain position (e.g. shebang `#!/usr/bin/env python`), that is NOT a comment and DOES go in
```

**Short reply:**
```
### Short reply
- A "task" is any action that requires using tools (Write, Edit, Bash, Read, Glob, Grep, etc.)
- A "question" is any request for information or explanation without using tools
- When completing a TASK: reply ONLY the word `Done` — no summary, no change list, no context
- When answering a QUESTION: reply concisely (max 2-3 sentences), without excessive detail
- If the user explicitly asks you to explain something: it is a question, reply concisely
- This rule applies to ALL following responses in the session
```

**Efficient reading:**
```
### Efficient reading
- DO NOT re-read files already in your session context; reuse what you already loaded
- If you must read a large file (>500 lines), use `offset` and `limit` to read only what is needed
- ALWAYS use `head_limit` in `Grep` and `Glob` to bound results (e.g. `head_limit: 50`)
- Prefer a specific `Grep` pattern over reading whole files
- If a single tool answers the question, do not launch several redundant ones
```

**No trivial sub-agents:**
```
### No trivial sub-agents
- DO NOT use `Agent` or `Explore` for tasks a direct `Grep`, `Glob`, or `Read` solves
- A sub-agent consumes its OWN full context: reserve it for genuinely broad or parallel searches
- Before launching a sub-agent, ask: can I solve this with a direct tool?
- If the task is a single lookup (a symbol, a value, a file), do it yourself
```

**No unnecessary web:**
```
### No unnecessary web
- DO NOT use `WebSearch` or `WebFetch` unless the user asks or it is strictly necessary
- Solve first with the code, local docs, and your own knowledge
- If unsure whether a web search is worth it, prefer NOT to do it and ask the user
```

**No decorative checks:**
```
### No decorative checks
- DO NOT re-read a file after editing to "confirm" it changed (Edit fails if it does not match)
- DO NOT re-run a command after seeing its successful output to "make sure"
- DO NOT restate in text what was already done; go straight to the next action
- Trust the result reported by the tool
```

The session rules (apply immediately) are the same as the Spanish "Reglas por funcion (para esta sesion)" section but follow the English wording above.

### Confirmation to the user (English)

IMPORTANT: If "Short reply" is active, apply that rule to this confirmation too.

If "Short reply" is active — confirmation on a SINGLE line:
`LoTokens active — [active features separated by |] — [saved/this session]`

If "Short reply" is NOT active — confirmation on one or two lines:

Example with active features and global save:
`LoTokens active — No emojis | Short reply`
`Preferences saved to ~/.claude/CLAUDE.md`

Example this session only:
`LoTokens active — Block .md | No emojis`

Example with no selection:
`LoTokens: no features active.`

Apply the selected rules for the rest of the conversation without mentioning them again.
