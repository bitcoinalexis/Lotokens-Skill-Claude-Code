---

# Skill-LoTokens — Documentacion en Espanol

Una skill para Claude Code que reduce el uso de tokens en tus sesiones. Activa solo lo que necesitas desde un menu interactivo. Guarda tus preferencias para que persistan entre sesiones. Disponible en **español e ingles**.

---

## Seleccion de idioma

La **primera vez** que ejecutas `/lotokens`, la skill te pregunta el idioma de la interfaz (`Español` / `English`). Tu eleccion se guarda como un marcador en el `CLAUDE.md` global, asi que las siguientes veces no se vuelve a preguntar y todo el menu, las opciones y las confirmaciones aparecen en el idioma elegido. Para cambiarlo, borra la linea `<!-- LoTokens-lang: ... -->` de tu `CLAUDE.md` global.

---

## Funciones

| Funcion | Que hace |
|---|---|
| **Bloquear .md** | Impide que Claude cree o edite cualquier archivo `.md` (via Write, Edit o Bash con `>`, `>>`, `sed -i`, `tee`, `cp`, `mv`, etc.). Excepciones: `CLAUDE.md` y `MEMORY.md` / directorio `memory/` permanecen permitidos para no romper preferencias persistentes ni el sistema auto-memory |
| **Sin emojis** | Elimina emojis de todas las respuestas y del codigo generado |
| **Sin comentarios** | Impide que Claude escriba comentarios en el codigo generado (`#`, `//`, `/* */`, docstrings, headers decorativos, etc.) |
| **Respuesta corta** | Al completar cualquier tarea (uso de herramientas), Claude responde unicamente: `Listo` |

Cada funcion es independiente: activa una, varias, o todas.

---

## Instalacion

**Opcion 1 — Global (disponible en todos los proyectos):**

macOS / Linux:
```bash
curl -o ~/.claude/skills/lotokens.md \
  https://raw.githubusercontent.com/AngyLabs/Skill-LoTokens/main/lotokens.md
```

Windows (PowerShell):
```powershell
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/AngyLabs/Skill-LoTokens/main/lotokens.md" `
  -OutFile "$env:USERPROFILE\.claude\skills\lotokens.md"
```

**Opcion 2 — Manual:**

Descarga `lotokens.md` y copialo a:

macOS / Linux:
- `~/.claude/skills/` — instalacion global
- `.claude/skills/` — solo para este proyecto

Windows:
- `%USERPROFILE%\.claude\skills\` — instalacion global
- `.claude\skills\` — solo para este proyecto

---

## Uso

En cualquier sesion de Claude Code, escribe:

```
/lotokens
```

La **primera vez** que ejecutes `/lotokens`, Claude te preguntara el idioma de la interfaz (`Español` / `English`). Tu eleccion se guarda como un marcador en el `CLAUDE.md` global, por lo que las siguientes veces no se vuelve a preguntar y todo el menu, las opciones y las confirmaciones aparecen en el idioma elegido. Para cambiarlo, borra la linea `<!-- LoTokens-lang: ... -->` de tu `CLAUDE.md` global.

Aparecera un menu interactivo. Selecciona las funciones que deseas activar.

Despues de seleccionar, Claude preguntara donde guardar tus preferencias:

| Opcion | Comportamiento |
|---|---|
| **Solo esta sesion** | Las reglas aplican ahora, no se guarda nada en disco |
| **Guardar globalmente** | Las reglas se escriben en `~/.claude/CLAUDE.md` (Linux/macOS) o `%USERPROFILE%\.claude\CLAUDE.md` (Windows) y aplican en todos los proyectos |
| **Guardar para este proyecto** | Las reglas se escriben en `.claude/CLAUDE.md` del directorio actual y aplican solo aqui |

### Como funciona la memoria persistente

Al guardar, Claude escribe un bloque `## LoTokens` en el archivo `CLAUDE.md` correspondiente. Claude Code carga `CLAUDE.md` automaticamente al iniciar cada sesion, por lo que tus reglas quedan activas sin necesidad de volver a ejecutar `/lotokens`.

- Solo se escriben las reglas de las funciones que seleccionaste — las no utilizadas se omiten.
- Si ya existe un bloque `## LoTokens` en el archivo, se reemplaza por completo.
- Para cambiar preferencias: ejecuta `/lotokens` nuevamente y elige nuevas opciones.
- Para eliminar preferencias: borra el bloque `## LoTokens` del `CLAUDE.md` correspondiente, o ejecuta `/lotokens` sin seleccionar ninguna funcion.

**Deteccion de sistema operativo:** Claude detecta tu sistema operativo automaticamente y usa la ruta correcta. En Windows, `%USERPROFILE%` se expande a tu directorio de usuario (por ejemplo `C:\Users\tunombre`).

---

## Ejemplo

```
/lotokens

LoTokens — selecciona las funciones que deseas activar:
  [x] Sin emojis
  [x] Sin comentarios
  [x] Respuesta corta
  [ ] Bloquear .md

Guardar estas preferencias para futuras sesiones?
  > Guardar globalmente (~/.claude/CLAUDE.md)

LoTokens activo — Sin emojis | Sin comentarios | Respuesta corta
Preferencias guardadas en ~/.claude/CLAUDE.md
```

---

## Compatibilidad

- Claude Code CLI
- Claude Code app de escritorio
- Extensiones IDE de Claude Code (VS Code, JetBrains)
- Windows, macOS, Linux

---

Desarrollado para [Claude Code](https://claude.ai/code) por AngyLabs.

# Skill-LoTokens

A Claude Code skill to reduce token usage during your sessions. Activate only what you need from an interactive menu. Optionally save your preferences to persist them across sessions. Available in **Spanish and English**.

---

## Language selection

The **first time** you run `/lotokens`, the skill asks for the interface language (`Español` / `English`). Your choice is saved as a marker in the global `CLAUDE.md`, so it is not asked again on later runs and the whole menu, options, and confirmations appear in the chosen language. To change it, delete the `<!-- LoTokens-lang: ... -->` line from your global `CLAUDE.md`.

---

## Functions

| Function | What it does |
|---|---|
| **Block .md** | Prevents Claude from creating or editing any `.md` file (via Write, Edit, or Bash with `>`, `>>`, `sed -i`, `tee`, `cp`, `mv`, etc.). Exceptions: `CLAUDE.md` and `MEMORY.md` / the `memory/` directory remain allowed so persistent preferences and the auto-memory system keep working |
| **No emojis** | Removes emojis from all responses and generated code |
| **No comments** | Prevents Claude from writing comments in generated code (`#`, `//`, `/* */`, docstrings, decorative headers, etc.) |
| **Short reply** | After completing any task (tool use), Claude replies only: `Done` (in Spanish mode: `Listo`) |

Each function is independent — activate one, several, or all of them.

---

## Installation

**Option 1 — Global (available in all projects):**

macOS / Linux:
```bash
curl -o ~/.claude/skills/lotokens.md \
  https://raw.githubusercontent.com/AngyLabs/Skill-LoTokens/main/lotokens.md
```

Windows (PowerShell):
```powershell
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/AngyLabs/Skill-LoTokens/main/lotokens.md" `
  -OutFile "$env:USERPROFILE\.claude\skills\lotokens.md"
```

**Option 2 — Manual:**

Download `lotokens.md` and copy it to:

macOS / Linux:
- `~/.claude/skills/` — global install
- `.claude/skills/` — local to a specific project

Windows:
- `%USERPROFILE%\.claude\skills\` — global install
- `.claude\skills\` — local to a specific project

---

## Usage

In any Claude Code session, type:

```
/lotokens
```

The **first time** you run `/lotokens`, Claude asks for the interface language (`Español` / `English`). Your choice is saved as a marker in the global `CLAUDE.md`, so it is not asked again on later runs and the whole menu, options, and confirmations appear in the chosen language. To change it, delete the `<!-- LoTokens-lang: ... -->` line from your global `CLAUDE.md`.

An interactive menu will appear. Select the functions you want to activate.

After selecting, Claude will ask where to save your preferences:

| Option | Behavior |
|---|---|
| **This session only** | Rules apply now, nothing is saved to disk |
| **Save globally** | Rules are written to `~/.claude/CLAUDE.md` (Linux/macOS) or `%USERPROFILE%\.claude\CLAUDE.md` (Windows) and apply in all projects |
| **Save for this project** | Rules are written to `.claude/CLAUDE.md` in the current directory and apply only here |

### How persistent memory works

When you choose to save, Claude writes a `## LoTokens` block into the target `CLAUDE.md` file. Claude Code automatically loads `CLAUDE.md` at the start of every session, so your rules are active without running `/lotokens` again.

- Only the rules for the functions you selected are written — unused functions are omitted.
- If a `## LoTokens` block already exists in the file, it is replaced entirely.
- To update preferences: run `/lotokens` again and choose new options.
- To remove preferences: delete the `## LoTokens` block from the corresponding `CLAUDE.md`, or run `/lotokens` and select no functions.

**OS detection:** Claude detects your operating system automatically and uses the correct path. On Windows, `%USERPROFILE%` is expanded to your home directory (e.g. `C:\Users\yourname`).

---

## Example

```
/lotokens

LoTokens — select the functions you want to activate:
  [x] No emojis
  [x] No comments
  [x] Short response
  [ ] Block .md

Save these preferences for future sessions?
  > Save globally (~/.claude/CLAUDE.md)

LoTokens active — No emojis | No comments | Short response
Preferences saved in ~/.claude/CLAUDE.md
```

---

## Compatibility

- Claude Code CLI
- Claude Code desktop app
- Claude Code IDE extensions (VS Code, JetBrains)
- Windows, macOS, Linux

---

Built for [Claude Code](https://claude.ai/code) by AngyLabs.

