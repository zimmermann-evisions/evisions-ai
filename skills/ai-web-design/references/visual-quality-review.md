# Visual quality review

> Used whenever Claude judges how something looks: auto component choice
> (mobbin-pattern-extraction), reviewing a design, and visual QA of the
> built site (web-build). Builds on the public `frontend-design` skill —
> read it too; it covers aesthetic direction, token planning, and the
> typical "AI-generated look" to avoid.
>
> Judge from a **screenshot** (Figma screenshot, or the rendered page on
> desktop + mobile), never from code or a description alone.
>
> Taste is partly subjective: Claude reliably catches errors (spacing,
> weak hierarchy, inconsistency, generic look) and judges against
> principles, but final art direction belongs to a human — ideally a
> designer.

## Criteria
| Criterion | What's judged |
|---|---|
| Hierarchy | at a glance it's clear what matters most, what's second, where to click |
| Spacing | same gaps for same things, enough whitespace, nothing cramped |
| Alignment | elements sit on a shared grid, nothing drifts |
| Typography | clear size scale, at most two typefaces, line length not too long |
| Color | balanced, one strong color for actions, sufficient contrast |
| Consistency | buttons, cards, icons look the same across the page |
| Visuals | good quality, one style, support the content |
| One memorable element | one thing stands out, the rest stays calm |
| No "AI look" | none of the generic template tells listed in `frontend-design` |
| Mobile | looks as good on a phone, not just "works" |

## Output
A list: what's fine, and what to improve (specific: which element, what's
wrong, proposed fix).
