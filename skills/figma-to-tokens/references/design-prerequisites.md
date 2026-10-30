# Design prerequisites — and fixing them

Check these **before extracting tokens**. If the design fails them, the
extraction produces a token file full of gaps, and every later stage inherits
the mess.

When prerequisites are missing, **fix the Figma file first** (see below).
Don't proceed with a half-empty token file and let the human patch it by hand
— that breaks the design↔code link the whole pipeline depends on.

## The checklist

**1. Variables exist and are bound** ← the one that actually matters

Colors, spacing and typography must be defined as Figma **Variables** *and*
bound to the elements. A color that merely looks right is not enough — it has
to be bound, or `get_variable_defs` returns nothing for it.

Quick check: `get_variable_defs` on the target node. If it returns few or no
variables while the design is visually complete, the values are hardcoded.

**2. Variable names are meaningful**

This skill deliberately keeps the designer's names. `Color / Text / Primary`
becomes `--color-text-primary`. But `Color 1`, `Color 2` becomes `--color-1`,
`--color-2` — technically valid, useless to read.

**3. Values are consistent**

Near-identical values (`#212322` vs `#212323`) usually mean a slip in the
design file. Better fixed at the source than inherited into code.

**4. Auto-layout throughout**

Spacing can only be read reliably from auto-layout. Manually positioned
elements mean guessed padding and gaps.

**5. One specific frame, with a node id**

Not a whole-file link. Without a node id, ask — don't guess which frame.

**6. The design is finished**

This stage converts an approved result. It is not a tool for iterating on a
design that is still moving.

## Fixing a design with no variables

If the design is visually complete but values are hardcoded, create the
variables and bind them. This is real work but it is mechanical, and doing it
once is far cheaper than maintaining two diverging sources of truth.

### Order of work

**1. Inventory what is actually used.** Walk the design tree and collect every
distinct fill color, stroke color, font size, font weight, padding and gap
value. Group near-identical values — that is where the design slips surface.

**2. Show the inventory to the human before creating anything.** Grouping
decisions ("these three greys look like one intended colour") are judgement
calls about the design, not mechanical cleanup. Getting them wrong bakes a
wrong assumption into the design system permanently.

**3. Create the collection and variables.**

```js
const collection = figma.variables.createVariableCollection("Design Tokens");
const v = figma.variables.createVariable("color/text/primary", collection, "COLOR");
v.setValueForMode(collection.modes[0].modeId, { r: 0.13, g: 0.14, b: 0.13 });
```

Use `/` in variable names — Figma renders it as a folder structure, and it
maps cleanly to `--color-text-primary`.

Types: `COLOR` for fills and strokes, `FLOAT` for spacing, radii and font
sizes, `STRING` for font families.

**4. Bind them to the elements.**

```js
// fills and strokes
node.fills = [figma.variables.setBoundVariableForPaint(
  { type: "SOLID", color: hex }, "color", variable
)];

// numeric fields — padding, gaps, radii, font size
node.setBoundVariable("paddingLeft", variable);
node.setBoundVariable("itemSpacing", variable);
```

Binding is what makes extraction work. A variable that exists but is bound to
nothing is invisible to `get_variable_defs`.

**5. Verify by re-extracting.** Run `get_variable_defs` again and confirm the
values now come back. Don't assume the binding took.

### Be honest about what could not be bound

Not everything binds cleanly. Gradients, effects, mixed text styles and some
nested instance properties are awkward or impossible. **Report exactly what
was left hardcoded and why** — a partial job described as complete is worse
than a partial job described accurately, because nobody knows what to check.

### When not to do this

If the design is still being iterated, adding variables now means redoing it
after the next round. Say so and wait — the prerequisite is a *finished*
design, not just a variable-free one.
