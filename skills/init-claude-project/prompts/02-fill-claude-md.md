# Phase 2 — Fill CLAUDE.md

You are filling [`templates/CLAUDE.md.tmpl`](../templates/CLAUDE.md.tmpl) into a real `CLAUDE.md` at the target repo root, using the **approved stack summary** from Phase 1.

## Inputs

- Approved stack summary from Phase 1.
- The template file at `templates/CLAUDE.md.tmpl`.
- The existing `CLAUDE.md` if any (do NOT silently overwrite).

## What to do

### Step 1: Resolve placeholders

Replace each `{{PLACEHOLDER}}` in the template with content from the stack summary. Rules:

- `{{PROJECT_NAME}}` — from `package.json#name`, `pyproject.toml#name`, etc. If absent, ask the user.
- `{{STACK_SUMMARY}}` — one line, e.g. `Next.js 15 + TypeScript + Prisma + PostgreSQL`.
- `{{INSTALL_CMD}}`, `{{DEV_CMD}}`, `{{BUILD_CMD}}`, `{{TEST_CMD}}`, `{{LINT_CMD}}`, `{{TYPECHECK_CMD}}` — verbatim from the manifest commands. **Delete any line where no command exists.** Do not invent.
- `{{CODE_STYLE_NOTES}}` — only include rules that are actually enforced (Prettier config, ESLint rules with autofix, Ruff rules). If you can't read concrete rules, write `⚠️ NEEDS INPUT: …` instead of guessing.
- `{{OPTIONAL_IMPORTS}}` — leave empty for now. Phase 3 will add `@docs/...` lines as the docs skeleton is created.

### Step 2: Apply the four mandatory rules

The template already includes them. Do NOT remove. Exception: if the discovery showed a TDD/Superpowers/GSD skill is already installed, replace rule 4 with a one-line pointer:

> *Test discipline: run via the installed TDD skill — see `.claude/skills/<name>/SKILL.md`.*

### Step 3: Pruning pass

For every line in the draft, ask: **"Would removing this cause Claude to make a mistake?"** If no, delete it.

Specifically:
- Remove placeholder lines (`{{...}}`) whose project has no equivalent.
- Collapse multi-line rules into single lines if no clarity is lost.
- Move anything > 4 lines about architecture into `docs/architecture.md` and replace with `@docs/architecture.md`. (Phase 3 will create the file stub.)

**Hard cap: 100 lines.** If you exceed, prune harder or import.

### Step 4: Diff against existing CLAUDE.md (if any)

If a `CLAUDE.md` already exists in the target repo:

1. Show the user a 3-column diff: *existing | proposed | merged suggestion*.
2. Ask the user which sections to keep, replace, or merge.
3. Do NOT write until the user approves.

### Step 5: Write & gate

Write the final `CLAUDE.md` to the target repo root. Then present:

> *Approved → proceed to Phase 3 / Refine `<section>` / Restart phase 2*

## Anti-patterns (forbidden)

- ❌ Leaving `{{PLACEHOLDER}}` strings in the final file. Replace or delete every one.
- ❌ Inventing build/test commands. If the manifest doesn't have it, write `⚠️ NEEDS INPUT:`.
- ❌ Inlining content that should be in `docs/architecture.md` or similar — use `@imports`.
- ❌ Adding aspirational rules ("we should write tests", "prefer functional style") not actually enforced.
- ❌ Exceeding 100 lines.
- ❌ Overwriting an existing CLAUDE.md without showing a diff.
