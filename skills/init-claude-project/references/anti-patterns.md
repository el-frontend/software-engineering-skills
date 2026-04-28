# Init-Claude-Project Anti-Patterns

Common failure modes when bootstrapping a target repo for Claude Code, with remedies.

## 1. Inventing build/test commands

**Symptom.** `CLAUDE.md` lists `pnpm build` but `package.json` has no `build` script. Or `pytest tests/` when the project uses `unittest`.

**Why it's bad.** Claude reads CLAUDE.md every session. An invented command runs nothing or runs the wrong thing. The agent thinks tests passed when nothing executed.

**Remedy.** Every command line in CLAUDE.md MUST come from a manifest you read in Phase 1. If you can't verify a command exists, write `⚠️ NEEDS INPUT:` and the user resolves it. Never default to "the thing this kind of project usually does."

---

## 2. Bloated CLAUDE.md

**Symptom.** CLAUDE.md is 200+ lines covering every architectural decision, code style nit, and team norm.

**Why it's bad.** CLAUDE.md loads every session. Bloat causes Claude to ignore real instructions — the framework PDF says it directly: *"Bloated CLAUDE.md files cause Claude to ignore your real instructions."*

**Remedy.** Hard cap of 100 lines (per the skill's checklist). For every line, ask: *"Would removing this cause Claude to make a mistake?"* If no, delete. Move long-form content to `docs/architecture.md`, `docs/conventions.md`, etc., and import via `@path/to/file`.

---

## 3. Aspirational rules

**Symptom.** CLAUDE.md says *"we should write tests"*, *"prefer functional style"*, *"keep files under 200 lines"* — but the team doesn't actually enforce any of these.

**Why it's bad.** Claude treats CLAUDE.md as ground truth. Rules that don't reflect the codebase create thrash: Claude rewrites code to match the rule, the team rejects the change, the rule sits in CLAUDE.md anyway.

**Remedy.** Only encode rules that are actually enforced (linter, CI check, team agreement with bite). If the team aspires to a rule but doesn't enforce it, that's a separate conversation — don't smuggle it into CLAUDE.md.

---

## 4. Duplicated TDD instructions

**Symptom.** Project already has a TDD/Superpowers/GSD skill installed. CLAUDE.md *also* says *"Run tests after every change."*

**Why it's bad.** Two sources of truth for the same rule. When they drift, Claude follows the more recent or more specific one — unpredictable.

**Remedy.** If a TDD-driven skill is installed, defer to it. CLAUDE.md replaces the run-tests rule with a one-liner: *"Test discipline: see `.claude/skills/<name>/SKILL.md`."*

---

## 5. Silent overwrite of existing CLAUDE.md

**Symptom.** Repo had a CLAUDE.md. Skill ran without diffing. User loses sections they spent time crafting.

**Why it's bad.** Destroys user work without consent. Erodes trust in the skill.

**Remedy.** Phase 2 explicitly requires a diff if `CLAUDE.md` exists. Show the user existing | proposed | merged, ask which sections to keep. Never write without approval when overwriting.

---

## 6. Inlining content that belongs in docs/

**Symptom.** CLAUDE.md contains a 40-line section on the data model, or a 30-line auth flow description.

**Why it's bad.** Same as bloat (#2). Plus: if architecture changes, CLAUDE.md goes stale and Claude operates on outdated info every session.

**Remedy.** Move architecture, ADRs, and per-domain docs into `docs/architecture.md` etc. Reference from CLAUDE.md with `@docs/architecture.md`. Imports load on-demand without inflating every session.

---

## 7. Empty docs/ folders without `.gitkeep`

**Symptom.** Skill creates `docs/designs/`, `docs/prd/`, `docs/plans/` but they don't show up in `git status` because they're empty.

**Why it's bad.** Convention is broken before the project even starts. The next user clones and sees a missing folder.

**Remedy.** Add a `.gitkeep` (or short `README.md`) in each subfolder so the empty dirs commit. Phase 3 calls this out — don't skip.

---

## 8. Pre-filling docs/prd/ or docs/plans/ with examples

**Symptom.** Skill drops a `docs/prd/example-prd.md` so the user "knows what to do."

**Why it's bad.** Sets a precedent that those folders contain templates rather than real artifacts. Real PRDs and plans are produced by `ai-driven-prd` and `generate-dev-plan` — let those skills be the source.

**Remedy.** Leave `docs/prd/` and `docs/plans/` empty (with `.gitkeep`). The `docs/README.md` documents the convention; that's enough.

---

## 9. Skipping the existing-CLAUDE.md check

**Symptom.** Phase 1 doesn't notice that `CLAUDE.md` already exists. Phase 2 generates a fresh one. User loses prior work.

**Why it's bad.** Same as #5 but caught later — wasted Phase 2 work plus the same trust hit.

**Remedy.** Phase 1's discovery step explicitly checks for `CLAUDE.md` and `.claude/`. Don't enter Phase 2 without that check confirmed.

---

## 10. Over-broad import statements

**Symptom.** CLAUDE.md has `@docs/architecture.md`, `@docs/conventions.md`, `@docs/runbook.md`, `@docs/glossary.md`, `@docs/api.md`, …

**Why it's bad.** Imports load every session. Importing 5 files is the same as inlining their content — back to bloat.

**Remedy.** Only import files that apply to *every* session. For "sometimes-relevant" content, leave it un-imported and let Claude open it on demand when needed.
