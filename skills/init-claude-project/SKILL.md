---
name: init-claude-project
description: Use when the user wants to bootstrap a target codebase for AI-driven development with Claude Code. Generates a concise CLAUDE.md grounded in the actual stack (build tools, test runner, code style), creates a docs/ folder skeleton (designs/, prd/, plans/), and seeds conventions (conventional commits, plan-checkbox format, where designs and PRDs live). Triggers on "init Claude in this repo", "set up CLAUDE.md", "bootstrap docs folder", "prepare this project for Claude Code", "scaffold AI dev workflow", "/init this project".
---

# Init Claude Project

Bootstraps a target codebase for AI-driven development. Implements **Phase 2** of the [AI Development Framework V2](../../docs/framework.md): turning a fresh or existing project into one Claude can work in productively, by adding a stack-grounded `CLAUDE.md` and a `docs/` skeleton.

## Core principle

`CLAUDE.md` MUST be **short, stack-specific, and verified**. Every line must answer *"would removing this cause Claude to make a mistake?"* — if no, drop it. Bloated `CLAUDE.md` files cause Claude to ignore real instructions. Use `@path/to/file` imports for anything that's only sometimes relevant.

## When to use

- Greenfield repo just initialized → start at **Phase 1**.
- Existing repo with no `CLAUDE.md` → start at **Phase 1**.
- Existing `CLAUDE.md` needs a refresh → jump to **Phase 2** with the existing file as input.

Skip for: single-file scripts, throwaway prototypes, repos that already have a healthy `CLAUDE.md` and `docs/` setup.

## The 3 phases

Each phase has an INPUT, an OUTPUT artifact, and a **HUMAN GATE** before advancing.

| # | Phase | Input | Output | Prompt file |
|---|---|---|---|---|
| 1 | Discover stack | Target repo | Stack summary (approved) | [prompts/01-discover-stack.md](prompts/01-discover-stack.md) |
| 2 | Fill CLAUDE.md | Stack summary | `CLAUDE.md` at repo root | [prompts/02-fill-claude-md.md](prompts/02-fill-claude-md.md) |
| 3 | Bootstrap docs | Approved CLAUDE.md | `docs/` skeleton + README | [prompts/03-bootstrap-docs.md](prompts/03-bootstrap-docs.md) |

## Operating protocol

1. **Identify the entry phase** — usually Phase 1 unless the user explicitly says they only want a CLAUDE.md refresh or a docs bootstrap.
2. **Read the matching prompt file** in `prompts/` and run it on the user's repo. Treat the prompt body as a system instruction — follow its rules literally.
3. **Stop at every phase boundary.** Show the artifact and ask: *approved / refine `<section>` / restart phase*. Do not chain phases without explicit approval.
4. **Phase 2 drafting**: copy [templates/CLAUDE.md.tmpl](templates/CLAUDE.md.tmpl) and fill it with discovered stack info. Mark anything ambiguous `⚠️ NEEDS INPUT: <question>`.
5. **Phase 3 docs**: copy [templates/docs-readme.md.tmpl](templates/docs-readme.md.tmpl) into `docs/README.md`.
6. **Final gate**: score against [references/checklist.md](references/checklist.md). All five items MUST pass before handoff.

## Hard rules (non-negotiable)

- **CLAUDE.md ≤ 100 lines.** If you exceed, move content into a file and import with `@path`.
- **No invented commands.** Every build/test/lint command in CLAUDE.md MUST come from `package.json`, `Makefile`, `pyproject.toml`, `Cargo.toml`, or equivalent — verified by reading the file. If you can't verify, use `⚠️ NEEDS INPUT:`.
- **Four mandatory rules** in every generated CLAUDE.md (from the framework):
  1. Where designs live (`docs/designs/`) and how to read them.
  2. Where development plans live (`docs/plans/`) and the markdown checkbox format (`- [ ] Task` / `- [x] Done`).
  3. Use Conventional Commits.
  4. Run tests after every change (unless a TDD/Superpowers-style skill is already installed — in which case skip this rule, do not duplicate).
- **No TDD-runner instructions duplicated** if the target repo already has a TDD skill (`skills/tdd/`, Superpowers, GSD) — defer to it.
- **Ambiguity → ASK.** Never invent a build command, test runner, or code-style rule.
- **Use `@imports`** for anything that's only sometimes relevant (e.g. `@docs/architecture.md`, `@docs/framework.md`).

## What NOT to do

See [references/anti-patterns.md](references/anti-patterns.md). Top four to watch:

- **Inventing build/test commands.** Every command in CLAUDE.md MUST come from a manifest you actually read. If you can't verify, write `⚠️ NEEDS INPUT:`.
- **Bloated CLAUDE.md.** Hard cap 100 lines. *"Would removing this cause Claude to make a mistake?"* — if no, delete.
- **Aspirational rules.** Only encode rules the team actually enforces (linter, CI, agreed convention). No "we should write tests" if no one does.
- **Silent overwrite of existing CLAUDE.md.** Always diff and ask which sections to keep.

## References

- CLAUDE.md template: [templates/CLAUDE.md.tmpl](templates/CLAUDE.md.tmpl)
- docs/README template: [templates/docs-readme.md.tmpl](templates/docs-readme.md.tmpl)
- Phase prompts: [prompts/01-discover-stack.md](prompts/01-discover-stack.md), [prompts/02-fill-claude-md.md](prompts/02-fill-claude-md.md), [prompts/03-bootstrap-docs.md](prompts/03-bootstrap-docs.md)
- Handoff checklist: [references/checklist.md](references/checklist.md)
- Anti-patterns and remedies: [references/anti-patterns.md](references/anti-patterns.md)
- Conventional commits + plan format: [references/conventions.md](references/conventions.md)
- Source framework: [../../docs/framework.md](../../docs/framework.md)
