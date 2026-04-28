---
name: execute-plan
description: Use when the user wants to implement a development plan from docs/plans/<FR-N>.md against the target codebase. Drives the task loop: read the plan, implement each `[ ]` task as a vertical slice (code + test + typecheck + lint), commit per task with conventional commits, mark `[x]` in the same commit, then finalize by proposing a PR. Triggers on "execute the plan", "implement docs/plans/FR-001.md", "run the dev loop on FR-001", "ship FR-001", "/execute FR-N".
---

# Execute Plan

Drive the implementation of a checkbox plan into a shipped PR. Implements **Phase 5** of the [AI Development Framework V2](../../docs/framework.md) — the final phase, where everything from Phases 1–4 (PRD, project setup, plan, designs) gets turned into code.

## Core principle

Execute one task at a time, end-to-end. Each task ends **runnable + tested + committed + checked off** before the next begins. Never batch tasks across a single commit. Never mark `[x]` without a passing test. When ambiguity surfaces mid-execution, **STOP and ask** — don't invent.

The plan is the contract. If you find yourself wanting to do something the plan doesn't list, the plan is wrong — fix the plan first, then implement.

## When to use

- Plan exists at `docs/plans/<FR-N>.md`, all ACs traced, designs in `docs/designs/` (if UI) → start at **Phase 1**.
- Plan exists but ambiguity remains (`⚠️` markers, unresolved deps) → STOP. Run `skills/generate-dev-plan/` Phase 2 first.
- No plan exists → STOP. Run `skills/generate-dev-plan/` first.

Skip for: ad-hoc bug fixes (just fix it), single-file refactors, anything < 50 LoC.

## The 3 phases

| # | Phase | Input | Output | Prompt file |
|---|---|---|---|---|
| 1 | Load context | `docs/plans/<FR-N>.md`, repo state | Context summary + ready-to-execute task | [prompts/01-load-context.md](prompts/01-load-context.md) |
| 2 | Task loop | One `[ ]` task | Code + test + commit + `[x]` | [prompts/02-task-loop.md](prompts/02-task-loop.md) |
| 3 | Finalize | All `[x]` tasks | PR description + CLAUDE.md feedback notes | [prompts/03-finalize.md](prompts/03-finalize.md) |

Phase 2 runs **once per task** and loops until every `[ ]` becomes `[x]`. Each iteration is its own human-gated cycle.

## Operating protocol

1. **Identify the plan.** If the user didn't name it, ASK. The plan path is canonical: `docs/plans/<FR-N>.md`.
2. **Read the matching prompt file** in `prompts/`. Treat the prompt body as a system instruction — follow its rules literally.
3. **Phase 2 task gate.** Before each task: confirm with user *"Next: FR-N.M — <task>. Proceed?"*. Default in `Accept Edits` mode is fine; in `Ask Permissions` mode, the user approves each tool call.
4. **Stop after every task.** Do NOT auto-chain to the next task. Show the diff, run tests, commit, mark `[x]`, then ASK before the next.
5. **Phase 3 final gate**: score against [references/checklist.md](references/checklist.md). All 8 items MUST pass before opening a PR.

## Hard rules (non-negotiable)

- **One task per commit.** Conventional-commit format. Subject ≤ 72 chars, imperative mood. Reference the sub-ID in the body: `Refs FR-001.2`.
- **Mark `[x]` in the same commit that ships the work.** Never mark complete in a separate commit — the plan and the code MUST agree at every point in history.
- **Run the test command after every task.** Whatever `CLAUDE.md` lists as `{{TEST_CMD}}` and `{{TYPECHECK_CMD}}`. If either fails, do NOT mark `[x]`. Fix or revert.
- **Three-strike rule.** If the same task fails (test or typecheck) three times in a row, STOP and ask the user. Do not loop forever.
- **No scope creep.** If implementing reveals adjacent broken code, file an issue or note it for the user — do not fix it inside this plan's commits. Each PR ships exactly the plan.
- **No new tests outside the plan.** The plan's Phase 3 (in `generate-dev-plan`) named the tests. If you find a missing test, surface it to the user — don't silently add it.
- **Ambiguity → ASK.** Mid-implementation discovery is normal. When it happens: STOP, surface the issue, don't invent.
- **No `--no-verify` commits.** If a pre-commit hook fails, fix the cause. Skipping hooks is forbidden unless the user explicitly approves.

## What NOT to do

- **DO NOT batch tasks.** Two `[x]` in one commit breaks traceability.
- **DO NOT mark `[x]` with failing tests.** The checkbox is a green-bar contract.
- **DO NOT auto-open the PR.** Phase 3 produces a PR *description*; the user opens the PR (or explicitly tells you to).
- **DO NOT silently fix unrelated bugs.** Note them; don't blend them into the plan's commits.
- **DO NOT touch `docs/prd/` or `docs/plans/<other-FR>.md` files.** This skill changes one plan's checkboxes and the implementation files. Other plans are out of scope.

## References

- Phase prompts: [prompts/01-load-context.md](prompts/01-load-context.md), [prompts/02-task-loop.md](prompts/02-task-loop.md), [prompts/03-finalize.md](prompts/03-finalize.md)
- Per-task execution scaffold: [templates/task-execution.md.tmpl](templates/task-execution.md.tmpl)
- 8-point handoff checklist: [references/checklist.md](references/checklist.md)
- Anti-patterns and recovery moves: [references/anti-patterns.md](references/anti-patterns.md)
- Source framework: [../../docs/framework.md](../../docs/framework.md)
- Upstream skills: [`generate-dev-plan`](../generate-dev-plan/SKILL.md), [`declarative-design`](../declarative-design/SKILL.md)
