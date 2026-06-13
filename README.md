# LoTokens

LoTokens es una skill para reducir consumo de tokens y aplicar reglas de salida en sesiones de agentes de código.

Ahora el repositorio incluye:

- versión original para Claude
- adaptación para Codex con `SKILL.md`
- página web estática para GitHub Pages

## GitHub

- Repositorio: [bitcoinalexis/Lotokens-Skill-Claude-Code](https://github.com/bitcoinalexis/Lotokens-Skill-Claude-Code)
- Skill Codex: [SKILL.md](https://github.com/bitcoinalexis/Lotokens-Skill-Claude-Code/blob/main/SKILL.md)

## Qué hace

LoTokens permite activar reglas como:

- bloquear escritura de `.md`
- quitar emojis
- evitar comentarios en código generado
- usar respuestas muy cortas

## Estructura recomendada del repo

```text
SKILL.md
agents/
  openai.yaml
lotokens.md
AGENTS.md
README.md
index.html
```

## Archivos mínimos para Codex

Para que funcione como skill de Codex deben existir como mínimo:

- `SKILL.md`
- `agents/openai.yaml`

## Instalación en Codex

Copia la carpeta del repositorio dentro de una carpeta llamada `lotokens` en:

```text
~/.codex/skills/
```

En Windows normalmente:

```text
C:\Users\TU_USUARIO\.codex\skills\
```

Después reinicia Codex para que detecte la skill.

## Instalación en Claude

El archivo original sigue disponible:

- `lotokens.md`

## GitHub Pages

Este repositorio incluye `index.html` para publicar una web simple desde GitHub Pages.

Si usas Pages desde la rama principal, la web quedará disponible en una URL parecida a:

```text
https://bitcoinalexis.github.io/Lotokens-Skill-Claude-Code/
```

## Compatibilidad

- Codex
- Claude Code
- Windows
- Linux
- macOS
