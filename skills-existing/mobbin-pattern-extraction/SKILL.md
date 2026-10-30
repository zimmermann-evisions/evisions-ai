---
name: mobbin-pattern-extraction
description: >-
  How to use Mobbin or screenshot-based UI references without copying designs.
  Trigger whenever references influence a component, section or page design,
  e.g. "search Mobbin", "find hero examples", "look at how other sites do X",
  or "get inspiration from Mobbin". Work component-by-component. A human UX
  specialist selects and mixes specific variants (e.g. visual from one,
  layout from another) before building; never auto-pick combinations.
  Extract structural and UX principles from each reference, keeping them
  labeled and separate rather than merging sources. Run a distance-from-source
  check and require human approval before moving to the next component.
  The goal is to avoid designs traceable to a single real site, reducing
  copyright and trade-dress risk.
---
# Mobbin Pattern Extraction

Mobbin (and similar tools) return real screenshots of real, live products.
Those screenshots are someone else's copyrighted work. The goal of this
skill is to get the *value* of seeing how others solved a UX problem without
reproducing anyone's specific solution — so the output is legally safer and,
as a side effect, more original.

## When to use / not use
Use every time a Mobbin (or Screen/Flow) search result is about to shape an
actual design decision — not just when someone explicitly asks for this
process. If you're about to look at 1–2 screenshots and translate what you
see into a component, this skill applies even if nobody said the word
"mobbin-pattern-extraction."

Do not use for: pure content/copy inspiration with no visual component
involved, or internal evisions assets (own component library, own past
projects) — the risk this skill addresses is specific to *other companies'*
live products.

## Mode — ask first
At the start of the phase ask:
> "Chceš si komponenty vyberať sám, alebo mám vybrať ja?"
- **Designer picks** → the workflow below as written (step 3 + per-component gate in step 6).
- **Claude picks** (non-designer, or SCS design sample) → auto mode below
  replaces step 3 and changes the gate in step 6.

## Auto mode — Claude chooses the components
Steps 1–2 (search, labeled variants) and step 4–5 (build from own system,
control check) stay exactly the same. Instead of waiting for a
specialist's instruction in step 3, Claude picks a combination of variants
per component using these criteria:

| Criterion | Meaning |
|---|---|
| Site goal | supports the main conversion from the brief (e.g. leads → form close to the hero) |
| Content we actually have | no testimonials → no testimonial-led hero; no photos → no photo-dependent component |
| Industry | rules from the segment profiles (`evisions-wireframe/references/segment-*.md`) |
| Brand | fits the brand manual (colors, type, character) |
| UX best practices | proven patterns; for landing pages `ai-web-design/references/landing-page-ux-standard.md` |
| Page consistency | all components read as one site, not a collage of sources |
| Visual quality | per `ai-web-design/references/visual-quality-review.md` (+ `frontend-design`) — judged on how the components look together, from a screenshot |
| Mobile + accessibility | works on mobile, meets the accessibility minimum |

For each choice write one or two sentences **why**.

**Gate in auto mode — one approval for the whole page**, not after each of
~12 components (a non-designer doesn't want 12 stops). Show the proposal
**as an image** (quick sketch or Figma screenshot), not just a text
description — approving from text is too abstract for a non-designer. Per
component add the reasoning and the control check result. Three options:
- **Approve** → continue.
- **Change a component** → they name it; Claude offers another combination,
  or a designer finishes it manually.
- **"Nechám to na tebe"** → Claude continues with its own choice and the
  person judges only the finished visual.
The control check is never skipped and is always shown, whichever option
is chosen.

## Workflow

### 1. Pull multiple variants, never one
Search for the component across **4–6 different sources** minimum, not one.
A single reference gives you nothing to triangulate against — the whole
method depends on cross-referencing enough variants that no single one
dominates the result.

If working through a full page (typically **~12 distinct component
types** per page — hero, nav, benefits grid, how-it-works, proof, pricing,
FAQ, footer CTA, etc.), do this **one component type at a time**, never
the whole page in one pass. Before searching, check whether this component
type was already extracted earlier in this project — if so, reuse those
labeled variants (step 2) instead of re-searching Mobbin, unless the UX
specialist explicitly asks for a fresh search.

**Empty or broken results:** if a returned variant is empty (e.g. a blank
grey screen) or unreadable, discard it and pull a replacement from another
source so there are still 4–6 usable variants.

**Weak coverage for a segment** (e.g. real estate returns mostly SaaS
sites): say so, then try in this order — (a) Mobbin's website **sections**
search instead of app screens, (b) adjacent segments with similar needs,
(c) ask the person for references. Never pad the set with irrelevant
variants just to reach the count.

If token cost is a concern: this only gets expensive if you re-run broad
Mobbin searches repeatedly per component. A handful of screenshots analyzed
once per component is cheap; the thing to avoid is re-searching the same
component many times across a session.

### 2. Extract principles per reference — keep them separate, don't merge
For each reference, write down structure, not appearance. Ask "what is this
doing," never "what does this look like." **Keep each reference's
extraction labeled and separate (Variant 1, Variant 2, …) — don't merge
them into one combined pattern list.** The UX specialist needs to pick and
mix across labeled variants in step 3, which is impossible if they've
already been blended.

Extract per variant:
- layout logic (e.g. "headline left, visual right, asymmetric grid")
- hierarchy (what's read first, second, third)
- interaction pattern (e.g. "two-tier CTA: primary button + secondary text link")
- content pattern (e.g. "proof numbers sit directly under the CTA, not in a
  separate section")
- spacing philosophy (e.g. "generous whitespace, one idea per screen height")
- visual character (e.g. "bold flat color blocks" vs. "soft photographic")

Explicitly discard (for every variant):
- the specific image, illustration or photo used
- exact colors, exact typeface choices
- exact spacing values, exact shapes/iconography
- the literal copy text

Write the result as **labeled prose principles**, e.g.:
```
Hero — Variant 1 (source: [type of site, not named/linked]):
- headline left, visual right, asymmetric grid
- two-tier CTA (primary button + secondary text link)
- soft photographic visual character

Hero — Variant 2:
- centered headline, no visual, large type
- single CTA, proof logos directly below
- bold flat color blocks

Hero — Variant 3:
- headline left, visual right, symmetric grid
- proof numbers sit low in the section
- soft photographic visual character
```
Never write "use this exact layout / this image / this shape" for any
variant — if you catch yourself describing a specific reference's literal
appearance instead of its structural pattern, stop and re-derive.

### 3. Hand labeled variants to the UX specialist — wait for their instruction
Present the labeled variants (step 2) to the UX specialist and **stop**.
Don't guess a combination or pick a "best" variant yourself — the
specialist gives an explicit instruction naming what to take from where,
e.g. *"chcem vizuál z varianty 2 a layout z varianty 3."* Only proceed to
step 4 once that instruction exists.

If the specialist doesn't like any of the variants, that's a valid answer
too — go back to step 1 with a narrower or different search, don't force a
combination out of a weak set.

### 4. Build from your own system, exactly per the instruction
Implement **only what the specialist specified** using your own component
library and the client's brand (colors, type, imagery, iconography).
Nothing from step 1's screenshots — not an image, not a color value, not a
shape — goes into the output directly, regardless of which variant it came
from.

### 5. Control check — before the result is shown
Run this on the combined result, every time:

- [ ] **Traceability:** could someone look at the output and point to one
      specific source it was "copied from"? If yes → stop, re-derive.
- [ ] **Shared-attribute limit:** does the result share more than ~2
      characteristic attributes with any single one source at once (e.g.
      layout + color + shape together)? If yes → stop, re-derive.
- [ ] **Origin check:** did the visual realization come from your component
      library and brand, not from a screenshot? Must be yes.
- [ ] **Synthesis check:** is this a new combination drawn from multiple
      sources, not a close copy of one? Must be yes.

Show the specialist the check itself, not just a pass/fail — name which
attributes were compared against which variant, so a "no" in step 6 is
informed, not a guess.

### 6. Human gate — stop, don't auto-retry
🚦 Wait for explicit confirmation before moving to the next component.

- **"áno, pokračuj"** → mark this component done, move to the next one
  (back to step 1).
- **"nie"** → this means the specialist takes over and iterates this
  component **manually**. Don't automatically re-derive, re-search, or try
  another combination on your own — that decision now belongs to the
  human, not to another automated pass. Wait for new direction from them
  before touching this component again.

Never start the next component before this gate resolves, even if there's
capacity to keep going.

## Notes
- This is a design-risk reduction method, not legal advice. It substantially
  lowers the chance of a traceable copy but doesn't replace legal review for
  a client sensitive to this (e.g. a competitor of one of the reference
  sources).
- Mobbin's own terms claim ownership over derivative works created through
  their service in some readings — another reason step 3 must build from
  your own library, not from anything exported out of Mobbin itself.
- Applies regardless of which skill or workflow triggered the Mobbin search
  — `evisions-wireframe`, a hackathon Framer build, or an ad hoc design
  question in chat.
