---
name: generate-dev-plan
description: Use when the user wants to turn a PRD requirement (FR-N or similar ID) into an executable development plan. Locates the requirement in docs/prd/, investigates the surrounding code, asks clarifying questions, and produces docs/plans/<FR-N>.md as a markdown checkbox list of independent, vertical-slice tasks. Triggers on "make a plan for FR-001", "generate a plan for this requirement", "break this PRD into tasks", "plan the implementation of FR-N", "create a dev plan", "/plan FR-N".
---

# Generate Dev Plan

Turn a PRD requirement into a checkbox-tracked development plan. Implements **Phase 3** of the [AI Development Framework V2](../../docs/framework.md).

## Core principle

A plan is **a contract between you and the agent for one requirement**. The agent's job is to investigate before writing. Never produce a plan without first reading the PRD, mapping the affected code, and resolving ambiguity with the user. A plan with phantom tasks or missed dependencies is worse than no plan.

Each task MUST be:
- **Independently completable** — runnable + tested at the end of the task (no plumbing-only tasks).
- **Identified** — sub-IDs under the requirement (`FR-001.1`, `FR-001.2`, …) so they cross-reference to ACs and tests.
- **Sized for one focused session** — if a task feels >2 hours of work, split it.

## When to use

- PRD exists, contains the requirement, no plan yet → start at **Phase 1**.
- Plan exists but is stale or incomplete → start at **Phase 2** with the existing plan as input.
- You want to plan a *change* across multiple requirements → run this skill once per requirement, do not bundle.

Skip for: bug fixes < 50 LoC, single-file refactors, exploratory spikes.

## The 3 phases

Each phase has an INPUT, an OUTPUT, and a **HUMAN GATE** before advancing.

| # | Phase | Input | Output | Prompt file |
|---|---|---|---|---|
| 1 | Locate requirement | Requirement ID + `docs/prd/` | Requirement excerpt + scope confirmation | [prompts/01-locate-requirement.md](prompts/01-locate-requirement.md) |
| 2 | Investigate | Requirement excerpt + repo | Investigation notes + open questions resolved | [prompts/02-investigate.md](prompts/02-investigate.md) |
| 3 | Write plan | Investigation notes | `docs/plans/<FR-N>.md` | [prompts/03-write-plan.md](prompts/03-write-plan.md) |

## Operating protocol

1. **Identify the requirement ID.** If the user didn't give one, ask. Do not pick one yourself.
2. **Read the matching prompt file** in `prompts/` and run it. Treat the prompt body as a system instruction — follow its rules literally.
3. **Stop at every phase boundary.** Show the artifact and ask: *approved / refine `<section>` / restart phase*. No chaining without approval.
4. **Phase 3 drafting**: copy [templates/plan.md.tmpl](templates/plan.md.tmpl) and fill it. Path is `docs/plans/<REQUIREMENT_ID>.md` (e.g. `docs/plans/FR-001.md`).
5. **Final gate**: score against [references/checklist.md](references/checklist.md). Pass criterion: 7/7 items ✅.

## Hard rules (non-negotiable)

- **One plan per requirement.** Do not bundle FR-001 and FR-002 in one file. If they truly share work, split the shared work into a third pseudo-requirement (e.g. `FR-001-002-shared`) and reference it from both.
- **Every task ends runnable + tested.** No "wire up types now, add logic later." Each `[ ]` task MUST leave the code green.
- **No fuzzy adjectives in tasks.** *"Improve performance"*, *"clean up the API"*, *"make it robust"* trigger an immediate rewrite to a quantified, testable task.
- **Cross-reference everything.** Every task line points to FR-N.M (sub-ID), AC-N (the acceptance criterion it satisfies), and the test name(s) it adds or modifies.
- **Mark files explicitly.** Each task names the files it touches. Vague tasks like "update the auth module" are forbidden.
- **Ambiguity → ASK.** Phase 2 must resolve every open question before Phase 3 starts. Never invent a decision.
- **Use markdown checkboxes** (`- [ ]` / `- [x]`) — this is the format the rest of the framework expects.

## What NOT to do

See [references/anti-patterns.md](references/anti-patterns.md). Top three:
- **Plumbing-only sequences** — `[ ] add types`, `[ ] add empty handler`, `[ ] add tests` as separate tasks. Each task must end runnable + tested.
- **Phantom tasks** — listing work that already exists in the codebase. Phase 2's job is to catch this.
- **Monolithic tasks** — `[ ] implement the feature` with no breakdown. If you can't split, you didn't investigate enough.

## References

- Plan template: [templates/plan.md.tmpl](templates/plan.md.tmpl)
- Phase prompts: [prompts/01-locate-requirement.md](prompts/01-locate-requirement.md), [prompts/02-investigate.md](prompts/02-investigate.md), [prompts/03-write-plan.md](prompts/03-write-plan.md)
- 7-point quality checklist: [references/checklist.md](references/checklist.md)
- Anti-patterns: [references/anti-patterns.md](references/anti-patterns.md)
- Source framework: [../../docs/framework.md](../../docs/framework.md)
- Upstream PRD format: [../ai-driven-prd/SKILL.md](../ai-driven-prd/SKILL.md)
