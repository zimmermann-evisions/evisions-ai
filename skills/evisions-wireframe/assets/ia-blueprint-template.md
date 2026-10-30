# IA Blueprint — [Client] · [Project]

> Output artifact for `evisions-wireframe` step 2 (IA). This is GATE 1 — text
> that a client/colleague approves BEFORE anything is drawn in Figma. Keep it
> text-based and cheap to change; disagreeing about the hero decision here is
> cheaper than redrawing a finished wireframe. Do NOT rename sections —
> both the human and the downstream build step read them by this structure.

**Brief ref:** [link/name of the `wireframe-intake` output this is based on]
**Segment profile:** fintech / healthcare / other
**Breakpoints:** [from the brief, default 1440 / 768 / 390]

---

## 1. UX diagnosis
[Main problem — one or two sentences, ideally quoting/paraphrasing the brief
if the client named it themselves. Causes. User barriers + business barriers.
If the brief has data (REDESIGN mode), the strongest data signal belongs
here, not only in the composition section.]

## 2. Design principles (4–6)
[Short, nameable principles that the decisions below are built on — not
generic UX phrases, but something you can reference in section 6 when a
conflict comes up. Check `references/segment-<profile>.md` — the segment
profile has its own core principles (e.g. fintech: "confidence before
conversion"; healthcare: "findability + clarity under stress"), carry them
over here if they fit, or explain why not.]

## 3. Information architecture (section order)
> Format per section, one line each:
> **Name — goal — user question — business purpose — component**

1. [Section] — [goal] — "[the question the user is asking]" — [business
   purpose] — [NEED/PATTERN, e.g. "structured listing with price and CTA" or
   "compact wrap-grid for many categories" — not a binding component name,
   that's resolved at build time]
2. ...

[This describes a NEED, not a promise of a specific component — the exact
component (or a composition of primitives, or NEW) is found and verified at
build time via search_design_system. If you're already confident about the
exact component, you can name it as a suggestion, but the build can swap in
a better fit without requiring a new IA approval — as long as the NEED/
pattern doesn't change, only its concrete realization.

Sections the brief allows to be cut/merged (see the "Mandate to cut" field
in the brief) drop out entirely here — don't add them back just because they
existed on the old site.]

## 4. Composition
> Only for conversion/decision-critical sections — not every one from
> section 3. Format: **Purpose · Why here · Conversion role.**

- **[Section].** *Purpose:* [what it should let the user do]. *Why here:*
  [why at this position, not another]. *Conversion role:* [how it
  contributes to the conversion/goal from the brief].

## 5. CTA strategy
- **Primary (commitment):** [CTA text] — [where it appears]
- **Secondary:** [CTA text] — [where]
- **Inline:** [where, after what]
- **Sticky:** [if relevant, when it appears]

## 6. Stakeholder conflicts + key decisions
> This is the heart of GATE 1 — this is where judgment shows, not just a
> stack of blocks. For each conflict: **who wants what, what you chose, what
> we gain, what we lose.** Don't average conflicts away — name them.

- **[Conflict/decision].** [Who wants what.] **Decision:** [what you chose].
  *Gain:* [...]. *Loss:* [...].

## 7. Responsive notes
[Major desktop → mobile changes — not a full responsive spec (that belongs
in the build), only decisions that change structure/order, not just sizes.]

## 8. Open questions for approval
[What exactly you need from the client/colleague before going into Figma.
This is a list to be directly answered before GATE 1 approval — not
rhetorical questions.]
