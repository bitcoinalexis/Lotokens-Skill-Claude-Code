---
name: lotokens
description: Reduce token usage and enforce response/output constraints in Codex sessions. Use when the user asks to activate LoTokens, wants shorter replies, wants to block writing Markdown files, disable emojis, disable code comments, or persist these preferences in AGENTS.md for the current project or globally.
---

# LoTokens for Codex

Use this skill when the user asks for LoTokens or asks to enforce any of its rules.

This Codex adaptation preserves the original behavior, but uses `AGENTS.md` instead of `CLAUDE.md`.

## Persistence targets

- Project scope: `AGENTS.md` in the current workspace root
- Global scope: `$HOME/.agents/AGENTS.md`

If the global file does not exist, create it.

## Interaction flow

1. Determine the interface language.
2. Ask which LoTokens functions to activate.
3. If at least one function is selected, ask whether to apply only in this session, save globally, or save for this project.
4. Apply the selected rules immediately for the current session.
5. If persistence was requested, write or replace the `## LoTokens` section in the target `AGENTS.md`.

If `request_user_input` is available, use it.
If it is not available, ask concise plain-text questions directly.

## Language

Check whether the target `AGENTS.md` already contains one of these markers inside a `## LoTokens` section:

- `<!-- LoTokens-lang: es -->`
- `<!-- LoTokens-lang: en -->`

If a marker exists, use it and do not ask again.
If no marker exists, ask the user to choose `es` or `en`.

## Functions

Offer these functions:

- `Bloquear .md` / `Block .md`
- `Sin emojis` / `No emojis`
- `Sin comentarios` / `No comments`
- `Respuesta corta` / `Short reply`

The user may enable any combination, including none.

## Writing rules

When the user asks to persist preferences, write or replace this section in the target `AGENTS.md` and include only the selected rule blocks:

```md
## LoTokens

<!-- Generado automaticamente por la skill LoTokens. Edita o elimina esta seccion para cambiar las preferencias. -->
<!-- LoTokens-lang: es -->

[selected rule blocks only]
```

Use the English marker and English rule text when the chosen language is `en`.

## Rule blocks in Spanish

### Bloquear .md
- NO uses Write ni Edit sobre archivos con extension `.md`
- Incluye README.md y cualquier otro Markdown
- EXCEPCION: puedes editar AGENTS.md unicamente cuando el usuario invoca LoTokens para guardar preferencias
- Si se intenta escribir otro .md, responde exactamente: `Bloqueado por LoTokens: escritura de .md desactivada.`

### Sin emojis
- NO incluyas emojis en respuestas de texto
- NO incluyas emojis en comentarios de codigo, strings ni documentacion inline

### Sin comentarios
- NO escribas comentarios de ningun tipo en el codigo generado
- Incluye: `#` (Python, shell, YAML), `//` (JS, TS, Go, Java, C/C++, Rust), `/* */`, `<!-- -->` (HTML/XML), `--` (SQL, Lua), `;` (Lisp, ASM)
- NO escribas docstrings de Python (`"""..."""` o `'''...'''`) ni JSDoc (`/** */`)
- NO escribas headers decorativos tipo `# ===== CONFIGURACION =====` ni separadores de seccion
- NO escribas comentarios de "que hace este archivo" al inicio del script
- NO escribas comentarios al final de linea explicando codigo (`x = 5  # contador`)
- Si modificas un archivo existente que YA tiene comentarios, no agregues nuevos pero deja los existentes intactos, salvo que el usuario pida quitarlos
- EXCEPCION UNICA: solo escribe comentarios si el usuario los solicita explicitamente en esa peticion concreta
- Si la sintaxis del lenguaje exige texto en cierta posicion, eso no cuenta como comentario

### Respuesta corta
- Una tarea es cualquier accion que requiera usar herramientas
- Una pregunta es cualquier solicitud de informacion o explicacion sin uso de herramientas
- Al completar una tarea: responde unicamente la palabra `Listo`
- Al responder una pregunta: responde de forma concisa, en 2 o 3 oraciones como maximo
- Si el usuario pide explicitamente que expliques algo, tratala como pregunta
- Esta regla aplica a todas las respuestas siguientes en la sesion

## Rule blocks in English

### Block .md
- DO NOT use Write or Edit on files with the `.md` extension
- Includes README.md and any other Markdown
- EXCEPTION: you may edit AGENTS.md only when the user invokes LoTokens to save preferences
- If another `.md` write is attempted, reply exactly: `Blocked by LoTokens: .md writing disabled.`

### No emojis
- DO NOT include emojis in text responses
- DO NOT include emojis in code comments, strings, or inline documentation

### No comments
- DO NOT write comments of any kind in generated code
- Includes: `#` (Python, shell, YAML), `//` (JS, TS, Go, Java, C/C++, Rust), `/* */`, `<!-- -->` (HTML/XML), `--` (SQL, Lua), `;` (Lisp, ASM)
- DO NOT write Python docstrings (`"""..."""` or `'''...'''`) or JSDoc (`/** */`)
- DO NOT write decorative headers or section separators
- DO NOT write file-introduction comments
- DO NOT write end-of-line comments
- If you modify an existing file that already has comments, do not add new ones unless the user explicitly asks for comments
- If the language syntax requires text in a certain position, that does not count as a comment

### Short reply
- A task is any action that requires tools
- A question is any request for information or explanation without tools
- When completing a task: reply only with `Done`
- When answering a question: reply concisely in at most 2 or 3 sentences
- If the user explicitly asks for an explanation, treat it as a question
- This rule applies to all following responses in the session

## Session behavior

After the user selects features, obey them immediately for the rest of the session.

If `Respuesta corta` or `Short reply` is active, use that rule for the activation confirmation too.

## Confirmation

If short reply is active, confirm in one line only:

- Spanish: `LoTokens activo - [funciones] - [alcance]`
- English: `LoTokens active - [features] - [scope]`

If short reply is not active, confirm in one or two lines and mention where preferences were saved when applicable.
