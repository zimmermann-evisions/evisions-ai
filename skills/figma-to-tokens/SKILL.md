---
name: figma-to-tokens
description: >-
  Turn a finished Figma hi-fi design into tokens.css plus a static HTML/CSS
  preview, as the first stage of the evisions dev pipeline. Use when a Figma
  design is approved and the work moves from design into code — triggers
  include "prekloň dizajn do kódu", "vytiahni tokeny z Figmy", "figma to
  code", "sprav statický náhľad z dizajnu", "tokens.css", or a bare Figma
  frame link handed over with the intent to build it. Runs in Claude Code
  (needs filesystem + Figma MCP). Runs start to finish without asking, then
  stops once for human approval of the preview, then hands off to /impeccable
  extract, audit, normalize and polish. Does NOT build the Next.js app —
  that is a later stage after the preview is approved.
---

# Figma → tokens → static preview

First stage of the evisions dev pipeline: from an approved Figma hi-fi design
to `tokens.css` and a static HTML/CSS preview the human compares against the
design and approves.

**The point of this stage: get the design into code without losing it.**
Everything downstream (Next.js implementation, forms, integrations) is built
on these tokens. A wrong token here is inherited by every later step and gets
progressively more expensive to fix.

## How this runs

**Autonomously.** Steps 1–3 run start to finish without checking in. Don't
ask the human to pick token names, confirm structure, or approve intermediate
steps — it is mechanical work, and interrupting it wastes their attention.

There is **one stop**: the approval gate in step 4.

One possible extra pause: if the design has no variables and they have to be
created (step 0), confirm the grouping of near-identical values with the
human before writing them. That is a decision about their design, not
mechanical work.

Ask before starting only if a precondition is missing — and then guide the
setup rather than just reporting the problem (see Preconditions below).

## Preconditions — check first, guide if missing

Check all of these **before starting**, and report every gap at once. Finding
them one at a time, with a round trip each, is the annoying version.

- Running in **Claude Code** (needs filesystem access)
- **Figma MCP connected** — this skill reads the design through it
- **Impeccable installed** (`npx impeccable install`)
- **`/impeccable init` run once** (writes `PRODUCT.md`)
- A **Figma frame link with a `node-id`**, not a whole-file link

If something is missing, **walk the person through fixing it and then
continue** — don't just report it and stop. Most people hitting this are
setting the pipeline up for the first time and a bare error tells them
nothing.

How to test each and how to fix it: [`references/setup.md`](references/setup.md)

## Workflow

### 0. Check the design is ready — and fix it if not

Before extracting anything, run `get_variable_defs` on the target node.

**If it returns few or no variables while the design is visually complete,
the values are hardcoded** and extraction would produce a token file that is
mostly gaps.

In that case, **create the variables and bind them first** — don't proceed
with an empty token file and leave the human to patch it by hand. That breaks
the design↔code link the whole pipeline exists to maintain.

The work is mechanical but has one judgement call: grouping near-identical
values. **Show the human the inventory before creating anything** — deciding
that three similar greys were meant to be one colour is a decision about
their design, not cleanup.

Full procedure, API calls, and what typically cannot be bound:
[`references/design-prerequisites.md`](references/design-prerequisites.md)

Also check there: variable naming, value consistency, auto-layout coverage.
If the design is still being iterated, say so and wait — adding variables to
a moving design means redoing it after the next round.

### 1. Extract tokens → `tokens.css`

Read the design's variables through Figma MCP (`get_variable_defs` on the
target node) — the real defined values, not values eyeballed from a
screenshot.

Write them to `tokens.css` as CSS custom properties on `:root`.

- **`tokens.css` is the single source of truth.** Nothing downstream
  hardcodes a value that exists here.
- **Keep the names the designer used.** Don't invent a "nicer" taxonomy —
  when the designer renames a variable, the mapping has to stay obvious.
- **Missing variables get reported, not guessed.** A token file that looks
  complete but is half-guessed is worse than one with a stated gap, because
  nobody knows to check it.

Details: [`references/token-extraction.md`](references/token-extraction.md)

### 2. Build the static HTML/CSS preview

Plain HTML + CSS. No framework, no build step, no dependencies. It exists to
be looked at and approved, nothing else.

**Every visual value comes from `var(--token)`.** No hardcoded hex, px, or
font-family anywhere. This is not a style preference: if values are
hardcoded, the preview can look perfect while the tokens are wrong, and the
error only surfaces much later in the Next.js build.

Something in the design with no matching token is a **finding to report**,
not a gap to paper over with a literal value.

Properties that drift quietly — spacing, font weight, opacity, radii — are
listed in [`references/build-accuracy.md`](references/build-accuracy.md).
Take them from the design context and tokens, not from an impression.

### 3. Write the handover report

**Don't run a screenshot comparison.** The human has Figma open and compares
it themselves — that is faster and more reliable than an AI eyeballing two
images.

Instead, write a short report the reviewer can act on: what was built, token
gaps found, anything not reproducible in plain HTML/CSS, anything
deliberately different, and — most valuable — **anything you were unsure
about**, pointed at specifically.

Format and honesty rules:
[`references/build-accuracy.md`](references/build-accuracy.md)

### 4. Human approval gate 🚦

Present the preview and the report, and **stop.** A human confirms the design
survived the trip into code before anything is built on top of it.

**Never state that the preview matches the design.** This skill did not
verify that — the human does. Saying it removes the reason the gate exists.

### 5. Hand off to Impeccable

Once approved, the design system work continues with Impeccable — don't
duplicate what it already does:

```
/impeccable extract     # consolidate tokens + components into the design system
/impeccable audit       # technical quality checks (a11y, responsive, performance)
/impeccable normalize   # align anything that drifted from the tokens
/impeccable polish      # final pass, reruns the detector until clean
```

`extract` reads the code this skill just produced and writes `DESIGN.md`;
`normalize` then checks against it. That order matters — `normalize` has
nothing to compare against until `extract` has run.

## What this skill does NOT do

- **Does not build the Next.js app.** Separate, later stage — after the
  preview is approved.
- **Does not replace Impeccable's quality commands.** This skill gets the
  design into code faithfully; Impeccable judges design quality.
- **Does not decide the visual design.** The Figma file already decided it.
  If something in the design looks wrong, raise it — don't quietly improve
  it.

## Files
- [`references/setup.md`](references/setup.md) — testing each precondition
  and how to fix it
- [`references/design-prerequisites.md`](references/design-prerequisites.md) —
  what the design needs before step 1, and how to create + bind missing
  variables
- [`references/token-extraction.md`](references/token-extraction.md) —
  naming, structure, handling missing variables
- [`references/build-accuracy.md`](references/build-accuracy.md) —
  where drift happens, and the handover report format
