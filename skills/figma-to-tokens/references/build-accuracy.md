# Build accuracy + handover report

The human compares the preview against Figma themselves — they have the design
open. This skill's job is to **build it accurately and report honestly**, not
to run its own screenshot comparison.

That split only works if the report is genuinely useful. A report that says
"done, matches the design" tells the reviewer nothing and wastes the gate.

## While building — where drift happens

These are the properties that go wrong quietly. Take them from the design
context and tokens, never from an impression of how it looks:

- **Spacing** — padding and gaps per element, and the vertical rhythm between
  them. The most common source of "looks slightly off, can't say why".
- **Typography** — size, weight, line-height, letter-spacing. Weight
  especially: Medium vs. SemiBold is easy to get wrong and changes the feel
  of the whole page.
- **Colors** — exact token values including opacity. A color at 60% opacity
  is not a lighter shade of the same color, even where they look alike.
- **Borders and radii** — width, color, radius. If the brand has a rule here
  (e.g. sharp corners only), respect it.
- **Alignment and element order** — nothing reordered, nothing dropped.
- **Proportions** — relative sizes between elements, not just absolutes.

## The handover report

Write this at the approval gate. Keep it short, but make every line something
the reviewer can act on.

**1. What was built** — which sections, in one line each.

**2. Token gaps** — every value in the design with no variable behind it.
State what was done instead. These are decisions for the designer or kit
owner, not things to quietly patch in code.

**3. Anything not reproducible in plain HTML/CSS** — effects, blend modes,
layouts that need JS. Say what was approximated and how, so the reviewer
knows exactly where to look sceptically.

**4. Anything deliberately different from the design, and why.**

**5. What you are unsure about** — the most valuable part. If a spacing value
was ambiguous, or a font weight was a judgement call, name it and point the
reviewer at it.

## Honesty rules

- **Never say the preview matches the design.** The skill did not verify
  that; the human does. Claiming it removes the reason the gate exists.
- **Never present an approximation as exact.** If something was eyeballed,
  say which part.
- An empty "unsure" list should be rare. On a real design there is almost
  always something ambiguous — reporting none usually means it was not
  looked for.
