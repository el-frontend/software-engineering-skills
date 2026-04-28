# Phase 3 — Place Designs

You have HTML + PNG pairs (from Phase 2 or pre-existing) and the screen list (from Phase 1). Now you place them in `docs/designs/` per the naming convention.

## Inputs

- The Phase 1 screen list (the source of truth for slugs and states).
- One or more HTML + PNG file pairs from the user.
- The repo's `docs/designs/` directory.

## What to do

### Step 1: Verify pairs

For every file path the user gave you:

1. Confirm the file exists.
2. Confirm there's a matching pair (HTML and PNG with the same logical screen + state).
3. If a pair is missing one half, STOP and tell the user: *"FR-001 login (error-401) — HTML found at X, but no PNG. Provide the PNG before we proceed."*

### Step 2: Compute target paths

For each row in the Phase 1 screen list, the target paths are:

```
docs/designs/<FR-N>-<slug>-<state>.html
docs/designs/<FR-N>-<slug>-<state>.png
```

Rules (full spec in [`references/naming.md`](../references/naming.md)):

- `<FR-N>` — verbatim requirement ID, e.g. `FR-001`.
- `<slug>` — kebab-case from the screen list (e.g. `login`, `password-reset-link`).
- `<state>` — kebab-case state (e.g. `populated`, `error-401`, `success`).
- If a screen has only one state, omit `-<state>` (file becomes `FR-001-login.html`).
- HTML and PNG share the slug + state — only the extension differs.

If a target file already exists:

1. Diff it against the new file. If identical, skip (no-op).
2. If different, ask the user: *"`docs/designs/FR-001-login.html` already exists. Replace, keep, or rename old to `.OLD.html`?"*
3. Default suggestion: rename old to `.OLD.html` so history is preserved without polluting the active filename.

### Step 3: Move files into place

Copy or move the pairs to their target paths. Confirm each one lands. If any move fails (permissions, disk, etc.), STOP and tell the user before continuing.

### Step 4: Sidecar note (optional)

If a design's filename + screen list isn't enough to disambiguate state or AC mapping, add a sidecar `<FR-N>-<slug>-<state>.notes.md` containing:

```markdown
# FR-001 — login (error-401)

- **AC visualized:** AC-001.2
- **Notes:** Inline error appears under the password field, not above the form.
- **Source:** Generated via Stitch on 2026-04-28.
```

Skip the sidecar when the filename + screen list already says everything load-bearing. Don't add notes for the sake of adding notes.

### Step 5: Cross-link

Optionally update related docs to mention the designs:

- If `docs/plans/<FR-N>.md` exists, add a *Designs* section linking to each placed file.
- If the PRD's FR-N section references designs, update the link if it was a placeholder.

Ask the user before editing other docs — they may prefer to do it themselves.

### Step 6: Final gate

Score against [`references/checklist.md`](../references/checklist.md). All 6 items MUST pass. If any fail, fix before handoff.

### Step 7: Suggest next step

> *Designs placed. Next: run `skills/generate-dev-plan/` (if no plan yet) or `skills/execute-plan/` (if a plan exists) to start implementation.*

## Anti-patterns (forbidden)

- ❌ Silently overwriting existing designs. Always diff and ask.
- ❌ Renaming the slug between phases. The slug is fixed in Phase 1; Phase 3 places, doesn't redefine.
- ❌ Editing the HTML you received. The skill places designs as-is — modifications happen in Phase 2 (re-prompt the design tool).
- ❌ Producing sidecar notes for every design. Most don't need one.
- ❌ Marking the skill complete with any checklist failure.
