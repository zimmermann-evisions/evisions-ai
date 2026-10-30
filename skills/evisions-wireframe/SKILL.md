---
name: evisions-wireframe
description: >-
  Turns a structured wireframe brief (output of wireframe-intake) into an
  approved information architecture and a built wireframe in Figma, using the
  shared evisions Wireframe Kit. Use AFTER a brief exists — when someone says
  "build the wireframe", "assemble the homepage", "design the IA", "put the
  wireframe together in figma", "build the wireframe from this brief", or
  runs /evisions-wireframe. Orchestrates IA blueprint → Figma build → QA →
  responsive/copy → handling missing components → handover, with three human
  approval gates (IA sign-off, acceptance, kit promotion). Requires a Figma
  file with the Wireframe Kit library attached.
---

# evisions-wireframe

Orchestrator on top of `figma-use` (writing to Figma) and
`figma-generate-library` (only when a library-ready component needs to be
built). It carries the *workflow*, not the *content* — the concrete rules
live in the attached files; load them selectively per step, not all at once.

## When to use / not use
Use once a structured brief exists and it needs to become an IA + a built
wireframe. Do not use for: writing the brief itself (that's
`wireframe-intake` — if there's no brief, redirect there), a production app
build (`idea-plan-launch`), or editing a finished visual design outside the
wireframe phase.

## Preconditions (check before step 1)
- A structured brief exists (`wireframe-intake` output). If not, stop and
  redirect to `wireframe-intake`.
- The target Figma file has the Wireframe Kit attached as a library (Assets →
  Libraries). If not, say so — don't proceed blind.

## Workflow

### 1. Load the segment profile
Based on the brief's `Segment profile` field (`fintech` / `healthcare` /
`other`), view **only that one** matching `references/segment-<profile>.md`.
For `other`, load no profile — proceed with generic UX judgment.

### 2. Write the IA blueprint → 🚦 GATE 1
Fill out the format in `assets/ia-blueprint-template.md`. Section 3 (IA)
describes the **need/pattern in words** ("structured listing with price and
CTA"), not a binding component name — the exact kit mapping happens only in
step 3. Section 6 (conflicts + decisions) is the core — that's where judgment
shows, not just a stack of blocks.

This is a pure text output. **Stop and wait for approval.** Do not go into
Figma before a human signs off on the IA — disagreeing here is cheap;
redoing a finished wireframe is not.

### 3. Build v0.1 in Figma
For each blueprint section, check the kit via `search_design_system` —
always live, never from memory (no manifest exists, the kit changes).
Inspect the **internal structure** of candidates too, not just name/
description (import and walk `children` 2–3 levels deep) — otherwise you'll
miss cases where a block can be composed from primitives instead of hunting
for one ready-made component.

Decide per `references/component-conventions.md` (three paths: existing
block / composed from primitives / genuinely NEW). Dashed placeholder for
speculative gaps, full build only for recurring patterns.

**One `use_figma` call = one logical section.** An error in one call rolls
back the steps that ran before it *within that same call* — a large
multi-section script in one go is fragile. Verify with a screenshot after
each substantial section, not only at the end.

### 4. QA + swap variants → 🚦 GATE 2
Go through `references/acceptance-checklist.md`. A human picks the final
variants wherever taste decides. Unapproved items get fixed before acceptance.

### 5. Responsive + copy
Separate, named passes — not part of step 3. Breakpoints from the brief
(default 1440/768/390). The copy pass replaces lorem ipsum with realistic
text — this applies to the client wireframe only, NOT to components headed
for the kit (those stay generic, see step 6).

### 6. Missing component (if it came up in step 3)
Handle per the full extent of `references/component-conventions.md`: phase
(speculative vs. recurring pattern), semantic taxonomy, binding to kit color
tokens ("Color Schemes"), generic Lorem-ipsum content even when it originated
on a specific client project, **no real Figma interactions** (this is a
wireframe, not a hi-fi prototype) outside of already-documented exceptions.

### 7. Promotion into the kit → 🚦 GATE 3
A local candidate with a governance line in its `description`. Publishing is
always a manual button in Figma (Assets → Libraries → Publish) — no API
triggers it. The kit owner does this after review, not AI, even if the user
has direct file access.

### 8. Handover + feedback loop
Wrap up via `ai-handover`. Log any new gaps/patterns discovered during the
project into the relevant `segment-*.md` profile — the kit itself needs no
manifest (the live source of truth is `search_design_system`), but the
profiles should grow with every additional client in that segment.

## Files
- `assets/ia-blueprint-template.md` — format for step 2
- `references/segment-fintech.md`, `references/segment-healthcare.md` —
  domain patterns, load selectively per the brief
- `references/component-conventions.md` — full rules for step 3 and 6
- `references/acceptance-checklist.md` — full rules for step 4
