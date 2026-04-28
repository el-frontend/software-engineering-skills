# Per-Tool Notes and Framework Matrix

Each AI coding agent has its own conventions for the root context file. The PRD itself always lives in `docs/prd/` or `specs/` and is **referenced** from the root context file — never duplicated.

## Claude Code (Anthropic)

- Root context: `CLAUDE.md` in repo root, **<300 lines**, auto-loaded each session.
- Generate with `/init`.
- Use **Plan Mode** (Explore → Plan → Code → Commit) before editing.
- Subagents in `.claude/agents/*.md` for parallel isolated work.
- `/effort low|medium|high|max` controls thinking budget. (The legacy `ultrathink` keyword was deprecated in January 2026 in favor of adaptive reasoning.)
- Skills live in `.claude/skills/<name>/SKILL.md` (this skill is one of them).

## Cursor

- Root context: `.cursor/rules/*.mdc` with YAML frontmatter (`description`, `globs`, `alwaysApply`).
- Four rule types: **Always**, **Auto Attached**, **Agent Requested**, **Manual**.
- Keep each rule **<500 lines**.
- `.cursorrules` (single-file legacy) — avoid for new projects.
- Use `@file`, `@folder`, `@docs` for references inside rules.

## GitHub Copilot

- Root context: `.github/copilot-instructions.md`.
- Path-specific rules in `.github/instructions/*.instructions.md` with frontmatter `applyTo: <glob>`.
- Reusable prompts as `*.prompt.md`.
- Style: one declarative sentence per instruction; add reasoning only when the case isn't obvious.

## Windsurf / Codeium Cascade

- Root context: `.windsurf/rules/*.md` (modern) or `.windsurfrules` (legacy).
- Bullets + Markdown + XML tags. The official docs explicitly endorse `<coding_guidelines>…</coding_guidelines>`.
- **Memories are local and not shareable** — write rules instead if you want the team to see them.

## Aider

- Root context: `CONVENTIONS.md`, loaded with `/read CONVENTIONS.md` or `aider --read` (marked read-only and cacheable).
- `/architect` for reasoning, `/ask` for analysis.
- Persist defaults in `.aider.conf.yml`.

## Cross-tool

- `AGENTS.md` in the repo root is the emerging cross-tool standard, recognized by Cursor, Copilot, and Windsurf (gaining traction in Claude Code).
- When possible, use `AGENTS.md` as the single cross-agent context file and have tool-specific files delegate to it.

---

## Framework matrix — when to use which

| Framework | Use it if… | Avoid if… |
|---|---|---|
| **GitHub Spec Kit** | You want an agent-agnostic workflow with governance (constitution) and quality tooling (`/analyze`, `/checklist`) | Project is very small; the overhead doesn't pay |
| **BMAD-METHOD** | Mid-size team; you like the multi-persona model (Analyst / PM / Architect / SM / Dev / QA) and need deep planning | Solo flow; separate personas just add friction |
| **Kiro** | You want an IDE with integrated spec mode, native EARS syntax, and on-save hooks | You're locked into VS Code / JetBrains |
| **Agent OS** | You have many projects with shareable standards; you want a `standards/product/specs` hierarchy | Single project; profile overhead doesn't pay |
| **snarktank/ai-dev-tasks** | You want the minimum viable: three prompts and you're done; ideal entry point | You need cross-feature governance, constitution, shared standards |
| **Tessl** | You're ready for spec-as-source (generated code) and accept the blast radius | Large existing codebase; migration is expensive |

**Recommended starter combo:** snarktank/ai-dev-tasks (the three prompts) + Spec Kit (constitution + `/checklist`) + the master template in this skill. Covers ~80% of the value without excessive overhead. This skill packages exactly that combination.
