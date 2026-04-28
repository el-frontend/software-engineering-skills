# Execute-Plan Anti-Patterns

Common failure modes during plan execution, with recovery moves.

## 1. Marking `[x]` with failing tests

**Symptom.** A task is checked off but `{{TEST_CMD}}` reports a regression.

**Why it's bad.** The plan's contract is "every `[x]` is green." Breaking that breaks every downstream signal — reviewers, automation, the next agent session.

**Recovery.** Revert the checkbox flip with `git revert <commit>` (if pushed) or `git reset HEAD~1` (if not). Fix the code. Re-commit with the checkbox in the same diff.

---

## 2. Batching tasks in one commit

**Symptom.** `git log` shows one commit with two `[x]` flips and code for two sub-IDs.

**Why it's bad.** Loses traceability. Bisect can't isolate which task broke things.

**Recovery.**

- Not pushed: `git reset HEAD~1`, stage and commit each task's diff separately.
- Pushed: surface to user. Either accept the bundle (note in PR body: "FR-001.2 and FR-001.3 ship together — they share a refactor") or open a fixup with the user's approval.

Prevention: **return control to the user between every task.** Phase 2's hand-back is the gate.

---

## 3. Auto-chaining tasks

**Symptom.** Agent finishes FR-001.1 and immediately starts FR-001.2 without asking.

**Why it's bad.** The user is the human gate. Auto-chaining defeats Phase 2's per-task review point.

**Recovery.** None needed if no harm done — just stop and re-engage the user. If a task introduced bad code that the next task built on, surface the chain so the user can decide rollback strategy.

Prevention: re-read [`prompts/02-task-loop.md`](../prompts/02-task-loop.md) Step 7 — *"Wait for the user's call before starting the next task."*

---

## 4. Three-strike loop

**Symptom.** Same test fails 3 times. Agent keeps retrying with small variations.

**Why it's bad.** Loops waste tokens and rarely break out of a misunderstanding.

**Recovery.** STOP. Tell the user:

> *"FR-001.2 has failed 3 times — pattern: [describe the failure]. Suspected cause: [hypothesis]. Options: A) ask user to clarify AC, B) update the plan, C) skip this task and revisit."*

Let the user decide.

---

## 5. Silent scope creep

**Symptom.** Diff includes files the plan didn't list. Agent "noticed" a related bug and fixed it.

**Why it's bad.** Mixes plan-implementation with surprise refactoring. Reviewer can't tell which change satisfies which AC.

**Recovery.**

- Not pushed: amend the commit to remove the unrelated change. File a separate issue or note.
- Pushed: surface to the user. Common resolution: open a follow-up PR for the surprise fix, link it from the main PR.

Prevention: stay inside the file list. Trivial one-liners (an import, a type widening) are OK if noted in the commit body.

---

## 6. `--no-verify` to bypass a hook

**Symptom.** Pre-commit hook fails (lint, format, test). Agent commits with `--no-verify` to "keep moving."

**Why it's bad.** Hooks exist for reasons the agent doesn't always know. Bypassing them ships broken code.

**Recovery.** Revert. Investigate why the hook failed. Fix the cause. Re-commit through the hook.

The framework's rule: **never `--no-verify` without explicit user approval.** And even with approval, surface what was bypassed in the commit body.

---

## 7. Mid-task ambiguity → invent

**Symptom.** Implementing FR-001.2, agent realizes the AC doesn't say what HTTP status to return on rate-limit. Agent picks `429` and moves on.

**Why it's bad.** The user is the source of truth, not the agent's defaults. A hidden decision in code is worse than a flagged decision in the plan.

**Recovery.** STOP. Surface the ambiguity:

> *"AC-001.5 says 'rate limited' but doesn't specify status code. Standard would be 429 with `Retry-After`. Confirm or change?"*

The framework's rule: **ambiguity → ASK.** Even mid-task.

---

## 8. Skipping the design read

**Symptom.** UI task implements something that doesn't match `docs/designs/FR-001-login.html`.

**Why it's bad.** Design rework after implementation costs more than design read before.

**Recovery.** Revert the UI work. Re-open the design HTML. Re-implement to match.

Prevention: Phase 1 explicitly requires opening every `docs/designs/<FR-N>-*.html`. Don't skip.

---

## 9. Plan checkbox in a separate commit

**Symptom.** Code commit `feat(auth): add lastLoginAt to user model`, then a follow-up `chore: mark FR-001.1 done`.

**Why it's bad.** History no longer shows code and plan in agreement at every commit. Bisect and PR review get confusing.

**Recovery.** If not pushed: `git reset HEAD~2`, re-stage including the plan, re-commit as one. If pushed: surface to user.

The framework's rule: **mark `[x]` in the same commit as the work.**

---

## 10. Closing the loop without finalize

**Symptom.** All tasks `[x]`, agent stops there. No PR description, no CLAUDE.md feedback.

**Why it's bad.** The user is left to write the PR body and lose the lessons-learned signal.

**Recovery.** Run Phase 3 ([`prompts/03-finalize.md`](../prompts/03-finalize.md)). Score the checklist. Produce the PR body. Surface any recurring corrections.

Prevention: don't declare the skill done until Phase 3 completes.
