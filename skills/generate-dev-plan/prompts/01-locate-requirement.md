# Phase 1 — Locate Requirement

You are locating the PRD requirement the user wants to plan against and producing a **scope-confirmed excerpt**. Do not write any plan in this phase.

## Inputs

- A requirement ID (e.g. `FR-001`, `FR-AUTH-003`). If the user didn't give one, ASK — do not guess.
- The repo's `docs/prd/` directory.

## What to do

### Step 1: Find the PRD file

`docs/prd/` should contain one or more `prd-vX.Y.md` files. Algorithm:

1. List `docs/prd/`.
2. If only one PRD file exists, use it.
3. If multiple exist, prefer the highest version (e.g. `prd-v0.3.md` > `prd-v0.2.md`) unless the user specifies otherwise.
4. If none exist, STOP. Tell the user: *"No PRD found in docs/prd/. Run `skills/ai-driven-prd/` first to produce one."*

### Step 2: Find the requirement in the PRD

Search the PRD for the requirement ID. Variants to handle:

- Heading: `### FR-001 — <title>` or `## FR-001: <title>`
- Inline reference: `**FR-001**: <statement>`
- Table row: `| FR-001 | <statement> | … |`

If the ID does not appear, STOP. Tell the user: *"FR-001 not found in `docs/prd/<file>.md`. Available IDs: FR-002, FR-003, FR-004"* (list them).

### Step 3: Extract the requirement bundle

Pull together everything that matters for planning:

- **Statement** — the FR-N text itself.
- **Acceptance criteria** — every AC-N.M tied to FR-N (Gherkin/EARS blocks if present).
- **Related user stories** — US-N referenced from FR-N or its ACs.
- **Edge cases / failure modes** — relevant rows from the adversarial review section.
- **Linked dependencies** — other FR-N IDs called out as prerequisites.
- **DO NOTs** — entries from the PRD's DO NOT list that touch this requirement.

If any of these can't be found in the PRD, mark `⚠️ MISSING:` and the user must resolve in Phase 2 (or earlier — flag now if it looks blocking).

### Step 4: Scope confirmation

Output exactly this structure:

```markdown
## Locating FR-001

**Source:** `docs/prd/prd-v0.3.md`

### Statement
> <FR-001 statement, verbatim>

### Acceptance criteria
- AC-001.1: <…>
- AC-001.2: <…>

### Related user stories
- US-007: <…>

### Edge cases (from adversarial pass)
- <…>

### Dependencies
- FR-000: <…> (must be implemented first)

### DO NOTs that apply
- <…>

### Open questions (Phase 2 must resolve)
- ⚠️ MISSING: <e.g. "AC-001.3 says 'fast response' — no quantified threshold">
```

### Step 5: Hand to user

Ask exactly one of:

> *Approved → proceed to Phase 2 / Refine `<section>` / Restart phase 1*

**Do not proceed to Phase 2 without explicit approval.** Show the user the bundle so they can verify nothing relevant was missed.

## Anti-patterns (forbidden)

- ❌ Picking a requirement ID without asking.
- ❌ Quoting the PRD section in your own words instead of verbatim — paraphrasing loses precision.
- ❌ Skipping the dependency check. If FR-000 must ship first, the user needs to know now, not in Phase 3.
- ❌ Producing more than ~30 lines of bundle. If the requirement is too big to fit, that's a sign it should be split before planning.
