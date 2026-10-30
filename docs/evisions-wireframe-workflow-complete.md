# evisions Wireframe Skill System — Complete Workflow Documentation

Prepared for external research and verification. This document describes an
AI-assisted (Claude) wireframing workflow built for evisions, a digital
agency in the Czech Republic/Slovakia region. It combines a two-skill
architecture, a shared component design system ("Wireframe Kit" in Figma),
and a set of process rules developed and iterated through real client work.

---

## 1. Purpose & context

evisions does UX/CRO work for clients across fintech, healthcare, B2B SaaS,
insurance, and e-commerce. The agency maintains a shared Figma "Wireframe
Kit" component library used to assemble low-fidelity wireframes quickly and
consistently across projects.

The goal of this system: let an AI assistant (Claude, via Figma's plugin
API through an MCP connector) take a client's raw input and turn it into an
approved information architecture and a built wireframe in Figma — reusing
the existing component kit wherever possible, escalating to a human at the
right decision points, and feeding new patterns back into the kit over time.

Two Claude "Skills" implement this:

1. **`wireframe-intake`** — turns raw/messy client input into a structured
   brief.
2. **`evisions-wireframe`** — turns a structured brief into an approved IA
   and a built Figma wireframe.

They are deliberately two separate skills, not one. Rationale: they are
different competencies (requirements gathering vs. design/composition
judgment), each is independently useful (e.g., `wireframe-intake` alone
for producing a brief for a human designer), and keeping them separate
keeps each skill's own instructions shorter and easier to maintain.

---

## 2. High-level workflow

```
Raw client input (meeting notes, call transcript, messy brief, or nothing yet)
        │
        ▼
┌─────────────────────┐
│  wireframe-intake    │  Extracts what's known → interviews gaps in small
│                       │  batches → tags conflicts without resolving them →
│                       │  outputs a structured brief (fixed template)
└─────────────────────┘
        │
        ▼
   Structured brief (brief-template.md, filled)
        │
        ▼
┌─────────────────────────────────────────────────────────────────┐
│  evisions-wireframe                                              │
│                                                                    │
│  1. Load segment profile (fintech / healthcare / other)           │
│  2. Write IA blueprint (text only)               ──► 🚦 GATE 1    │
│     (human approves before any Figma work starts)                 │
│  3. Build v0.1 in Figma from the Wireframe Kit                    │
│     - check kit live via search_design_system, not from memory    │
│     - inspect internal structure, not just names/descriptions     │
│     - 3 paths: existing block / composed from primitives / NEW    │
│  4. QA against acceptance checklist                ──► 🚦 GATE 2  │
│     (human picks final variants where taste decides)              │
│  5. Responsive + copy pass (separate, named passes)                │
│  6. Handle any missing component per conventions                   │
│  7. Promote new component into shared kit         ──► 🚦 GATE 3   │
│     (kit owner reviews and clicks Publish — always manual in Figma)│
│  8. Handover + feed new patterns back into segment profiles        │
└─────────────────────────────────────────────────────────────────┘
```

The three gates are the core governance mechanism: they keep a human
in control at the three points where judgment (not mechanics) matters —
approving the plan before drawing, approving the drawn result, and
approving anything that becomes a permanent, shared library asset.

---

## 3. Skill 1 — `wireframe-intake`

### 3.1 `SKILL.md`

```markdown
---
name: wireframe-intake
description: >-
  Turn a client's raw input (text brief, meeting notes, a call transcript, or
  nothing yet) into a structured evisions wireframe brief that the wireframing
  workflow can consume. Use this at the very START of any website wireframing
  project — when a client wants a new web or a redesign and we must define
  goals, segments, content and constraints before drawing anything. Trigger on:
  "prepare a wireframe brief", "wireframe intake", "intake", "new site for a
  client", "client wants a website", "brief for wireframes", "redesign brief",
  "client wants a new site", "intake for wireframes", or /wireframe-intake.
  Also use whenever someone pastes a messy or partial client brief and wants
  it made usable for wireframing. Produces a fixed brief template; when
  information is missing it interviews the user to fill the gaps instead of
  guessing.
---

# Wireframe Intake

A wireframe is only as good as the brief behind it. The strongest wireframes
come from a brief that names the business goal, the KPI, the audience segments
and their fears, — for redesigns — the funnel data, and, critically, an
explicit **mandate to cut content**. Without those, the output defaults to
"everything is important," which is the exact failure the whole wireframing
process exists to prevent.

This skill converts any client input into a completed
`assets/brief-template.md` — the structured brief the `evisions-wireframe`
workflow reads downstream. Intake defines **WHAT and WHY**, never HOW: no IA,
no sections, no components here.

## When to use / not use

Use at the **start** of a website wireframing project, before any layout work.

Do not use for: building the wireframe itself (that's `evisions-wireframe`),
writing a product spec (`prd-creator`), or a production build (`idea-plan-launch`).

## Workflow

### 1. Detect language and mode
The client's own language stays the client's language — if the raw input or
the conversation is in Slovak, Czech, or any other language, keep gathering
and writing the brief in that language. Keep the **field labels exactly as
written in the template** regardless of conversation language — downstream
parsing depends on them, so translate the conversation, not the labels.

Set the mode: **NEW site** or **REDESIGN**. Redesign unlocks the Data section
(analytics + funnel), which is the single highest-value input. If it is a
redesign and no data is offered, treat that as the most important gap to chase.

### 2. Ingest what already exists
If the user pasted a brief, notes, or a transcript, extract every field you can
into the template **first**. Only then ask about what is missing. Never re-ask
for something they already gave you — it erodes trust and wastes their time.

### 3. Interview the gaps — a few at a time
Load `references/interview-guide.md` for the question bank per section. Ask
**grouped, 2–4 questions at a time**, most decision-relevant first. Never dump
the whole questionnaire at once. For choices (segments, breakpoints, WCAG
level, segment profile) prefer tappable/structured questions over open text.

Stop pushing on a field after one reasonable follow-up. A partial brief with
clearly flagged gaps beats an interrogation — the missing pieces can be filled
in review.

### 4. Name the conflicts (don't resolve them)
Stakeholder inputs routinely contradict each other (sales wants segmentation,
CEO wants minimal, CMO wants more content). Capture each contradiction in the
**Conflicts** field, plainly. Resolving them is the wireframe step's job — not
intake's. Surfacing them here prevents them from being silently averaged away.

### 5. Set the segment profile
Tag the project **fintech / healthcare / other**. This routes the downstream
wireframe skill to the right domain profile. If it is "other," say so — there is
no profile yet, and that's fine.

### 6. Output the brief
Fill `assets/brief-template.md` completely. Mark every unknown as
`⚠ MISSING` with a one-line note on *why it matters*, rather than leaving it
blank or inventing an answer. Finish with a short "biggest gaps / risks" line
so the next step knows where the brief is thin.

## Output rules
- Keep field labels verbatim from the template (downstream reads them).
- Concrete over abstract everywhere — sums, numbers, real examples — mirroring
  the evisions content principle ("€50/month," not "financial wellbeing").
- Do not design. No IA, no section order, no components. If you catch yourself
  proposing layout, stop — that belongs to `evisions-wireframe`.

## Files
- `assets/brief-template.md` — the exact output contract. Always produce
  this, fully filled.
- `references/interview-guide.md` — question bank per section, edge cases,
  redesign-vs-new handling, sparse-input handling.
```

### 3.2 `assets/brief-template.md`

```markdown
# Wireframe Brief — [Client] · [Project]

> Output contract for `evisions-wireframe`. Do NOT change field labels —
> downstream reads them. Mark unknown fields `⚠ MISSING — <why it's missing /
> why it matters>`. Never invent an answer.

**Output language:** [client's language — SK / CZ / EN / other]
**Mode:** NEW / REDESIGN
**Segment profile:** fintech / healthcare / other
**Date:**

---

## 1. Project & context
- **Client:**
- **Segment / industry:**
- **Market (countries + languages):**
- **Site type:** (marketing / product / e-commerce / …)
- **Phase:**

## 2. Goals & KPI
- **Primary business goal:**
- **Primary KPI:** (for REDESIGN, give baseline → target, e.g. 2.1% → 3.0%)
- **Secondary goals:**

## 3. Segments & their questions
> For each segment: who they are, their key question, their main fear.
- **Segment A —**
- **Segment B —**
- **Segment C —**

## 4. Data *(REDESIGN only)*
- **Traffic + sources:**
- **Funnel / conversions:**
- **What converts best:**
- **Where users drop off:**
- *(NEW site → write: "N/A — new site")*

## 5. Content
- **Must-have content / sections:**
- **Mandate to cut** (what MAY be removed / merged):  ← key field
- **What NOT to do / avoid:**

## 6. Brand, tone & design direction
- **Tone / voice:**
- **Design direction + references:**
- **What to avoid visually:**

## 7. Hard constraints
- **Legal / compliance:**
- **Accessibility (WCAG level):**
- **Language versions:**
- **Technical / other:**

## 8. Scope
- **Which pages:**
- **Breakpoints:** (default 1440 / 768 / 390)

## 9. Stakeholder conflicts
> Named, NOT resolved — resolving them is the wireframe step's job.
-

## 10. Open questions / biggest gaps
-
```

### 3.3 `references/interview-guide.md`

```markdown
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
```
## 4. Skill 2 — `evisions-wireframe`

### 4.1 `SKILL.md`

```markdown
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
```

### 4.2 `assets/ia-blueprint-template.md`

```markdown
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
```

### 4.3 `references/segment-fintech.md`

```markdown
# Segment Profile — Fintech

Load at the IA step when the brief has `Segment profile: fintech`. Adds to,
never replaces, the brief — concrete data, segments and product subtype
always come from the brief.

Fintech is not one product. Investing, payments, lending, insurance, banking,
B2B fintech infrastructure — each has different segments and a different core
tool. The principles below hold across subtypes. Every claim is tagged by
strength of evidence — don't mix a verified fact with an industry opinion.

## Sources and their weight
- **[RESEARCH]** Baymard Institute — aggregated checkout/e-commerce research
  (dozens of studies). Closest verified analog for fintech, not
  fintech-specific. baymard.com/learn/reduce-cart-abandonment
- **[REGULATION]** European Accessibility Act (Directive EU 2019/882) —
  legally binding since 28 June 2025. Sources: twobirds.com,
  en.wikipedia.org/wiki/European_Accessibility_Act, deque.com (B2C-only
  scope detail)
- **[INDUSTRY CONSENSUS]** Multiple design agencies independently (Eleken,
  WSA, YUJ, Fuselab, Phenomenon, UITOP, etc.) — consistent themes, but
  marketing content, not research. A directional signal, not evidence.

## Core principle
**Confidence before conversion.** With money, default distrust is higher
than on an ordinary site — the site must build trust before asking for
commitment. **[INDUSTRY CONSENSUS]** A phrase repeated across agencies:
*"friction-right, not frictionless"* — deliberate friction at risky steps
(a large transfer, changing settings, confirming a payment) builds trust
rather than eroding it.

## Cost transparency — the most strongly supported principle
**[RESEARCH]** Hidden/extra costs are the most commonly cited reason for
abandoning a purchase/checkout (39–48% per Baymard's aggregation), and ~25%
of users specifically don't trust a site with their financial data. This is
e-commerce data, not fintech-specific, but the closest verified analog we
have for a flow involving financial commitment.

Practical consequence: fees, interest, APR, commissions, premiums — always
explicit and early in the flow, never buried in terms at the end.

## Accessibility — for B2C fintech in the EU, this is law, not a recommendation
**[REGULATION]** The European Accessibility Act (Directive EU 2019/882),
in force since 28 June 2025, requires accessible websites/apps explicitly
for online services too (not just physical products) — the services
directive directly requires "accessible websites and mobile apps." Banking
falls directly within scope; the "e-commerce services" category is broad on
top of that — it covers any online sale of a product/service to a consumer,
so in practice almost any fintech site where a consumer opens an account/
signs a contract/buys a product online falls under the EAA.

**Important boundary: B2C only.** The EAA does not apply to B2B or
employee-facing systems. If the client is B2B fintech (e.g. payment
infrastructure sold to businesses, not consumers), the EAA does not apply —
accessibility remains best practice there, not a legal requirement. Check in
the brief whether the product is B2C or B2B before drawing a compliance
conclusion from this.

For B2C fintech clients (SK/CZ, EU member states): propose WCAG 2.1 AA as an
absolute minimum regardless of whether the brief explicitly asks for it —
it's a compliance requirement, not an optional recommended level.

## The "try before commitment" pattern
A logical extension of the principle above (concrete over abstract) — a tool
that lets the user see a concrete outcome/value before they have to sign up.
The shape differs by subtype, check the brief:
- **Investing/savings:** calculator (amount → orientation result)
- **Insurance:** configurator/wizard (pick modules → see the price)
- **Lending:** eligibility checker / orientation repayment calculator
- **Payments/B2B fintech:** fee estimator, pricing simulator by volume

If the brief mentions "how much will this cost / how much will I get / what
am I entitled to" and the kit has no fitting tool, that's a legitimate
reason for a NEW component (see `component-conventions.md`).

## Trust signals — content differs by subtype, the principle doesn't
**[INDUSTRY CONSENSUS]** Must be explicit, not in the footer:
- Investing/banking: regulator (e.g. NBS/ČNB), deposit protection
- Insurance: insurance supervisory authority, policy terms, claims speed
- Payments: PCI-DSS, transaction security
- B2B fintech: compliance certifications (SOC2, etc.), uptime/SLA

## Hero decision (decide deliberately, don't default)
A fintech hero typically has competing claims: trust × a clear value
proposition × "how it works" × what engagement data says. Default **if data
is missing**: engagement-first (VP + a "try before commitment" tool, not
straight to "Sign up/Apply"). If the brief's data says otherwise, follow the
data.

## Segments — always from the brief, never universalize
Segments differ fundamentally across fintech subtypes — B2C investing has
different segments than B2B payment infrastructure than insurance after a
merger. Don't reuse segments from one project as a template for another.

## Components that recur
- An interactive "preview" tool matching the subtype
  (calculator/configurator/checker)
- Comparison vs. status quo (a different broker, a previous policy, a
  traditional bank)
- Trust/metrics strip
- Sticky CTA bar — especially for a longer flow (configurator, multiple
  steps)
- Pricing/fees section with no hidden items
- FAQ right before the final CTA

## Copy rules
- Concrete numbers/examples instead of abstractions ("€50/month," not
  "build your financial wellbeing")
- Never guarantee an outcome/return/speed that can't always be kept
- CTAs confident, not aggressive — trust is built through clarity, not
  pressure
```

### 4.4 `references/segment-healthcare.md`

```markdown
# Segment Profile — Healthcare

Load at the IA step when the brief has `Segment profile: healthcare`. Adds
to, never replaces, the brief. Every claim is tagged by strength of evidence.

## Sources and their weight
- **[RESEARCH]** Berkman ND, Sheridan SL, Donahue KE, et al. "Low health
  literacy and health outcomes: an updated systematic review." Ann Intern
  Med. 2011;155(2):97-107 — peer-reviewed systematic review.
- **[RESEARCH]** CDC/NIH plain-language programs (cdc.gov/health-literacy),
  a randomized trial (Univ. of Sydney, medrxiv preprint) on improved
  comprehension via a plain-language tool.
- **[REGULATION]** European Accessibility Act (Directive EU 2019/882) —
  scope per the European Union of the Deaf (a direct stakeholder in the
  transposition) and Bird & Bird (legal analysis). Nuanced, see below — not
  blanket coverage.
- **[INDUSTRY CONSENSUS]** Healthcare UX agencies independently (Eastern
  Standard, Geonetric, Highland) — consistent themes, marketing content, not
  research.

## Core principle
**Findability + clarity under stress.** A healthcare site's user is more
often anxious/under pressure than an average site visitor — **[INDUSTRY
CONSENSUS]** a repeatedly cited reason for redesigns: patients can't find
information, or bookings stay low despite traffic.

## Plain language — the most strongly supported principle
**[RESEARCH]** Low health literacy is demonstrably correlated with worse
health outcomes (Berkman et al., 2011, peer-reviewed systematic review).
This is not just a style recommendation — it's grounded in health research,
not only UX research. An RCT (Sydney, medrxiv) confirms that moving from
medical jargon to plain language measurably improves comprehension.

Practical consequence: no medical jargon in the main flow without
explanation, short sentences, active voice, structured text (not walls of
text).

## Accessibility — nuanced, not a blanket law
**[REGULATION]** The common claim "the EAA requires WCAG AA for healthcare
websites" is **inaccurate**. The European Union of the Deaf (Art. 2 of the
EAA's scope) explicitly states that healthcare services are *not* within the
EAA's baseline scope — unlike banking/insurance (see `segment-fintech.md`).

**An exception directly relevant to self-pay:** if a site lets a consumer
complete a paid transaction online (booking + payment for a self-pay
program), that specific part of the flow may fall under the EAA's
"e-commerce services" category (by analogy from Bird & Bird's analysis for
medical devices). Check this for projects with online payment/booking of
paid programs — not for general informational content.

**Regardless:** propose WCAG 2.1 AA as the default even where it isn't
strictly legally required — healthcare users (older patients, temporarily
disabled users, caregivers searching on someone else's behalf) have an
above-average need for accessibility. This is an ethical/UX recommendation,
not a legal fact — don't label it as law unless the client wants it verified
by a lawyer.

National legislation (SK/CZ) may go beyond the EU minimum — verify locally,
don't treat this summary as final for a specific project.

## Wayfinding — the most frequently recurring problem
**[INDUSTRY CONSENSUS]** Independently consistent across multiple healthcare
UX agencies: the top tasks are "find a doctor/department," "book an
appointment," "pay a bill" — they belong front and center, not buried in a
menu. Mega menus on healthcare sites need explicit governance (what's
allowed at the top level), otherwise they bloat. The mobile version must not
be just a shrunk desktop mega menu — it needs its own, simplified pattern.

## Segments — always from the brief, never universalize
A regular patient (findability) / a self-pay client (needs a clear program
structure with price) / a caregiver searching on someone else's behalf / an
international patient (language barrier) are common types, but always
verify the exact composition and priorities in the brief — don't use this
as a ready-made template.

## Components that recur
- A finder/wayfinding tool (find a doctor/department) — usually the
  strongest pain point per industry consensus
- A compact listing of many categories (departments, programs) without an
  infographic or a long list
- A self-pay/programs structure with price + package content — if it
  includes online payment, check the EAA e-commerce scope above
- FAQ right before the final CTA — covering remaining concerns in
  jargon-free language

## Copy rules
- Plain language instead of medical jargon — backed by research, not just
  style (see above)
- Never promise speed/availability the site can't guarantee
- Premium/trustworthy tone ≠ aggressive sales language

## What this profile still doesn't know
No internal source project yet with sources verified this way (unlike the
fintech profile). Add to it with each additional healthcare client —
especially verify the EAA e-commerce scope nuance against actual legal
advice, not just this summary.
```
### 4.5 `references/component-conventions.md`

```markdown
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

*(Noted exception in this project's history: one early component —
`Carousel / Tag Navigation /` — was built with real `SCROLL_TO` reactions
before this rule was finalized. It was deliberately left as-is rather than
reworked, and treated as a one-off exception, not a precedent.)*

## Generic placeholder content — always, even for client-sourced work
Kit text is English Lorem-ipsum style regardless of the client's target
market/language — "Section heading goes here," "Category one"–"five," "Card
heading goes here," "Learn more →." A new component must not carry a
specific client's content — even if it originated on their project, it goes
into the kit with generic content. Real, localized copy always comes one
step later, outside the component itself.

## Technical gotchas (expensive to rediscover)
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
- **Component instances don't allow `remove()` on their internal nodes.**
  Attempting to delete a child node inside an instance (e.g. an unwanted
  sub-block that came with the component) throws "Removing this node is not
  allowed." Use `node.visible = false` instead — hiding is permitted,
  structural deletion isn't.
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
```

### 4.6 `references/acceptance-checklist.md`

```markdown
# Acceptance Checklist — when is a wireframe "done"

Run through this at GATE 2 (after v0.1 QA) and again before handover. If any
item fails, fix it before acceptance. The core (Q1–Q3) is distilled from a
self-test one real client brief applied to its own output — it works as a
universal test, not a client-specific one.

## Core — three questions (must pass)
- [ ] **3-screen test:** if only the first 3 sections remained, would the
      user understand what the product does, who it's for, and why to trust it?
- [ ] **60-second test:** after ~a minute, does the user have a clear reason
      to click the primary CTA?
- [ ] **System test:** did I use the kit as a system, or just stack random
      blocks together? Every section has a reason to be where it is.

## IA & conversion
- [ ] Section order matches the conversion flow from the blueprint (entry →
      understanding → confidence → relevance → engagement → conversion).
- [ ] One clear priority per screen (no "everything is important").
- [ ] Clear CTA hierarchy: primary / secondary / inline / sticky — and they
      don't compete with each other.
- [ ] Confidence signals come BEFORE asking for commitment, not after.

## Kit & components
- [ ] Real kit blocks used everywhere they fit (no unnecessary NEW).
- [ ] Before every NEW, also check path 2 from `component-conventions.md` —
      can it be composed from existing primitives (e.g. Text input +
      Filters), or does it genuinely need to be a new component?
- [ ] Every NEW/composed/placeholder is clearly labeled and justified in the
      blueprint.
- [ ] No "block just because it exists."
- [ ] No extra functional prototype interactions (the wireframe stays
      structural, not hi-fi) — outside of explicitly agreed exceptions.

## Responsive
- [ ] Breakpoints from the brief are covered (default 1440 / 768 / 390).
- [ ] Content order on mobile makes sense (not just a narrowed desktop).
- [ ] Sticky elements and nav are handled on mobile.

## Accessibility — check by subtype, don't assume
- [ ] Logical heading hierarchy (one H1, meaningful H2s).
- [ ] Target WCAG level from the brief — default AA. **Fintech B2C:** check
      whether the site touches banking/insurance/an e-commerce transaction —
      if so, the EAA (Directive EU 2019/882) makes AA legally binding, not
      optional. **Healthcare:** general content usually doesn't have the
      same hard legal basis — but if the site allows online payment/booking
      (self-pay), that part may fall under EAA e-commerce scope. See
      `segment-fintech.md` / `segment-healthcare.md`.
- [ ] Color is not the only carrier of meaning.
- [ ] Touch targets and focus order are considered.
- [ ] CTA text is understandable out of context.

## Content
- [ ] Copy is concrete, not abstract ("€50/month," not "build your
      financial wellbeing"). This applies to the client wireframe —
      realistic copy, not lorem ipsum. A different rule applies to NEW
      components headed for the kit: those MUST have generic placeholder
      text, not client content (see `component-conventions.md`).
- [ ] H1 and key CTAs are realistic — at least by the copy pass.

## Handover
- [ ] NEW/composed components are written up for the kit owner (name +
      description + reason it was created + estimated repeatability across
      segments).
- [ ] New gaps/patterns are logged into the relevant `segment-*.md` profile
      for the feedback loop — the kit itself is always a live source of
      truth via `search_design_system`, no separate manifest is maintained.
```
## 5. Key design decisions & rationale

These were arrived at through iteration, not decided upfront — several
reversed an earlier choice once it was tested against real building.

1. **Two separate skills, not one.** Requirements-gathering and
   design/composition judgment are different competencies; each skill is
   independently useful (e.g. `wireframe-intake` alone can produce a brief
   for a human designer); keeping them separate keeps each skill's
   instructions shorter.

2. **Progressive disclosure architecture.** `SKILL.md` holds only the
   workflow; concrete rules live in `references/` and `assets/`, loaded
   selectively per step. Rationale: a segment profile for fintech shouldn't
   load into context on a healthcare project, and the main skill file stays
   short and stable as content grows.

3. **No static kit manifest.** `search_design_system` (an MCP tool over the
   Figma file) is treated as the single, always-current source of truth.
   A cached manifest document was considered and explicitly rejected —
   it would duplicate what the live tool already provides and would go
   stale the moment the kit changes.

4. **IA blueprint describes a *need*, not a binding component name.**
   Section 3 of the IA blueprint (the human-approved plan) names a pattern
   in words ("structured listing with price and CTA") rather than
   committing to a specific kit component. The exact component is resolved
   only at build time. This avoids forcing a re-approval cycle when the
   build step finds a better-fitting component than what was guessed at
   the planning stage.

5. **Three paths when the kit doesn't have an obvious match:** (a) an
   existing component fits, (b) it can be composed from smaller existing
   primitives, (c) it's genuinely new. Path (b) was discovered empirically,
   not designed in advance — see the Nemocnica Bory case below.

6. **Speculative vs. recurring pattern distinction for new components.**
   A one-off need gets a cheap dashed placeholder; a pattern expected to
   recur across clients gets built fully library-ready immediately. This
   avoids both under-investing in patterns worth keeping and over-investing
   in one-off content.

7. **No real Figma prototype interactions in wireframe components** — even
   for inherently click-driven patterns (tab navigation, scroll-to). A
   wireframe communicates structure and intent, not working behavior;
   non-trivial interaction gets named in the component's description
   instead. This reversed an earlier choice: the first new library
   component built (a tag-navigation carousel) was given real
   `SCROLL_TO` reactions before this rule existed. It was kept as a
   deliberate one-off exception rather than reworked once the rule was
   set.

8. **Generic, English, Lorem-ipsum-style content for anything headed into
   the shared kit — even when it originated on a specific client's
   project.** A component first built to solve one client's need (e.g. a
   "premium programs" carousel) gets its content genericized before being
   treated as a kit candidate, so it reads as reusable rather than
   client-specific.

9. **Client-facing output stays in the client's own language; the skill's
   own instructions are English.** The skill files themselves (this whole
   document) were deliberately translated to English at the project
   owner's request, but the logic that tells the assistant to keep
   client briefs/wireframes in the client's own language (Czech/Slovak in
   practice) was preserved rather than removed.

10. **Governance model: local candidate → kit owner reviews → kit owner
    publishes.** New components always sit locally with a governance
    sentence in their description until a human who owns the shared
    library reviews and clicks Publish. This isn't just a policy choice —
    Figma's publish action is a manual UI-only step with no API trigger,
    so it's also a hard technical constraint, not only a process one.

11. **Evidence-tiered sourcing in the domain (segment) profiles.** Claims in
    `segment-fintech.md` and `segment-healthcare.md` are explicitly tagged
    `[RESEARCH]` (peer-reviewed or aggregated primary research),
    `[REGULATION]` (a named, dated legal instrument), or `[INDUSTRY
    CONSENSUS]` (repeated but unverified claims from design-agency
    marketing content). This was introduced after a direct challenge to
    justify where the domain principles actually came from, and after that
    challenge, unsupported client-specific anecdotes were deliberately
    stripped back out of the fintech profile so it wouldn't read as more
    authoritative than it was.

12. **A regulatory correction is baked into the healthcare profile.** An
    initial assumption (that EU accessibility law covers healthcare sites
    the same way it covers fintech) was checked and found inaccurate:
    the European Accessibility Act's baseline scope excludes healthcare
    services; only a payment/booking sub-flow may trigger its e-commerce
    category. The healthcare profile documents this as a correction to a
    common claim, not as a settled fact to take at face value.

13. **A tangential regulation (DORA, EU 2022/2554) was deliberately excluded**
    from the fintech profile once it was judged not relevant to the
    agency's actual scope of work (it governs a financial institution's own
    internal ICT risk management, not the marketing website being designed).

14. **Distribution model, for now: personal "share with a colleague," not
    org-wide provisioning.** Claude's skill-sharing mechanism auto-syncs a
    shared skill's future edits to the recipient (the recipient's copy is
    view-only). This was chosen deliberately as the lighter-weight option
    for enabling one immediate colleague, with org-wide admin provisioning
    (available on Team/Enterprise plans) identified as the right path only
    once/if the tool is rolled out more broadly (e.g. to PPC/SEO,
    mentioned as a stated future goal but not yet executed).

## 6. Technical findings (Figma plugin API, discovered through building)

These were not documented anywhere in advance — each was hit as a real bug
during a build and then written into `component-conventions.md` so it
wouldn't be rediscovered at cost the next time.

- **`resize(w, h)` on an auto-layout frame silently resets BOTH
  `primaryAxisSizingMode` and `counterAxisSizingMode` to `FIXED`** —
  regardless of which dimension actually changed, and even if one axis had
  previously been set to `AUTO` (hug contents). The practical rule adopted:
  set the intended `AUTO` sizing mode *after* the last `resize()` call on a
  node, never before.
- **Which axis governs width vs. height depends on `layoutMode`.** For a
  `HORIZONTAL` auto-layout frame, `primaryAxisSizingMode` governs width and
  `counterAxisSizingMode` governs height; for `VERTICAL` it's reversed.
  Mixing these up produces a clipped/mis-sized result that looks identical
  to the `resize()` gotcha above but has a different cause and fix.
- **A component instance will not allow `remove()` on one of its internal
  nodes** (e.g. deleting an unwanted sub-block that ships inside a reused
  kit component) — Figma throws "Removing this node is not allowed."
  `node.visible = false` works where `remove()` doesn't.
- **A single tool-call script is one atomic transaction.** If any part of
  a multi-step script throws partway through, Figma rolls back the entire
  call — including steps that had already completed successfully earlier
  in that same call. The adopted rule: one call per logical
  section/component during a build, so a failure in one section can't
  silently undo a different, already-finished section.

## 7. Real-world validation — projects this was tested against

- **Finora** (a written "redesign challenge" brief, used as the first test
  case). Established the IA blueprint format and the general build pattern
  (dashed placeholders for gaps, numbered label strips annotating each
  section on the Figma canvas).

- **Nemocnica Bory** (Penta Hospitals, an existing agency client). Real,
  intentionally messy kick-off notes were run manually through the intake
  and IA-blueprint steps, then built in Figma. This project is where:
  - the "composed from primitives" path was discovered — a "find a doctor"
    search+filter block turned out not to need a new component at all; it
    was assembled from an existing text input, an existing filter-chip
    row, and an icon;
  - the compact "wrap-grid of chips" pattern for listing many categories
    (hospital departments) was arrived at only after two rejected attempts
    (a card grid that read as "too much like an infographic," then a plain
    row list that still wasn't compact enough);
  - the first new, library-ready kit component was built (a tag-navigation
    carousel, originally scoped to "premium programs" and later
    generalized on request into a reusable pattern);
  - this is also where real click interactions were mistakenly built into
    a kit component, before the "no real interactions" rule existed —
    later kept as a one-off exception rather than reworked.

- **RoklenFX** (a Czech FX/currency-exchange company, an existing agency
  client). Real client meeting notes (both a cleaned summary and a
  deliberately "dirty" raw version) were run through intake, an IA
  blueprint was written and explicitly approved (GATE 1), and a v0.1 was
  built, then iterated twice:
  - **v2:** the individual-vs-business audience choice was moved directly
    into the Hero as two CTAs, and the separate segment-selector section
    it replaced was removed as redundant; a full data table of live
    exchange rates was replaced with a compact widget (repurposing an
    existing "stat card" component instead of the heavier table
    component);
  - **v3:** a static (non-functional) currency-exchange calculator was
    added directly into the Hero at the client's explicit request — after
    flagging that this contradicted an earlier, explicit brief decision
    (the client had said calculators belong only in their separate app,
    not the marketing website) and getting an explicit "proceed anyway"
    from the person driving the project;
  - the "customer care" section was reworked from a plain text block into
    a "meet the team" pattern with photo placeholders, names, roles and
    short blurbs (reusing an existing "Team" kit component), with one name
    deliberately chosen to echo a specific detail from the client's own
    notes;
  - a second new, library-ready kit component was built here: a
    split-layout "video explainer" block (heading + intro text on one
    side, a video placeholder + CTA on the other), built correctly
    following the by-then-finalized "no real interactions" rule.

## 8. Open items / unresolved questions

- **Long-term storage location is undecided.** The two skills currently
  exist only as downloaded `.skill` packages and personally-shared Claude
  skills. Where they should permanently live (an internal Git repository,
  tied to the agency's existing build/repo conventions for internal
  tooling) was raised more than once but never resolved.

- **Kit hygiene rollout had a propagation lag.** A prior pass to rename
  kit components to a clearer, semantic naming scheme was reported as
  complete, but the renamed labels did not actually appear in a working
  Figma file until partway through later work — consistent with a Figma
  library "update" needing to be manually pulled into files that consume
  the library after the source library republishes. Worth confirming this
  is understood as a required step for any future hygiene/renaming pass,
  not assumed to propagate automatically.

- **One unexplained data-loss incident.** During iteration on the RoklenFX
  file, a previously built and screenshot-verified section (a live
  exchange-rate widget and its label) was confirmed present, then found
  completely absent from the file a short time later, with no intentional
  deletion having occurred in between. It was rebuilt from the known
  recipe, but the root cause was never conclusively identified. Worth
  monitoring for recurrence.

- **The two segment profiles are asymmetric in maturity.** The fintech
  profile has two real, evidence-checked reference points (an investing
  platform and an insurance product); the healthcare profile currently has
  no internal project it was checked against, only external sources. This
  imbalance is noted inside the healthcare profile itself.

- **Scaling distribution beyond one colleague** (e.g. to PPC/SEO teams, an
  explicitly stated future goal) will require moving from personal
  skill-sharing to organization-wide admin-provisioned skills — not yet
  done.

## 9. Suggested angles for external research / verification

This document is being handed to another AI system for an independent
research pass. Angles worth specifically checking:

1. **Regulatory accuracy and currency.** Re-verify the European
   Accessibility Act scope claims (in-force date, B2C-only boundary,
   healthcare exclusion, e-commerce-services breadth) against current,
   primary EU legal sources — these were checked once via web search but
   should be independently reconfirmed, especially given how much of the
   accessibility guidance in both segment profiles depends on getting this
   boundary right.
2. **Whether the cited health-literacy research (Berkman et al. 2011) is
   still the best/most current systematic review available**, or whether
   a more recent one should replace or supplement it.
3. **Whether the underlying methodology — tagging claims by evidence
   strength (`[RESEARCH]` / `[REGULATION]` / `[INDUSTRY CONSENSUS]`) — is
   itself a sound and sufficient practice**, or whether a more rigorous
   framework exists for this kind of applied-UX documentation.
4. **Whether the "three human gates" governance model** (plan approval,
   build acceptance, library-promotion approval) is consistent with
   established practice for keeping humans appropriately in the loop in
   AI-assisted design/production workflows, or whether there are known
   failure modes this doesn't account for.
5. **Whether treating a live query tool as the sole source of truth (no
   cached manifest) is a sound architectural choice at scale**, versus the
   trade-offs of a periodically-refreshed cache.
6. **Whether the documented Figma Plugin API behaviors** (the `resize()` /
   sizing-mode interaction, the axis behavior per `layoutMode`, and the
   instance-node removal restriction) match Figma's own published plugin
   API documentation — useful to cross-check independently since they were
   derived empirically from build failures rather than from reading the
   API reference end-to-end.

---

*End of document.*
