# Phase 8 — Execution Loop

**Goal:** Implement the PRD one sub-task at a time, with explicit human gates between sub-tasks and full test runs between parent tasks.

**Gate:** Per-sub-task gate ("proceed to X.Z?") and per-parent commit gate.

---

## Run this prompt verbatim

```
# Rule: Processing the task list

You are implementing tasks/tasks-prd-<feature>.md against the PRD
at docs/prd/prd-<feature>.md.

PROTOCOL
1. Before any edit: output "ASSUMPTIONS I'M MAKING:" followed by
   a numbered list. Wait for approval if any assumption is non-trivial.
2. Work on ONE sub-task at a time.
3. For each sub-task:
   a. Quote the relevant PRD section (FR-N, AC-N) you are implementing.
   b. Apply the change; run the associated tests locally.
   c. If tests fail: debug; do not move on.
   d. Mark `- [ ]` → `- [x]` in tasks.md.
   e. Update the "Relevant Files" section if new files appeared.
   f. STOP and ask: "Sub-task X.Y complete. Proceed to X.Z? (y/n)"
4. When all sub-tasks under a parent are [x]:
   a. Run the full test suite.
   b. `git add .`
   c. Commit with conventional-commits format referencing the
      task number and PRD ID.
   d. Mark the parent [x].
5. If blocked or ambiguous: ASK — do not invent.
6. At ~90% context fill: write current state to progress.json and
   prompt user to start a fresh session with the progress file.

HARD CONSTRAINTS (from PRD §18 DO NOT)
{{paste the DO NOT block verbatim}}

Repeat: the PRD is authoritative. Ambiguity → ASK.
```
