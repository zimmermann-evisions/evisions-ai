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
