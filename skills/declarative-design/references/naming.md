# Design Naming Convention

All designs live under `docs/designs/`. Filenames follow a strict pattern so the agent can locate designs from a requirement ID alone.

## Pattern

```
docs/designs/<FR-N>-<slug>[-<state>].{html,png}
```

- `<FR-N>` — verbatim PRD requirement ID. Same casing and format as in the PRD (`FR-001`, `FR-AUTH-003`).
- `<slug>` — kebab-case short name for the screen. Nouns only: `login`, `cart`, `password-reset-link`. Avoid verbs (`logging-in`) and full sentences.
- `<state>` — kebab-case state, optional. Include only when the screen has multiple states. Standard buckets:
  - `empty` — no data, first-time
  - `loading` — fetching
  - `populated` — happy path with content
  - `error-<code>` — specific error (e.g. `error-401`, `error-network`, `error-validation`)
  - `success` — terminal success state
- HTML and PNG MUST share the slug + state, differ only in extension.

## Examples

```
docs/designs/FR-001-login.html              ← single-state screen
docs/designs/FR-001-login.png

docs/designs/FR-002-cart-empty.html         ← multi-state, "empty"
docs/designs/FR-002-cart-empty.png
docs/designs/FR-002-cart-populated.html
docs/designs/FR-002-cart-populated.png

docs/designs/FR-003-checkout-error-429.html ← multi-state with error code
docs/designs/FR-003-checkout-error-429.png
```

## Sidecars (optional)

If filename + screen list isn't enough to disambiguate, add:

```
docs/designs/<FR-N>-<slug>[-<state>].notes.md
```

Use sparingly. Don't add notes for the sake of adding notes.

## Versioning superseded designs

When a design is replaced, rename the old file:

```
docs/designs/FR-001-login.OLD.html
docs/designs/FR-001-login.OLD.png
```

This preserves history without polluting the active filename. After a few iterations, the user can delete `.OLD.*` files to clean up.

## Anti-patterns

- ❌ `docs/designs/login.html` — missing `<FR-N>` prefix; can't trace to requirement.
- ❌ `docs/designs/FR-001_login.html` — underscore instead of hyphen breaks the convention.
- ❌ `docs/designs/FR-001-login-states.html` — combined states; should be split per state.
- ❌ `docs/designs/login_v2.html` — version suffix in filename. Use `.OLD.html` for superseded files instead, and keep the active file at the canonical name.
- ❌ `docs/designs/feature-x.html` — slug doesn't trace to a requirement. If the feature has a requirement ID, use it.
