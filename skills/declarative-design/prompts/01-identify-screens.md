# Phase 1 — Identify Screens

You are deriving the **list of screens (and states)** a requirement needs, from the PRD. Do not generate or import any designs in this phase.

## Inputs

- A requirement ID (e.g. `FR-001`). If not given, ASK.
- The repo's `docs/prd/` directory.
- Optionally an existing plan at `docs/plans/<FR-N>.md` if `generate-dev-plan` already ran.

## What to do

### Step 1: Locate the requirement

Reuse the locating logic from `generate-dev-plan`'s Phase 1: find the highest-version PRD in `docs/prd/`, locate FR-N, pull statement + ACs + user stories + edge cases.

If the PRD or requirement is missing, STOP and tell the user — same as `generate-dev-plan`.

### Step 2: Derive the screen list

Read the FR + ACs + user stories. For each user-visible interaction, list:

- **The screen** (a noun: `login`, `cart`, `password-reset`).
- **The states it has** (verbs/conditions: `empty`, `loading`, `error`, `success`, `disabled`).

A state is its own design when **the visible UI changes meaningfully**. Hover/focus styles are not states; "no items in cart" vs "5 items in cart" is.

#### Common state buckets to consider

For each screen, ask whether each of these applies. Skip any that don't:

- **Empty / first-time** — no data yet, no history.
- **Loading** — fetching data; skeleton or spinner.
- **Populated** — the happy path with real content.
- **Error** — failure modes from the AC's edge cases (401, 429, network down, validation fail).
- **Success / done** — terminal state after action (e.g. "password reset sent").

Don't invent states the AC doesn't justify. If AC-001.2 doesn't mention loading, don't produce a loading design — ask the user first.

### Step 3: Map each design to ACs

For each screen + state, list which AC-IDs it visualizes. If a screen + state maps to no AC, flag it and ask the user why it's needed.

### Step 4: Output the screen list

```markdown
## Screens for FR-001 (Login)

**Source PRD:** `docs/prd/prd-v0.3.md`

| Slug | State | Visualizes ACs | Notes |
|------|-------|----------------|-------|
| `login` | populated | AC-001.1 | Default form |
| `login` | error-401 | AC-001.2 | Inline message under password field |
| `login` | error-429 | AC-001.5 | Rate-limit warning |
| `password-reset-link` | success | AC-001.6 | Confirmation after submit |

### Open questions
- ⚠️ MISSING: AC-001.3 mentions "remember me" but no state distinguishes it. Is there a UI change?
```

### Step 5: Hand to user

> *Approved → proceed to Phase 2 (acquire) / Refine list / Restart*

**Do not proceed to Phase 2 with unresolved `⚠️ MISSING:` items.**

## Anti-patterns (forbidden)

- ❌ Inventing states. If the AC doesn't call for a loading state, don't list one.
- ❌ Bundling multiple screens per row. One row = one screen + one state.
- ❌ Skipping the AC mapping. A design that doesn't visualize any AC is decorative, not load-bearing.
- ❌ Producing more than ~12 rows for a single requirement. If you need that many, the requirement is probably too big and should be split before designing.
