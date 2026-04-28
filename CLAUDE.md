# CLAUDE.md

## Purpose

This repo is a **skills library**, not an application. Each folder under `skills/` is an independent agent skill. The skills encode the [AI Development Framework V2](docs/framework.md) — see that document for the underlying philosophy and the 5-phase pipeline this library targets.

Skills follow Anthropic's [agent-skills format](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview).

## Source of truth

@docs/framework.md

## Skill-authoring conventions

When adding or editing a skill in this repo, follow these rules. Mirror the structure of [`skills/ai-driven-prd/`](skills/ai-driven-prd/) — it is the reference implementation.

### Folder layout

```
skills/<skill-name>/
├── SKILL.md          (required — entry point)
├── prompts/          (optional — phase-specific instructions)
├── templates/        (optional — scaffolds the skill fills in)
└── references/       (optional — checklists, anti-patterns, examples)
```

### SKILL.md frontmatter

Exactly two keys: `name` and `description`. The description MUST start with "Use when…" and MUST list trigger phrases the user is likely to say. Example:

```yaml
---
name: ai-driven-prd
description: Use when the user wants to author, refine, or audit a PRD for AI coding agents. Triggers on "write a PRD", "spec this feature", "audit my PRD".
---
```

### Hard rules for skill content

- **RFC 2119 keywords** (MUST / MUST NOT / SHOULD / MAY) on every normative statement.
- **Numbered IDs** for every requirement, acceptance criterion, user story, and task. Cross-reference FR-N ↔ AC-N ↔ test name.
- **No fuzzy adjectives.** *Fast, easy, intuitive, robust, modern, seamless* trigger an immediate rewrite to a quantified threshold.
- **DO NOT lists** — concrete forbidden actions, not vibes.
- **Each phase ends at a human gate.** Do not chain phases without explicit user approval.
- **Ambiguity → ASK.** Mark unfilled sections `⚠️ NEEDS INPUT:` with the specific question. Never invent.
- **Anti-patterns file** for every skill that has hard rules.
- **Checklist file** when there is a quality gate (e.g. `references/checklist.md` with a pass threshold like ≥13/15).

### Tone

Prescriptive, engineering-grade. Write for an LLM with short memory and dated training data — explicit > clever, structure > prose.

## Commit convention

Use [Conventional Commits](https://www.conventionalcommits.org/). Examples:

- `feat(skills): add init-claude-project skill`
- `docs(framework): translate Phase 4 to English`
- `fix(ai-driven-prd): correct phase 6 checklist threshold`
