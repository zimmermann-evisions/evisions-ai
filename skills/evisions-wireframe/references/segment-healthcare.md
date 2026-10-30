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
above-average need for accessibility. This is an ethical/UX
recommendation, not a legal fact — don't label it as law unless the client
wants it verified by a lawyer.

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
