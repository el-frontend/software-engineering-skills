---
name: using-agent-skills
description: Use when starting a session, deciding which framework skill applies to the current task, or sequencing them across a feature. Maps the user's intent to one of the five framework skills (ai-driven-prd, init-claude-project, generate-dev-plan, declarative-design, execute-plan) and enforces the cross-skill operating behaviors. Triggers on "which skill should I use", "where do I start", "how do these skills fit together", "I have a PRD now what", "/using-agent-skills".
---

# Using Agent Skills

## Overview

This repository is a skills library that implements the [AI Development Framework V2](../../docs/framework.md). Each skill under `skills/` encodes one phase of the pipeline that turns a stakeholder request into a shipped pull request. **This meta-skill is the dispatcher**: it identifies which framework skill applies to the user's current intent, sequences them when a feature spans multiple phases, and enforces the operating behaviors every skill shares.

Read this skill first when:
- The user describes a goal but does not name a skill.
- Multiple skills could plausibly apply.
- A feature needs to flow end-to-end (PRD → setup → plan → designs → execution).
- You are unsure which phase of the framework you are in.

## Skill Discovery

Map the user's intent to a single skill before doing any other work. Do not begin a phase before confirming the entry point with the user.

```
Task arrives
    │
    ├── Need to author / refine / audit a PRD for AI agents? ─→ ai-driven-prd
    │     (triggers: "write a PRD", "spec this feature",
    │                "audit my PRD", "is this AI-ready")
    │
    ├── Bootstrapping a repo for Claude Code? ─────────────── → init-claude-project
    │     (triggers: "init Claude here", "set up CLAUDE.md",
    │                "scaffold docs folder", "/init this project")
    │
    ├── Have a PRD requirement (FR-N), need an executable plan? → generate-dev-plan
    │     (triggers: "make a plan for FR-001",
    │                "break this PRD into tasks", "/plan FR-N")
    │
    ├── Have a UI requirement, need designs in docs/designs/? → declarative-design
    │     (triggers: "import designs for FR-001",
    │                "vibe design these screens", "/designs FR-N")
    │
    └── Have a plan at docs/plans/<FR-N>.md, need to ship it? → execute-plan
          (triggers: "execute the plan", "implement FR-001",
                     "ship FR-001", "/execute FR-N")
```

If no skill matches, the task is likely too small for the framework — bug fixes under ~50 LoC, exploratory questions, single-file refactors. Handle it directly without invoking a skill. The framework is for non-trivial features.

## Core Operating Behaviors

These behaviors apply across all five skills. They are non-negotiable. RFC 2119 keywords (MUST / MUST NOT / SHOULD / MAY) carry their normal force.

### COB-1 — Ambiguity → ASK

Every skill in this library MUST stop and ask the user when an input is ambiguous. Never invent a value to keep moving. The marker `⚠️ NEEDS INPUT: <specific question>` is reserved for unfilled sections in artifacts.

- Bad: silently picking one interpretation of a vague requirement and writing 200 lines around it.
- Good: *"FR-003 says 'fast search' — do you mean p95 < 200ms, or another threshold?"*

Omission is read as permission; vague adjectives become hallucinations. Surface uncertainty early.

### COB-2 — No Fuzzy Adjectives

The words *fast, easy, intuitive, robust, modern, seamless, scalable, clean, optimized* MUST trigger an immediate rewrite to a quantified, testable threshold. This applies to PRDs, plans, commit messages, and code comments.

- Bad: *"Make the dashboard fast."*
- Good: *"FR-007: dashboard initial render p95 ≤ 800ms on a 1000-row dataset."*

If the user provides a fuzzy adjective in a request, the skill MUST ask for the threshold before drafting.

### COB-3 — Number and Cross-Reference Everything

Every functional requirement, acceptance criterion, plan task, and test MUST have an ID. The cross-reference chain is canonical: **FR-N ↔ AC-N ↔ test name ↔ commit body**. A skill that produces an artifact without IDs MUST be rejected and rerun.

Sub-IDs follow the pattern `FR-N.M` for plan tasks (e.g. `FR-001.2`).

### COB-4 — Stop at Every Phase Boundary

Each skill is divided into numbered phases. Each phase has an INPUT, an OUTPUT artifact, and a **HUMAN GATE**. The skill MUST present the artifact and ask: *approved / refine `<section>` / restart phase*. Phases MUST NOT be chained without explicit approval, even when the user appears to be in a hurry.

The gate is the contract. Skipping it is the most expensive failure mode in this framework.

### COB-5 — Each Phase Ends Runnable + Tested

For implementation-side skills (`generate-dev-plan`, `execute-plan`): every task MUST leave the codebase green — passing tests, passing typecheck, passing lint. Plumbing-only sequences (*"add types now, add logic later"*) are forbidden. If a task cannot end runnable + tested, it MUST be split or merged.

### COB-6 — DO NOT Lists Are Concrete

Every skill in this library has hard rules and anti-patterns. They are concrete forbidden actions, not vibes. When you find yourself wanting to do something the skill forbids, the correct move is to stop and surface the conflict to the user, not to rationalize an exception.

### COB-7 — Push Back When Warranted

When the user's request collides with a skill's hard rules, name the conflict directly and propose the rule-compliant alternative. Sycophancy is a failure mode. *"Of course!"* followed by a 200-line plan that batches three requirements is worse than *"This skill produces one plan per requirement — splitting FR-001 and FR-002 keeps traceability. OK to split?"*

### COB-8 — Verify, Don't Assume

A phase is complete only when its checklist (in `references/checklist.md`) passes. *"Seems right"* is never sufficient. The thresholds are explicit per skill: AI-readiness ≥13/15 (`ai-driven-prd`), init checklist 5/5, dev-plan 7/7, designs 6/6, execution 8/8.

## Failure Modes to Avoid

These are the subtle failures that look like productivity but break the framework. Numbered for cross-reference.

| ID | Failure mode | Where it bites |
|----|--------------|----------------|
| FM-1 | Inventing a value instead of marking `⚠️ NEEDS INPUT` | All skills |
| FM-2 | Chaining phases without the human gate | All skills |
| FM-3 | Writing fuzzy adjectives into requirements or tasks | `ai-driven-prd`, `generate-dev-plan` |
| FM-4 | Missing or broken FR-N ↔ AC-N ↔ test cross-reference | `ai-driven-prd`, `generate-dev-plan`, `execute-plan` |
| FM-5 | Bundling multiple requirements into one plan or one design file | `generate-dev-plan`, `declarative-design` |
| FM-6 | Plumbing-only tasks that don't end runnable + tested | `generate-dev-plan`, `execute-plan` |
| FM-7 | Marking `[x]` with failing tests or in a separate commit | `execute-plan` |
| FM-8 | Bloated CLAUDE.md (>100 lines, aspirational rules, invented commands) | `init-claude-project` |
| FM-9 | Silent scope creep — fixing adjacent code inside a plan's commits | `execute-plan` |
| FM-10 | Stale designs reused after a PRD change | `declarative-design` |
| FM-11 | Auto-opening a PR instead of producing a description for the user | `execute-plan` |
| FM-12 | Treating a user's *"go ahead"* on phase N as approval for phase N+1 | All skills |

## Skill Rules

1. **Check the framework before starting non-trivial work.** If the task spans more than a single file or could surface ambiguity, identify the entry skill first via the Skill Discovery tree.

2. **Skills are workflows, not suggestions.** Follow the phase order in each `SKILL.md`. Do not skip the human gate, the checklist, or the artifact filename convention.

3. **Multiple skills compose.** A full feature flows through `ai-driven-prd` → (`init-claude-project` if first time in this repo) → `generate-dev-plan` → `declarative-design` (if UI) → `execute-plan`. Each skill hands off a named artifact at a known path.

4. **One skill at a time.** Do not run `generate-dev-plan` and `execute-plan` interleaved. Finish the upstream artifact and clear its gate before invoking the next skill.

5. **When in doubt, start with the PRD.** If the task is non-trivial and there is no PRD or an unclear one, begin with `ai-driven-prd`.

## Lifecycle Sequence

For a complete feature, the canonical sequence is:

```
1. ai-driven-prd          → docs/prd/prd-<slug>.md       (FRs, ACs, tasks, ≥13/15)
2. init-claude-project    → CLAUDE.md + docs/ skeleton    (one-time per repo)
3. generate-dev-plan      → docs/plans/<FR-N>.md          (one per requirement)
4. declarative-design     → docs/designs/<FR-N>-<slug>.{html,png}  (UI only)
5. execute-plan           → commits + PR description       (loops one task at a time)
```

Not every task needs every skill:

- **Backend-only feature** — skip step 4.
- **Greenfield repo** — start at step 2 (init), then 1.
- **Existing PRD, no plan yet** — skip steps 1 and 2; start at step 3.
- **Plan exists, ready to ship** — start at step 5.
- **Bug fix < 50 LoC** — skip the framework; just fix it.

The artifact paths are canonical because the downstream skills rely on them. Do not relocate `docs/prd/`, `docs/plans/`, or `docs/designs/` without updating every skill that points there.

## Quick Reference

| Phase | Skill | One-line summary | Output artifact | Pass threshold |
|-------|-------|------------------|-----------------|----------------|
| 1 | [ai-driven-prd](../ai-driven-prd/SKILL.md) | Stakeholder request → AI-readable PRD with ACs and tasks | `docs/prd/prd-<slug>.md` | ≥ 13/15 |
| 2 | [init-claude-project](../init-claude-project/SKILL.md) | Bootstrap CLAUDE.md and `docs/` from the actual stack | `CLAUDE.md`, `docs/README.md` | 5/5 |
| 3 | [generate-dev-plan](../generate-dev-plan/SKILL.md) | One PRD requirement → checkbox plan of vertical slices | `docs/plans/<FR-N>.md` | 7/7 |
| 4 | [declarative-design](../declarative-design/SKILL.md) | UI requirement → HTML + PNG pairs traceable to ACs | `docs/designs/<FR-N>-<slug>.{html,png}` | 6/6 |
| 5 | [execute-plan](../execute-plan/SKILL.md) | Plan + designs → one-task-per-commit implementation + PR | Commits + PR description | 8/8 |

## What NOT to do

- **DO NOT invoke a skill without confirming the entry phase.** The user's wording often implies a different phase than what they actually have. Ask which artifacts already exist.
- **DO NOT chain skills automatically.** Each skill ends at a human gate; the next skill begins only when the user opens it.
- **DO NOT translate this skill's rules into a different framework.** If the user wants a different process (Superpowers, GSD, plain TDD), say so and stop — do not silently graft this framework onto another.
- **DO NOT invent a sixth skill on the fly.** If the task does not fit any of the five, surface that to the user.
- **DO NOT bypass `⚠️ NEEDS INPUT`.** It exists so the user sees exactly which decision is theirs.

## References

- Source framework: [../../docs/framework.md](../../docs/framework.md)
- Authoring conventions for this library: [../../CLAUDE.md](../../CLAUDE.md)
- Skill SKILL.md files: [ai-driven-prd](../ai-driven-prd/SKILL.md), [init-claude-project](../init-claude-project/SKILL.md), [generate-dev-plan](../generate-dev-plan/SKILL.md), [declarative-design](../declarative-design/SKILL.md), [execute-plan](../execute-plan/SKILL.md)
- Anthropic agent-skills overview: https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview
