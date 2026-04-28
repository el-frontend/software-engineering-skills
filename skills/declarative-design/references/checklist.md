# Declarative-Design Handoff Checklist

Score before declaring the skill complete. **All 6 items MUST pass.** Any ❌ blocks handoff.

| # | Item | Pass criterion | Verification |
|---|------|----------------|--------------|
| 1 | Naming convention applied | Every file matches `<FR-N>-<slug>[-<state>].{html,png}` | `ls docs/designs/` and visual scan |
| 2 | HTML + PNG pairs intact | Every HTML has a matching PNG with the same slug + state | Diff filenames; lone files = ❌ |
| 3 | One state per file pair | No combined images stacking multiple states | Open each PNG; if it shows >1 state, ❌ |
| 4 | AC traceability | Every file maps to ≥1 AC (via screen list, sidecar, or filename) | Cross-check screen list from Phase 1 |
| 5 | No silent overwrites | Any replaced file was either user-approved or moved to `.OLD.html`/`.OLD.png` | `ls docs/designs/*.OLD.*` matches what user approved |
| 6 | No `⚠️ MISSING:` markers | Every Phase 1 open question resolved | `grep -r "MISSING" docs/designs/` returns empty |

## Quick scoring rubric

For each design pair, ask:
- Could an implementer find this design from the requirement ID alone? → ✅ (filename traces back)
- Does the design visualize a specific AC, or is it decorative? → ✅ if AC-traced
- Is the HTML semantic enough that an agent could derive component structure from it? → ✅ if yes

If "no" to any, fix before handoff.

## On failure

Report which item failed and the specific fix:

- ❌ Item 1 (`docs/designs/login.html` — missing `FR-N` prefix) → "Rename to `FR-001-login.html`."
- ❌ Item 2 (HTML with no PNG) → "Re-export the screenshot from Stitch and place at `docs/designs/<slug>.png`."
- ❌ Item 3 (`FR-001-login.html` shows populated AND error states stacked) → "Split into `FR-001-login-populated.html` + `FR-001-login-error-401.html`. Re-prompt Stitch for one state per export."
