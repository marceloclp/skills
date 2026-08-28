# Marcelo's agent skills

Reusable agent skills maintained by [@marceloclp](https://github.com/marceloclp).

## Install

List the available skills:

```bash
npx skills add marceloclp/skills --list
```

Install a specific skill globally for Codex:

```bash
npx skills add marceloclp/skills --skill <skill-name> --global --agent codex
```

Install all skills globally for Codex:

```bash
npx skills add marceloclp/skills --skill '*' --global --agent codex
```

## Repository layout

Each skill is a directory under `skills/` with a required `SKILL.md` entry point:

```text
skills/
└── <skill-name>/
    ├── SKILL.md
    ├── scripts/       # optional
    ├── references/    # optional
    └── assets/        # optional
```

Create a new skill locally:

```bash
cd skills
npx skills init <skill-name>
```

Use lowercase letters, digits, and hyphens for skill names. The `SKILL.md` frontmatter must contain a matching `name` and a concise `description` explaining both what the skill does and when to use it.

## Update installed skills

```bash
npx skills update
```
