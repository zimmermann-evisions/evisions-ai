# Component Conventions — new wireframe component

Load at GATE 3, when a section in the IA blueprint is marked "⚠ NEW," when a
placeholder from v0.1 needs to be fleshed out — or any time during the build
when the kit clearly lacks a fitting block. Goal: if a component is built
library-ready, promoting it into the kit should be sign-off + publish, not a
rebuild.

## Check the kit first — not just by name/description
`search_design_system` is the live source of truth (no static manifest).
But top-level names alone aren't enough — **check the internal structure**
of candidates too (import and walk `children` 2–3 levels deep). The
name/description alone often won't reveal that two or three smaller
primitives can be composed into a working block without needing one big new
component.

## Three paths, not two
1. **An existing kit component fits directly** → use it, done.
2. **It can be composed from existing primitives** (e.g. a text input +
   filter chip row + icon; a custom wrap-grid in the kit's chip style) →
   build it directly in the wireframe file, no separate registration
   needed. Add a small canvas note ("⚙ Composed from kit primitives —
   candidate for a permanent component once validated") so it's easy to
   find later if the pattern repeats.
3. **Genuinely new** → decide the phase by recurrence:
   - **Speculative/one-off** → a cheap placeholder (dashed frame + label).
     Don't invest in full library-ready quality for something that might
     be cut.
   - **Recurring pattern** (will show up across multiple clients in the
     segment) → build it fully library-ready right away, per the rules
     below.

## Library-ready component — what it actually means
This is the heavier process (`figma-generate-library` skill, not just
`figma-use`). For one new component in an already-existing kit, **phases 0–2
aren't needed** (the kit already has foundations) — go straight to phase 3
(component) with inspection of existing tokens:

- **Name:** semantic taxonomy — `Category / Type /`, e.g.
  `Carousel / Tag Navigation /`.
- **Description (mandatory):** what it is + when to use it + conversion role
  + governance label (see below). This field is what makes the kit
  navigable on the next project instead of blind guessing.
- **Variants:** a mandatory `Breakpoint` variant, exact variant names
  `Breakpoint = Desktop` / `Breakpoint = Tablet` / `Breakpoint = Mobile`
  (spaces around `=` — that's exactly how the kit itself has them). Widths:
  1440 / 768 / 390.
- **Color tokens:** the kit has a "Color Schemes" collection with 4
  variables — `Text`, `Foreground`, `Background`, `Border` — aliasing the
  primitives `Color/Neutral Darkest` (near-black) and `Color/White`. Bind
  fills via `figma.variables.setBoundVariableForPaint`, not hardcoded
  colors. The Border token actually resolves to near-black — for a subtle
  card/chip outline that may be too strong; if you use a literal instead of
  the token, write why in the component's description (so the kit owner can
  weigh it in deliberately, not silently diverge).
- **Auto-layout:** FILL horizontal, HUG vertical (so it fits a 1440 column
  like the rest of the kit).

## No prototype interactions — it's a wireframe, not a hi-fi prototype
Do not build real Figma interactions (`setReactionsAsync`, `SCROLL_TO`,
etc.), even for patterns that are inherently click-based (tab navigation,
scroll-to, toggling). A wireframe communicates structure and intent
visually and through description, not through a working prototype. If the
behavior is non-trivial, name it in words in the component's `description`
field (e.g. "tags act as quick navigation to the matching card below") —
that's enough for a dev/designer to know what to build/resolve later,
without us having to actually simulate it in Figma.

## Generic placeholder content — always, even for client-sourced work
Kit text is English Lorem-ipsum style regardless of the client's target
market/language — "Section heading goes here," "Category one"–"five," "Card
heading goes here," "Learn more →." A new component must not carry a
specific client's content — even if it originated on their project, it goes
into the kit with generic content. Real, localized copy always comes one
step later, outside the component itself.

## Two technical gotchas (expensive to rediscover)
- **`resize()` silently clears AUTO/HUG sizing.** Calling `node.resize(w,h)`
  on an auto-layout frame resets both `primaryAxisSizingMode` and
  `counterAxisSizingMode` to `FIXED` — regardless of which axis you meant to
  change, and even if you'd previously set `AUTO`. Consequence: content
  silently gets clipped to a small width/height. **Fix:** set the AUTO/HUG
  sizing mode AFTER the last `resize()` call on that node, never before.
  Applies cascading — if you resize a parent, also check its AUTO-sizing
  children, not just the parent itself.
- **Don't mix up which axis governs width vs. height.** For a `HORIZONTAL`
  layout frame, `primaryAxisSizingMode` governs width and
  `counterAxisSizingMode` governs height. For a `VERTICAL` layout frame it's
  the reverse. Setting the wrong one silently produces a clipped or
  mis-sized result that looks like the gotcha above but isn't — check which
  axis you actually need before "fixing" it.
- **One call = one atomic transaction.** If a script in `use_figma` fails
  partway through, the steps that succeeded before the error are ALSO
  rolled back — it's not a partial write. When building something with
  unknown internal structure (especially the "check the kit" step above),
  keep one call to one logical section/component, not one large
  multi-section script.

## Governance — until it's promoted
- **Local candidate:** a dashed placeholder (if the phase is "speculative")
  or a full component set with a governance sentence directly in the
  `description` field: *"NOT IN THE KIT — local candidate, awaiting review
  and publishing by the kit owner."* A dashed outline doesn't suit a
  full component set — governance belongs in the description, not the
  frame color.
- **Don't write to the shared library.** You can physically move a
  component into the Wireframe Kit file (copy-paste, or via the API if you
  have file access) — but the actual **publish action is always a manual
  button in Figma**, no API/skill triggers it. Promotion (review + Publish)
  is done by the kit owner, not AI.

## Handover output
For every NEW component (phases 2 and 3), write for the kit owner:
1. Name + description as-is (ready to copy-paste/review).
2. Why it was created + which segments/projects it's likely to recur on
   (basis for the promotion decision).
