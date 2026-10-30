# Cutting the spec into implementation plans

## The rule: vertical slices, not horizontal layers

One plan = one piece that can be **built, tested and shown on its own**.

✅ "Hero + calculator" · "Lead form end-to-end" · "Cookie bar + GTM"
❌ "All the HTML" → "All the CSS" → "All the JS"

**Why it matters:** a vertical slice can be finished, verified and — if it
goes wrong — reverted without taking the rest down. A horizontal layer is
only done when everything is done, so nothing is testable until the end,
which is exactly when problems are most expensive.

## Size

A plan should be small enough that a reviewer can hold it in their head, and
big enough to deliver something that works end to end.

Too big if it contains an "and also" that could stand alone. Too small if it
cannot be demonstrated without another plan finished first.

## Order

Don't invent it. `web-build` already defines the order for client sites:

```
cookie bar → GTM + GA4 → lead form → legal pages → meta tags + SEO
```

Measurement is tied to consent from the start, which is why the cookie bar
comes first — not as bureaucracy, but because retrofitting consent means
redoing the instrumentation.

Sections and components come before or alongside these, in the order the page
is read.

## What every plan contains

- **What it delivers** — in one sentence, in terms of what works afterwards
- **Which spec sections it implements** — so a reviewer can trace it back
- **Dependencies** — which plans must be finished first
- **Acceptance** — how to tell it is actually done, in terms someone can
  check. Not "the form is implemented" but "submitting the form with valid
  data stores a row and sends the notification; submitting with an invalid
  email shows the error and stores nothing."
- **Tests** — what will be tested, and what a failure would look like. A test
  that passes on broken code is not a test.
- **Open items** — anything from the spec still unresolved that touches this
  plan

## Plans blocked by open questions

A plan that depends on an unresolved `⚠ OPEN` item **does not start**. Mark
it blocked and name what unblocks it.

The temptation is to start on a reasonable assumption and fix it later. That
is how a guessed form destination becomes three days of rework — the
assumption gets built on before anyone notices it was an assumption.

## Where plans live

Files in the repo, next to the spec, referenced from section 13. Not chat
output — an implementation plan that exists only in a conversation cannot be
reviewed, tracked or handed to someone else.
