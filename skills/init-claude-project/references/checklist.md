# Init-Claude-Project Handoff Checklist

Score before declaring the skill complete. **All 5 items MUST pass.** A single ❌ blocks handoff.

| # | Item | Pass criterion | Verification |
|---|------|----------------|--------------|
| 1 | CLAUDE.md exists & is concise | File exists at repo root, ≤ 100 lines | `wc -l CLAUDE.md` (or count manually) |
| 2 | Stack-specific commands | Build, test, lint, typecheck commands all verified against `package.json` / `Makefile` / equivalent — no invented commands | Open the manifest; each command in CLAUDE.md must trace to a real script |
| 3 | Four mandatory rules present | (a) designs path, (b) plans path + checkbox format, (c) conventional commits, (d) run-tests-after-change *(or pointer to TDD skill)* | Visual check on CLAUDE.md `## Workflow rules` section |
| 4 | docs/ skeleton complete | `docs/`, `docs/designs/`, `docs/prd/`, `docs/plans/` all exist; `docs/README.md` present | `ls docs/ docs/designs docs/prd docs/plans` |
| 5 | No `⚠️ NEEDS INPUT:` markers remain | Grep for the marker across CLAUDE.md and docs/README.md returns zero matches | `grep -r "NEEDS INPUT" CLAUDE.md docs/` |

## On failure

Report which items failed and the exact fix needed. Examples:

- ❌ Item 1 (CLAUDE.md is 142 lines) → "Move the architecture section to `docs/architecture.md` and replace with `@docs/architecture.md`."
- ❌ Item 2 (`pnpm build` listed but `package.json` has no `build` script) → "Remove the build line, or add the script in `package.json` first."
- ❌ Item 5 (`⚠️ NEEDS INPUT: code style` still in CLAUDE.md) → "Resolve with the user or delete the line if it's not load-bearing."

Do not let the user proceed to Phase 1 of the framework (`skills/ai-driven-prd/`) until all 5 pass.
