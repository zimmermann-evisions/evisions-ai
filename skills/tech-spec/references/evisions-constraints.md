# evisions constraints that shape the spec

These are not preferences. Several are enforced by CI, which greps the code —
a spec that ignores them produces a build that cannot ship.

## Hosting decides the stack

**The single highest-value question in the spec.**

| Hosting | Stack | Rules that apply |
|---|---|---|
| **Client hosting** (Websupport…) | Static HTML + CSS + JS | No Docker, no Caddy, no Next.js |
| **evisions servers** | Next.js + TypeScript + Tailwind + Docker + Caddy | Full evisions app rules |

Most client marketing sites are the first row. Don't reach for the app stack
because it is more capable — a static site that any hosting can serve is the
point.

A site with a form and one integration still fits the first row, as long as
the server-side part (the form handler, the lead app) is separated out. That
part follows evisions rules even when the site itself does not.

## Data stays on evisions infrastructure

Refused by CI: **Supabase, PlanetScale, Firebase, MongoDB Atlas, Upstash,
Neon, Xata**. CI greps the code, so an SDK import fails the build regardless
of what the manifest says.

Allowed: Postgres, SQLite, or a JSON file — on evisions infrastructure.

**Vercel is not used.** An app that only runs there cannot be deployed.

Sending email through an external transactional service (Resend, Postmark
and similar) is a different category — data is not stored there, it passes
through. Declare it in `externalCalls`. Which service evisions prefers is
worth confirming with the AI team; it is not written down.

## The manifest is a promise, not a config field

`evisions-app.json` declares `auth` and `externalCalls`, and someone will
rely on both.

- **Every external call gets declared.** CI greps the code, not just the
  manifest.
- **`auth: "none"` is allowed** and produces a warning. Quietly declaring an
  auth mode the app does not actually enforce is the failure mode — a config
  field that lies.

## Consent before measurement

Every site gets a cookie bar. Measurement fires only after consent, wired in
from the start.

Retrofitting consent onto an instrumented site is more work than doing it
first, and leaves a compliance gap in the meantime.

## Accessibility is sometimes law

Check the client's segment before treating a WCAG level as a preference.

**B2C fintech in the EU:** the European Accessibility Act (Directive EU
2019/882, in force since 28 June 2025) makes WCAG AA a legal requirement for
consumer-facing banking, insurance and e-commerce flows. Not a target — a
requirement.

**B2B** is outside the EAA's scope. **Healthcare** is more nuanced; a
self-pay payment or booking flow can fall under its e-commerce category even
where general content does not.

Where it matters, say so in the spec rather than leaving "WCAG AA" looking
like an aspiration someone can trade away under deadline.

## Secrets

`.env` only. Never in code, never in the compose file, never in client-side
JavaScript. Anything with a key is server-side.

If a key has ever been committed, that is a stop-and-fix, not a note for
later.

## Repo and deploy

For apps on evisions servers: the AI team creates the repo from the
`ai-deploy-probe` template. Branch → PR → CI (5 checks) → review → **the
person clicks Merge**. Merge is the deploy.

For client-hosted static sites, upload is currently manual — confirm with the
AI team where the repo lives.
