---
name: tech-spec
description: >-
  Turn an approved design and brief into a technical specification and a set
  of small, independently buildable implementation plans — the step between
  "the design is approved" and "someone starts coding". Use when a web or app
  build is about to start and it is not yet written down what exactly gets
  built: triggers include "technická špecifikácia", "tech spec", "rozdrob to
  na plány", "implementačný plán", "čo presne ideme stavať", "pripravme
  zadanie na build", or when the evisions dev pipeline reaches the spec
  phase after the static preview is approved. Runs in Claude Code so the spec
  lives in the repo. Reads the existing brief and design instead of
  re-interviewing. Ends at a human approval gate before any implementation.
---

# Technical specification

The step between **"the design is approved"** and **"someone starts
building"**. Produces two things:

1. A **technical specification** — what exactly gets built, how, with what
2. A set of **small implementation plans** — the spec cut into pieces that
   can each be built and tested on their own

**Why this step exists:** changing a plan costs a sentence. Changing a
finished implementation costs a rebuild. Everything expensive to get wrong
is decided here, on purpose, while it is still cheap.

## Run this with Opus

The spec and the implementation plans are where errors are most expensive to
correct later — a wrong assumption here is inherited by every plan built on
it. **Use Opus for this skill, not a smaller model.**

This cannot be enforced from inside the skill; it is a decision made when the
session starts. If the model running this is not Opus, say so once at the
start and let the person decide whether to continue.

## Where it runs

**Claude Code**, in the project repo. The spec has to exist as a file that
implementation plans reference and that reviewers can read — not as chat
output that scrolls away.

## First: read, don't interview

By the time this runs, most inputs already exist. **Read them before asking
anything.** Re-asking what the brief already answers wastes the person's time
and signals the spec was written without reading the project.

| Input | Where it lives | Gives you |
|---|---|---|
| Client brief | `wireframe-intake` output | goals, KPI, segments, constraints, legal |
| Approved design | Figma | what is actually on the page |
| Tokens + preview | `tokens.css`, static preview | how it looks in code |
| Product truth | `PRODUCT.md` (from `/impeccable init`) | audience, purpose, voice |

Ask only about what genuinely is not in any of them — see
[`references/spec-sections.md`](references/spec-sections.md), which marks per
section what is usually already known and what must be asked.

## Workflow

### 1. Gather and read the inputs

If a core input is missing (no brief, no approved design), **say so and
stop** — a spec written without them is guesswork dressed as a plan.

If the design is approved but the static preview stage has not run, flag it:
the spec can be written, but token and feasibility questions will stay open
until it does.

### 2. Write the specification

Fill [`assets/spec-template.md`](assets/spec-template.md).

Section-by-section guidance, including what is usually already answered and
what to ask about:
[`references/spec-sections.md`](references/spec-sections.md)

Hard constraints that shape the spec whether anyone likes them or not
(hosting choice, CI checks, forbidden services, consent-before-measurement):
[`references/evisions-constraints.md`](references/evisions-constraints.md)

**Mark every unknown explicitly.** A spec that looks complete but quietly
guessed at the form's destination or the hosting target is worse than one
with a stated `⚠ OPEN` — nobody knows which parts to distrust.

### 3. Cut it into implementation plans

Vertical slices that can each be built and tested alone — not horizontal
layers. How to cut, how big, what order:
[`references/implementation-plans.md`](references/implementation-plans.md)

### 4. 🚦 Approval gate — spec and plans, before any code

Present both and **stop.** This is the whole point of the step.

State plainly:
- What was decided, and what it rests on
- Every `⚠ OPEN` item and who has to close it
- Anything from the brief or design that could not be satisfied technically,
  and what was done instead
- Which open items **block the start** vs. which can be resolved during the
  build

Do not start implementing after approval unless asked — the build is its own
step, run with `web-build` (client sites) or `idea-plan-launch` (apps).

## What this skill does NOT do

- **Does not write the code.** That is `web-build` or `idea-plan-launch`.
- **Does not redo the PRD.** `prd-creator` covers WHAT/WHY. This is HOW.
  If the problem and goals were never defined, go there first.
- **Does not redesign.** The design is approved. If something in it cannot
  be built as drawn, raise it — don't quietly change it.
- **Does not decide the stack.** That follows from the hosting choice and
  the evisions rules, not from preference.

## Files
- [`assets/spec-template.md`](assets/spec-template.md) — the output contract
- [`references/spec-sections.md`](references/spec-sections.md) — how to fill
  each section, what is usually already known
- [`references/implementation-plans.md`](references/implementation-plans.md) —
  cutting the spec into buildable plans
- [`references/evisions-constraints.md`](references/evisions-constraints.md) —
  hosting, stack, CI, consent and data rules that constrain the spec
