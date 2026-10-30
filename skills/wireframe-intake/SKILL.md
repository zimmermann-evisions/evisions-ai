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
[`assets/brief-template.md`](assets/brief-template.md) — the structured brief the
`evisions-wireframe` workflow reads downstream. Intake defines **WHAT and WHY**,
never HOW: no IA, no sections, no components here.

## When to use / not use

Use at the **start** of a website wireframing project, before any layout work.

Do not use for: building the wireframe itself (that's `evisions-wireframe`),
writing a product spec (`prd-creator`), or a production build (`idea-plan-launch`).

## Workflow

### 1. Detect language and mode
The client's own language stays the client's language — if the raw input or the
conversation is in Slovak, Czech, or any other language, keep gathering and
writing the brief in that language. Keep the **field labels exactly as written
in the template** regardless of conversation language — downstream parsing
depends on them, so translate the conversation, not the labels.

Set the mode: **NEW site** or **REDESIGN**. Redesign unlocks the Data section
(analytics + funnel), which is the single highest-value input. If it is a
redesign and no data is offered, treat that as the most important gap to chase.

### 2. Ingest what already exists
If the user pasted a brief, notes, or a transcript, extract every field you can
into the template **first**. Only then ask about what is missing. Never re-ask
for something they already gave you — it erodes trust and wastes their time.

### 3. Interview the gaps — a few at a time
Load [`references/interview-guide.md`](references/interview-guide.md) for the
question bank per section. Ask **grouped, 2–4 questions at a time**, most
decision-relevant first. Never dump the whole questionnaire at once. For choices
(segments, breakpoints, WCAG level, segment profile) prefer tappable/structured
questions over open text.

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
- [`assets/brief-template.md`](assets/brief-template.md) — the exact output
  contract. Always produce this, fully filled.
- [`references/interview-guide.md`](references/interview-guide.md) — question
  bank per section, edge cases, redesign-vs-new handling, sparse-input handling.
