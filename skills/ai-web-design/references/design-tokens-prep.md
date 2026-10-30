# Design tokens — prep step

> **The work itself is done by the `figma-to-tokens` skill**, in Claude Code
> — not by hand and not in this chat. This file explains what tokens are and
> why they matter; the skill has the actual procedure, including creating
> Figma Variables when the design has none.
>
> **Build paths only (1 New, 2 Redesign, 3 PPC, 4 SEO).** Paths 5 (wireframe
> only), 6 (design only) and 7 (SCS pitch) never reach a build, so they never
> need tokens — a design pitch for a client is delivered as an image or PDF,
> not as code.

## What it's for
Design tokens are named style values (e.g. `color-primary = #D0021B`,
`space-4 = 16px`). The site code uses only the names, never raw values.
Change a value once in the tokens file → it changes on every page and in
every place automatically (100 pages, 100 places, one edit).

## Condition — a prepared Figma file
Ideally the design uses **Figma Variables** (colors, spacing, radius, sizes)
and **Styles** (text styles, effects) rather than values typed into each
element.

If values are hard-coded, this is **not a blocker** — `figma-to-tokens`
detects it and creates the variables first, binding them to the elements.
It shows the value inventory and asks before grouping near-identical values,
since deciding that three similar greys were meant to be one colour is a
decision about the design.

## Procedure
Run the **`figma-to-tokens`** skill in Claude Code with a link to the
finished frame (needs a `node-id`). It handles the whole stage: checks the
design, creates variables if missing, reads them through the Figma connector,
writes `tokens.css`, builds a static HTML/CSS preview, and stops for human
approval before anything else is built on top.

After approval, design-system quality continues with Impeccable:
`/impeccable extract → audit → normalize → polish`.

Then `web-build` uses only these tokens — never a raw value in the code.

If the client has a brand guide or brand manual minimum, its values must
match the Figma variables; if they differ, ask which one is right.

## Changing a value later
Change it in Figma → re-run steps 2–3 → the site picks it up everywhere.
Or, for a quick fix, change it in `tokens.json`/`tokens.css` — then also
update Figma so the two don't drift apart.

## Notes on Figma's own options
- Figma stores tokens natively as Variables and shows them in Dev Mode.
- Exporting them as a file isn't a one-click default: community plugins
  (e.g. Tokens Studio) or the Variables REST API (limited to certain Figma
  plans — check) can do it. Reading them through the Figma connector
  avoids both.
