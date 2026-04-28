# Declarative-Design Anti-Patterns

Common failure modes when bringing UI designs into a project, with remedies.

## 1. Bundling unrelated requirements

**Symptom.** One design file `docs/designs/auth-screens.html` covers FR-001 (login), FR-002 (signup), and FR-003 (password reset).

**Why it's bad.** Filename can't trace to a requirement. Implementation for FR-001 has to manually pick the right screen out of the bundle. Updates to one requirement risk visual drift on the others.

**Remedy.** One file pair per `<FR-N>-<slug>[-<state>]`. If three requirements share visual language, the consistency comes from the brand/style anchors in the vibe-design prompt — not from co-locating them in one file.

---

## 2. Inventing states

**Symptom.** Phase 1 produced 4 screens for FR-001 (login). Phase 2 generated 6 — agent added a "loading" and "disabled" state because "those usually exist."

**Why it's bad.** Designs the requirement doesn't need become aspirational specs. Implementer wastes time matching designs that have no AC backing.

**Remedy.** Phase 1's screen list is the contract. If a state isn't on the list, don't generate it. If you think one is missing, return to Phase 1 and ask the user — don't expand silently.

---

## 3. Stale designs

**Symptom.** PRD changed (e.g. AC-001.2 added a new error code). Designs were generated against the old PRD and are still in place. Implementation matches the new PRD, leaving the design out of sync.

**Why it's bad.** Designs become decorative — implementation diverges, no one trusts the design folder.

**Remedy.** When PRD changes meaningfully, rename affected designs to `<FR-N>-<slug>.STALE.html` and rerun Phase 2. The `.STALE` marker stops anyone from referencing the obsolete design as truth. Generate fresh designs to replace them.

---

## 4. Screenshot without HTML

**Symptom.** User exports only a PNG (or only a Figma raster export) and asks the skill to "add this design."

**Why it's bad.** Screenshots are for humans; the agent can't read component structure, spacing tokens, or content from a raster image. Implementation guesses.

**Remedy.** HTML is non-negotiable. If the user only has screenshots, send them back to Phase 2 to regenerate via Stitch/Claude Design with HTML output enabled. Pixel-perfect Figma HTML exports don't count — they're unreadable.

---

## 5. Pixel-perfect Figma HTML

**Symptom.** User uses Figma's "Export as HTML" feature and brings the result into `docs/designs/`. The HTML is one giant `<div>` tree with absolute positioning and inline pixel coordinates.

**Why it's bad.** No semantic structure. Components can't be derived. Implementer essentially re-builds from scratch but now has a 5,000-line "design" that no one can reason about.

**Remedy.** Figma → screenshot is fine for the PNG side. For HTML, use a tool that produces semantic output: Stitch, Claude Design, or hand-written. The skill explicitly forbids raw Figma HTML in its hard rules.

---

## 6. Hidden behavior in screenshots

**Symptom.** A behavior that matters — *"submit button disabled until terms checkbox is checked"* — appears only in a screenshot annotation, not in the PRD's AC.

**Why it's bad.** Designs visualize; ACs specify. Behaviors hidden in design notes get missed in implementation because the agent's source of truth is the AC list, not the design margin.

**Remedy.** When you find a behavior in a design that isn't in the AC, STOP. Surface it to the user: *"AC-001.2 doesn't mention disabled-until-terms-checked, but the design shows it. Add to AC or remove from design?"* Either fix the AC or remove the behavior from the design — don't let them drift.

---

## 7. Silent design overwrite

**Symptom.** `docs/designs/FR-001-login.html` already exists. New version replaces it without diff or `.OLD.html` rename. Reviewer can't tell what changed visually.

**Why it's bad.** Loses design history. If the new version regresses, hard to recover.

**Remedy.** Phase 3 explicitly requires diffing existing files. Default move: rename old to `.OLD.html`. User decides later whether to delete the `.OLD` files.

---

## 8. Slug drift between phases

**Symptom.** Phase 1's screen list calls it `password-reset-link`. Phase 3 places the file as `forgot-password.html`.

**Why it's bad.** Cross-references break. The plan references the Phase 1 slug; designs use Phase 3's. Implementer has to figure out the mapping manually.

**Remedy.** Slug is fixed in Phase 1. Phase 3 places, doesn't redefine. If the slug needs to change, return to Phase 1 and update the screen list explicitly.

---

## 9. AC-orphan designs

**Symptom.** A design pair exists in `docs/designs/` that doesn't visualize any AC. It's "for context" or "for the brand."

**Why it's bad.** Designs are inputs to implementation. A design with no AC is decorative — it lives in a brand-guidelines folder, not `docs/designs/`.

**Remedy.** Every design traces to ≥1 AC. If it doesn't, ask the user why it's there. Most likely: move to `docs/branding/` or delete.

---

## 10. Skipping the design read in implementation

**Symptom.** Designs were placed correctly. But during `execute-plan`, the agent implements the UI without opening `docs/designs/<FR-N>-*.html`.

**Why it's bad.** All the work in Phases 1–4 wasted. Implementation diverges from the spec the team approved.

**Remedy.** This is partly a design-skill problem (prevent it by making designs hard to miss in `docs/`) and partly an `execute-plan` problem (its Phase 1 explicitly opens every design file). If implementation diverges, the rework is on the implementer; the design skill's job ends at well-placed, well-named files.
