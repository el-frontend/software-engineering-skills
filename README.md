# Software Engineer Skills

A growing collection of agent skills that cover the **full software engineering development process** — from raw stakeholder request to a shipped, tested, maintainable feature.

This repo encodes the [AI Development Framework V2](docs/framework.md) as composable skills. The framework defines a 5-phase pipeline (PRD → project setup → plan → design → execution); each skill implements one phase.

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

The categories below mirror the 5 phases of the [AI Development Framework V2](docs/framework.md).

### Setup & Specification

Skills for getting a project ready and turning intent into a buildable spec.

- **[init-claude-project](skills/init-claude-project/SKILL.md)** — Bootstraps a target codebase for AI-driven development. 3-phase pipeline: discover stack → fill `CLAUDE.md` (≤100 lines, no invented commands) → bootstrap `docs/` skeleton (`designs/`, `prd/`, `plans/`). Seeds conventional commits and the markdown plan-checkbox format. Triggers on "init Claude in this repo", "set up CLAUDE.md", "bootstrap docs folder", "/init this project". *(Framework Phase 2.)*
- **[ai-driven-prd](skills/ai-driven-prd/SKILL.md)** — 8-phase pipeline that transforms raw requests into PRDs AI agents can execute. Discovery → PRD draft → acceptance criteria (Gherkin/EARS) → adversarial review → task decomposition → AI-readiness scoring (≥13/15 to pass) → test generation → execution handoff. Triggers on "write a PRD", "spec this feature", "audit my PRD", "is this PRD AI-ready". *(Framework Phase 1.)*

### Planning

Skills for turning a spec into an executable plan.

- **[generate-dev-plan](skills/generate-dev-plan/SKILL.md)** — 3-phase pipeline that turns one PRD requirement (`FR-N`) into a markdown task list with `[ ]` checkboxes. Locate requirement in `docs/prd/` → investigate code (prior art, dependencies, ambiguity resolution) → write `docs/plans/<FR-N>.md` as vertical slices, each runnable + tested. Triggers on "make a plan for FR-001", "break this PRD into tasks", "/plan FR-N". *(Framework Phase 3.)*

### Implementation *(coming)*

Skills that drive code from plan to shipped PR.

- **declarative-design** — Import Stitch / Claude Design HTML exports into `docs/designs/` and tie them to requirement IDs. *Planned. (Framework Phase 4.)*
- **execute-plan** — Drive the execution loop: run plan → tests → typecheck → review → conventional commit → PR. *Planned. (Framework Phase 5.)*

More skills will follow once the Phase 4–5 pair lands and is battle-tested.

## Status

This repo is in early stages. `ai-driven-prd`, `init-claude-project`, and `generate-dev-plan` are the first three complete skills — covering Phases 1–3 of the framework. The "coming" entries describe the target shape of the collection — the rest will land as they're built.

## Credits

Structure and philosophy inspired by [mattpocock/skills](https://github.com/mattpocock/skills).

Repo: [el-frontend/software-engineering-skills](https://github.com/el-frontend/software-engineering-skills)
