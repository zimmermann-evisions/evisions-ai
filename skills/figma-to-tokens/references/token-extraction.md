# Token extraction — `tokens.css`

## Where the values come from

Figma MCP `get_variable_defs` on the target node. These are the variables the
designer actually defined, not values read off a screenshot.

**Never substitute screenshot-eyeballed values for missing variables.** If a
color or spacing value has no variable behind it, that is a gap in the design
system and it gets reported — not silently filled in. A `tokens.css` that
looks complete but is partly guessed is more dangerous than one with an
explicit hole, because nobody knows which parts to distrust.

## Naming

Keep the designer's names. If Figma has `Color Schemes / Text`, the token is
`--color-text`, not `--fg-primary` because that reads better in code.

Reason: when the designer renames or adds a variable, someone has to map the
change into code. A one-to-one mapping makes that a lookup. A "cleaner"
invented taxonomy makes it an investigation every time.

Mechanical transform only:
- lowercase, kebab-case
- `/` in the Figma path becomes `-`
- keep the category prefix so tokens group naturally

```
Figma: Color Schemes / Background   →  --color-background
Figma: Spacing / md                 →  --spacing-md
Figma: Type / Heading / L           →  --type-heading-l
```

## Structure

Everything on `:root`, grouped by category with a comment per group. Plain and
boring on purpose — this file gets read by people looking for one value.

```css
:root {
  /* Colors */
  --color-text: #212322;
  --color-background: #ffffff;

  /* Spacing */
  --spacing-sm: 8px;

  /* Typography */
  --font-heading: "...";
}
```

## Modes / themes

If the Figma collection has multiple modes (light/dark, brand variants),
extract each mode. Default mode on `:root`, others under a selector or media
query. Don't collapse modes into one set — that silently discards a decision
the designer made.

## What gets reported, not solved

Report these to the human rather than deciding alone:

- **Missing variables** — a value used visually in the design with no
  variable behind it
- **Inconsistencies** — two near-identical values that look like one was
  meant (e.g. `#212322` and `#212323`), which usually means a slip in the
  design file worth fixing at the source
- **Unbound values** — hardcoded colors/spacing inside the Figma design that
  never became variables

Each of these is a decision for the designer or the kit owner, not a gap to
quietly patch in code. Patching them in code means the design file and the
code disagree from day one.
