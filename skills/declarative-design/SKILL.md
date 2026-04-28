---
name: declarative-design
description: Use when the user wants to bring UI designs into a project for a PRD requirement. Identifies the screens/states a requirement needs, helps the user generate them via Stitch or Claude Design (or import existing exports), and places HTML + screenshot pairs under docs/designs/<FR-N>-<slug>.{html,png} so implementation can reference them. Triggers on "import these designs", "add screens for FR-001", "set up the designs for this requirement", "vibe design this screen", "/designs FR-N".
---

# Declarative Design

Bring UI designs into a project tied to a PRD requirement. Implements **Phase 4** of the [AI Development Framework V2](../../docs/framework.md).

## Core principle

Designs are inputs to implementation, not after-the-fact polish. Each design MUST be **traceable to a requirement** (FR-N), **machine-readable** (HTML, not just a screenshot), and **stored where the agent can find it** (`docs/designs/<FR-N>-<slug>.html`). Pixel-pushing is replaced by *vibe design*: describe the intent, iterate on output.

## When to use

- Requirement exists, no designs yet → start at **Phase 1** to identify needed screens, then generate via Stitch/Claude Design.
- User already has HTML/screenshot exports → start at **Phase 3** to place them.
- Designs exist but are unlinked / poorly named → start at **Phase 3** with the existing files as input.

Skip for: backend-only requirements, CLI tools, internal services with no UI.

## The 3 phases

Each phase has an INPUT, an OUTPUT, and a **HUMAN GATE** before advancing.

| # | Phase | Input | Output | Prompt file |
|---|---|---|---|---|
| 1 | Identify screens | Requirement bundle (`docs/prd/`) | Screen list (per state) | [prompts/01-identify-screens.md](prompts/01-identify-screens.md) |
| 2 | Acquire designs | Screen list | HTML + screenshot pairs in user's hands | [prompts/02-acquire-designs.md](prompts/02-acquire-designs.md) |
| 3 | Place designs | Files from user | Files in `docs/designs/`, named per convention, cross-linked | [prompts/03-place-designs.md](prompts/03-place-designs.md) |

Phase 2 is the only phase that **does not produce a file inside the repo** — its output is the user going off to Stitch / Claude Design and returning with exports. The skill's job there is to give the user a useful design prompt and wait.

## Operating protocol

1. **Identify the requirement ID.** If not given, ASK. Use the same FR-N format as `generate-dev-plan` and `ai-driven-prd`.
2. **Read the matching prompt file** in `prompts/`. Treat the prompt body as a system instruction — follow its rules literally.
3. **Stop at every phase boundary.** Show artifact, ask: *approved / refine / restart phase*.
4. **Phase 2 fork:** at the start of Phase 2, ask the user *"Do you already have HTML exports for these screens?"* If yes, skip to Phase 3 with those files as input. If no, produce the vibe-design prompt from [templates/vibe-prompt.md.tmpl](templates/vibe-prompt.md.tmpl).
5. **Phase 3 final gate**: score against [references/checklist.md](references/checklist.md). All 6 items MUST pass.

## Hard rules (non-negotiable)

- **Naming convention**: `docs/designs/<FR-N>-<slug>.{html,png}` — no exceptions. The `<slug>` is a kebab-case short name (`login`, `password-reset`, `cart-empty-state`). Both HTML and PNG share the slug.
- **Pair invariant**: every HTML MUST have a matching PNG screenshot. The PNG is for humans skimming `docs/designs/`; the HTML is for the agent.
- **One screen state per file**. If a requirement has 3 states (empty / loading / error), produce 3 file pairs, not 1 with all states stacked.
- **Trace to AC**. Each design's filename or sidecar note maps to the ACs it visualizes. If a design doesn't visualize any AC, ask the user why it's needed.
- **No raw Figma exports.** Figma → static image is fine for screenshots only; the HTML must come from a tool that produces clean, semantic HTML (Stitch, Claude Design, hand-written). Pixel-perfect Figma HTML exports are unreadable and useless to the agent.
- **Don't hide constraints in screenshots.** If a behavior matters (e.g. *"submit button disabled until terms accepted"*), it MUST live in the PRD's AC, not only in the design. Designs visualize; ACs specify.
- **Ambiguity → ASK.** Never invent a screen the requirement doesn't call for.

## What NOT to do

See [references/anti-patterns.md](references/anti-patterns.md). Top three to watch:

- **Bundling unrelated requirements.** One file pair per `<FR-N>-<slug>[-<state>]`. No `auth-screens.html` covering FR-001 + FR-002 + FR-003.
- **Inventing states.** Phase 1's screen list is the contract. Don't add a "loading" or "error" design unless the requirement calls for one.
- **Stale designs.** When the PRD changes meaningfully, rename affected designs to `<FR-N>-<slug>.STALE.html` and rerun Phase 2 — never silently use outdated designs.

## References

- Vibe-design prompt scaffold: [templates/vibe-prompt.md.tmpl](templates/vibe-prompt.md.tmpl)
- Phase prompts: [prompts/01-identify-screens.md](prompts/01-identify-screens.md), [prompts/02-acquire-designs.md](prompts/02-acquire-designs.md), [prompts/03-place-designs.md](prompts/03-place-designs.md)
- 6-point handoff checklist: [references/checklist.md](references/checklist.md)
- Anti-patterns and remedies: [references/anti-patterns.md](references/anti-patterns.md)
- Naming convention details: [references/naming.md](references/naming.md)
- Source framework: [../../docs/framework.md](../../docs/framework.md)
- Tooling: [Stitch](https://stitch.withgoogle.com/) — Google's vibe-design tool. [Claude Design](https://claude.com/) — Anthropic's design tool.
