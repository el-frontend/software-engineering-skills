# Software Engineer Skills

A growing collection of agent skills that cover the **full software engineering development process** — from raw stakeholder request to a shipped, tested, maintainable feature.

These skills are built for real engineering work, not vibe coding. They're small, composable, and model-agnostic. They work with Claude Code, Cursor, Copilot, Windsurf, Aider, and any other agent that supports the skills format. Hack them, adapt them, make them yours.

The first skill in the set, [`ai-driven-prd`](skills/ai-driven-prd/SKILL.md), is the front door of the pipeline: it turns a fuzzy request into a PRD an AI agent can actually execute. The rest of the pipeline — TDD, code review, refactoring, debugging, release — is being added skill by skill.

## Quickstart

Skills live under `skills/`. Point your coding agent at this directory, or copy individual skills into your project's agent config.

```bash
git clone git@github.com:el-frontend/software-engineering-skills.git
```

Each skill is a self-contained folder with a `SKILL.md` entry point, plus `prompts/`, `templates/`, and `references/` subfolders where needed. Open the `SKILL.md` to see when the skill triggers and how to use it.

## Why These Skills Exist

These skills exist to fix the failure modes that show up over and over when AI agents touch real codebases. The fixes are old-school engineering fundamentals — applied to the AI workflow.

### #1: The Spec Is The Bottleneck

> "No-one knows exactly what they want."
>
> David Thomas & Andrew Hunt, *The Pragmatic Programmer*

**The Problem.** The most common failure mode is misalignment. The agent confidently builds the wrong thing because the spec was vague, missing edge cases, or written for a human reader instead of an LLM with short memory and dated training data. Omission is read as permission. Fuzzy adjectives turn into hallucinations.

**The Fix** is a PRD that is **explicit, atomic, machine-verifiable, and self-contained** — one the agent can execute without filling gaps from "the average of the internet."

That's [`ai-driven-prd`](skills/ai-driven-prd/SKILL.md): an 8-phase pipeline from Socratic discovery → PRD draft → acceptance criteria → adversarial review → task decomposition → AI-readiness gate → test generation → handoff. Every phase has a human gate. The output is a PRD agents can actually build from.

### #2: The Process Is Owned By The Tool, Not You

Approaches like GSD, BMAD, and Spec-Kit try to help by owning the process end-to-end. The trade-off is control: when something breaks inside the pipeline, you can't reach in and fix it.

**The Fix.** These skills stay small and decomposable. Each skill is a single concern with a clear input and output. You can run one in isolation, swap it out, fork it, or chain it with others. No framework lock-in.

### #3: Software Entropy On Steroids

> "Invest in the design of the system *every day*."
>
> Kent Beck, *Extreme Programming Explained*

**The Problem.** Agents radically speed up coding, which means they also accelerate decay. Without explicit care for design, codebases turn into balls of mud faster than ever.

**The Fix** is to bake engineering discipline — TDD, vertical slices, RFC 2119 requirements, ADRs, architectural review — into every skill. The PRD skill already enforces this on the spec side. The implementation, review, and refactoring skills (in progress) will enforce it on the code side.

### Summary

The fundamentals — clear specs, tight feedback loops, deliberate design — matter more in the AI age, not less. These skills are an attempt to encode those fundamentals into a repeatable workflow you control.

## Reference

### Specification & Planning

Skills for turning intent into a buildable spec.

- **[ai-driven-prd](skills/ai-driven-prd/SKILL.md)** — 8-phase pipeline that transforms raw requests into PRDs AI agents can execute. Discovery → PRD draft → acceptance criteria (Gherkin/EARS) → adversarial review → task decomposition → AI-readiness scoring (≥13/15 to pass) → test generation → execution handoff. Triggers on "write a PRD", "spec this feature", "audit my PRD", "is this PRD AI-ready".

### Implementation *(coming)*

Skills for turning a spec into working code.

- **tdd** — Red-green-refactor loop, one vertical slice at a time. *Planned.*
- **vertical-slice** — Break a PRD into independently-shippable slices. *Planned.*
- **diagnose** — Disciplined diagnosis loop for hard bugs and performance regressions. *Planned.*

### Quality & Review *(coming)*

Skills for keeping the code honest.

- **code-review** — Structured PR review against the original PRD and acceptance criteria. *Planned.*
- **security-review** — Security pass over pending changes. *Planned.*
- **architecture-review** — Find deepening opportunities and ball-of-mud risks. *Planned.*

### Maintenance *(coming)*

Skills for keeping the codebase healthy after the feature ships.

- **refactor** — Safe, test-backed refactoring. *Planned.*
- **deprecate** — Retire a feature, flag, or module without breaking callers. *Planned.*
- **post-incident** — Turn an incident into a postmortem and a regression test. *Planned.*

## Status

This repo is in early stages. `ai-driven-prd` is the first complete skill. The categories above describe the target shape of the collection — the rest will land as they're built and battle-tested.

## Credits

Structure and philosophy inspired by [mattpocock/skills](https://github.com/mattpocock/skills).

Repo: [el-frontend/software-engineering-skills](https://github.com/el-frontend/software-engineering-skills)
