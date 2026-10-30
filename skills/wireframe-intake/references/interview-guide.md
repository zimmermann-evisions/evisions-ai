# Interview Guide — wireframe-intake

Question bank and handling rules for filling `assets/brief-template.md`. Ask
grouped, 2–4 at a time, most decision-relevant first. Ask in the client's own
language; keep template field labels unchanged regardless.

## Golden rules
- **Extract before you ask.** If the user provided any text, mine it first and
  only ask about genuine gaps.
- **Don't dump.** Two to four questions per turn, not the whole list.
- **One follow-up max** per field, then mark `⚠ MISSING` and move on.
- **Concrete over abstract.** Push for numbers and real sums, not slogans.
- **Don't design.** If the user starts describing sections/layout, capture it
  under "Must-have content" and steer back — layout is the next skill's job.

## Per-section questions

### 1. Project & context
- Who is the client and what industry are they in?
- Which markets and languages does the site run in?
- Is it a marketing site, a product, e-commerce, something else?

### 2. Goals & KPI  *(most important section)*
- What is the main business goal of this site? What has to happen for this
  project to be a success?
- How will you measure it — one primary metric?
- (REDESIGN) What is today's value of that metric, and where do you want it?
- Any secondary goals (trust, bounce rate, time on site)?

### 3. Segments
- Who are the main types of visitors? (2–4 segments is enough)
- For each: what is their most important question, and what are they most
  afraid of?
- If the client doesn't know, offer an industry hypothesis and get it confirmed.

### 4. Data *(REDESIGN only — highest-value input)*
- Do you have analytics? Traffic, traffic sources?
- Where is the biggest funnel drop-off? What converts best?
- Is there an element/section with above-average engagement?
- If there's no data → mark `⚠ MISSING` and say it's the most important gap
  for a redesign (without it, prioritization happens blind).

### 5. Content
- What MUST appear on the site (non-negotiable sections/info)?
- **Am I allowed to cut and merge existing sections, or does the structure
  need to stay as-is?** (This mandate is critical — without it we end up at
  "everything is important.")
- Anything explicitly to NOT do?

### 6. Brand, tone & design direction
- What tone should the site have? Any reference sites (like / dislike)?
- Is there a brand guide / design system?
- What to avoid visually?

### 7. Hard constraints
- Legal/compliance requirements? (important for fintech, healthcare, insurance)
- Target WCAG level? (default AA; fintech B2C and healthcare each have their
  own specifics — see the relevant segment profile in evisions-wireframe,
  don't decide this blind here)
- How many language versions?

### 8. Scope
- Which pages are we wireframing now (just the homepage, or more)?
- Breakpoints? (default 1440 / 768 / 390 — confirm or adjust)

## Edge cases

**Almost no input ("client wants a new site, nothing else").**
Start with section 2 (Goals) and 3 (Segments) — nothing else makes sense
without them. Fill the rest over more rounds. A skeleton with gaps beats
waiting for complete info.

**Rich brief.**
Fill everything from the text, then confirm only the 2–3 most important
ambiguities. Don't make the user repeat what they already answered in the text.

**Contradictory stakeholder input.**
Don't force them into agreement. Log each contradiction in field 9
(Conflicts) exactly as it was said.

**Redesign with no data.**
The most common and most expensive gap. Ask for at least rough numbers
(conversion, bounce, top entry pages). If there are none, flag it heavily
in field 10.

**Segment profile.**
fintech / healthcare / other — the actual domain patterns live in the
evisions-wireframe segment profiles, not here. Here it's only tagged, not
elaborated.
