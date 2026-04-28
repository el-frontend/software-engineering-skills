# Phase 3 — Bootstrap docs/

You are creating the `docs/` skeleton in the target repo and wiring it into the `CLAUDE.md` from Phase 2.

## Inputs

- Approved `CLAUDE.md` from Phase 2.
- The template at [`templates/docs-readme.md.tmpl`](../templates/docs-readme.md.tmpl).

## What to do

### Step 1: Detect existing `docs/`

If `docs/` already exists:
- List its contents.
- Do NOT delete or move existing files.
- Plan to *add* the missing subfolders without disturbing what's there.
- If files already live where the new convention says they should go (e.g. an existing `docs/PRD.md`), surface this to the user — they decide whether to rename or keep both.

### Step 2: Create the skeleton

Create these directories at the target repo root, each with a `.gitkeep` file so the empty dirs commit:

```
docs/
├── README.md           ← from templates/docs-readme.md.tmpl
├── designs/
│   └── .gitkeep
├── prd/
│   └── .gitkeep
└── plans/
    └── .gitkeep
```

The `docs/README.md` is a verbatim copy of `templates/docs-readme.md.tmpl` — no placeholders to fill. It documents the three-folder convention for future maintainers and agents.

### Step 3: Wire into CLAUDE.md

Edit the `CLAUDE.md` written in Phase 2 to add — under the existing `## Imports` section — only the imports that point to files that **actually exist**:

- ✅ `@docs/framework.md` (added in Phase 2 already, leave as-is)
- ❌ Do NOT add `@docs/architecture.md` unless you actually created it
- ❌ Do NOT add `@docs/prd/...` — those are per-feature and shouldn't auto-import

The point of `@imports`: load on relevance, not eagerly. Only import files that apply to *every* session.

### Step 4: Final handoff gate

Score against [`references/checklist.md`](../references/checklist.md). All five items MUST pass:

1. CLAUDE.md exists at repo root, ≤ 100 lines.
2. Stack-specific build/test/lint commands present and verified against manifest.
3. Four mandatory rules present (designs path, plans path + checkbox, conventional commits, run-tests).
4. `docs/`, `docs/designs/`, `docs/prd/`, `docs/plans/` exist with `docs/README.md`.
5. No `⚠️ NEEDS INPUT:` markers remain anywhere.

If any item fails, return the failures to the user with the specific fix needed. Do NOT declare the skill complete until all five pass.

### Step 5: Suggest next step

Suggest the user run [`skills/ai-driven-prd/`](../../ai-driven-prd/SKILL.md) next to produce the first PRD into `docs/prd/`. This is Phase 1 of the framework — it follows naturally from Phase 2.

## Anti-patterns (forbidden)

- ❌ Deleting or moving existing files in `docs/`.
- ❌ Adding `@imports` to files that don't exist (broken imports waste tokens and mislead Claude).
- ❌ Skipping `.gitkeep` — empty directories don't commit, and the next user will see a missing folder.
- ❌ Filling `docs/prd/`, `docs/plans/`, or `docs/designs/` with example content. Those folders are intentionally empty until the team produces real artifacts.
- ❌ Marking the skill complete with any checklist failure.
