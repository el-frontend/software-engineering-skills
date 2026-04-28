# Anti-Patterns and Remedies

The common thread across all of these: **assuming human judgment that the LLM does not have**. Each anti-pattern below has a concrete remedy.

## 1. Over-specification
**Symptom:** PRD lists 300 requirements; the agent implements all 300 badly instead of 50 well, because each requirement crowds context and degrades attention.
**Remedy:** Phase the PRD into 30–50 requirements per implementation phase. Defer the rest to follow-up PRDs with explicit links.

## 2. Sub-specification
**Symptom:** Agent picks Express in your Fastify codebase; chooses Jest where you use Vitest; uses `console.log` instead of your logger. The agent fills gaps with the statistical average of its training data.
**Remedy:** Pin stack with versions, list reference files (`@src/...`) the agent must mimic, enumerate forbidden packages.

## 3. Fuzzy adjectives
**Symptom:** "Fast", "easy", "intuitive", "modern", "robust", "seamless" appear without thresholds. They are pure noise to an LLM.
**Remedy:** Quantify every one. See `examples.md` for the rewrite recipe.

## 4. Missing repo context
**Symptom:** No `AGENTS.md` / `CLAUDE.md` / `.cursor/rules`. Each session starts blind; the agent re-discovers stack, conventions, and boundaries from scratch.
**Remedy:** Maintain a <300-line root context file; reference per-feature PRDs from it.

## 5. Monolithic document
**Symptom:** A single 2000-line PRD. LLMs lose attention in the middle of long contexts ("context rot").
**Remedy:** Chunk into PRD + constitution + tasks + progress.json. Use headings, code fences, and XML tags to give the parser anchors.

## 6. Assuming common sense
**Symptom:** "Handle edge cases reasonably." The agent's notion of "reasonable" is unpredictable.
**Remedy:** Enumerate edge cases explicitly in a failure-modes table covering all 12 categories from `prompts/04-adversarial.md`.

## 7. Code as source of truth
**Symptom:** Spec drifts from implementation; regenerating from spec breaks features.
**Remedy:** Spec + tests are authoritative; code is regenerable. Keep tests co-located and the spec under version control.

## 8. No verification gates
**Symptom:** Agent declares success while tests are red or lint fails.
**Remedy:** Each implementation phase ends with an explicit verification command. The agent quotes the command output before moving on.

## 9. Stale training-data assumptions
**Symptom:** Agent uses deprecated APIs because its training data predates the breaking change.
**Remedy:** Add a research step that pins documentation URLs and version numbers; reference current docs from the PRD.

## 10. Implicit scope boundaries
**Symptom:** Agent "helpfully" refactors unrelated files, renames variables, reformats imports — generating an unreviewable PR.
**Remedy:** Absolute DO NOT list with ≥4 concrete forbidden actions (specific file paths, specific actions). Treat omission as permission, so spell out boundaries.

---

## Complementary pattern: feedforward + feedback

The PRD is only the **feedforward** half of the harness. It also needs a **feedback** half: tests, linters, and error messages designed for LLM consumption — sometimes called "positive prompt injection," where build errors carry instructions for the fix.

Without feedback, the agent repeats mistakes. Without feedforward, it never learns the rules. Build both.
