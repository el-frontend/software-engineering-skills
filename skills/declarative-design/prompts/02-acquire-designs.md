# Phase 2 — Acquire Designs

You have an approved screen list from Phase 1. Now you help the user **get HTML + screenshot pairs** for each screen + state. This phase produces no files inside the repo — its output is the user returning with exports in hand.

## Inputs

- Approved screen list (Phase 1).
- Optionally existing exports if the user already has them.

## What to do

### Step 1: Fork on existing assets

Ask the user exactly:

> *"Do you already have HTML exports for these screens, or do you need to generate them?"*

- **Already have them** → skip to Phase 3 with the file paths the user provides. Phase 2 is a no-op.
- **Need to generate** → continue with Step 2.
- **Mix** (some exist, some don't) → continue with Step 2 for the missing ones; Phase 3 will batch both.

### Step 2: Pick the tool

Two tools the framework supports:

- **[Stitch](https://stitch.withgoogle.com/)** — Google's vibe-design tool. Good for full screens, exports clean HTML + image.
- **[Claude Design](https://claude.com/)** — Anthropic's design tool. Tightly integrated with Claude conversations.

Either works. Ask the user which one they want to use, or default to whichever they've used on prior screens in this repo (consistency > novelty).

### Step 3: Produce a vibe-design prompt for each screen + state

Copy [`templates/vibe-prompt.md.tmpl`](../templates/vibe-prompt.md.tmpl) for each row in the screen list. Fill the placeholders from:

- The PRD (visual hints in the FR statement, ACs, brand/style notes).
- `docs/architecture.md` if it exists (component library, theme tokens).
- Adjacent existing designs in `docs/designs/` — the new screens MUST visually match the rest of the app.

Each prompt MUST include:

- **What screen + state** (e.g. *"Login screen, error state — 401 from server"*)
- **Key elements** (form fields, buttons, error placement)
- **Brand / style anchors** if the project has them (font, primary color, density)
- **What NOT to design** (don't invent navigation, don't add unrelated elements)
- **Output format** request: HTML export + PNG screenshot at the same aspect ratio

### Step 4: Hand to the user, then wait

Output the prompt(s) for the user to paste into Stitch / Claude Design. Then output exactly:

> *Generate each screen at the link above. When you have HTML + PNG for each row in the screen list, return here with the file paths and we'll proceed to Phase 3.*

**Do not invent file paths or simulate the design.** The skill cannot generate visual designs — it can only help the user prompt the right tool.

### Step 5: When the user returns

Confirm with the user:

- They have an HTML + PNG pair for each row in the screen list (or for each row not already covered by existing assets).
- The file paths are absolute or repo-root-relative — easy to copy.
- The screenshots actually match the HTML (sometimes Stitch exports get out of sync).

Then proceed to Phase 3.

## Anti-patterns (forbidden)

- ❌ Generating HTML yourself and pretending it came from a design tool. The skill is a coordinator, not a designer.
- ❌ Producing one giant prompt that asks for "all the screens" — Stitch/Claude Design works better with one screen per request.
- ❌ Skipping the brand/style anchors. New screens that don't match existing designs create churn in implementation.
- ❌ Continuing to Phase 3 if the user only has screenshots (no HTML). The HTML is what makes the design machine-readable.
