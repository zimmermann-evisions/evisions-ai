# Technical specification — [Client] · [Project]

> Output contract. Don't rename sections — implementation plans and reviewers
> read them by this structure. Mark anything unknown `⚠ OPEN — <what's
> missing, who closes it>`. Never guess and present it as decided.

**Based on:** [brief link/name] · [Figma frame link] · [preview / tokens.css]
**Date:** · **Author:**

---

## 1. What is being built
One paragraph, plain language. Someone who hasn't seen the design should
understand the scope from this alone.

- **Type:** marketing site / landing page / app / other
- **Pages:** list every page. Say explicitly what is NOT in scope.
- **Languages:**
- **Breakpoints:** (default 1440 / 768 / 390)

## 2. Hosting and stack
> Decides almost everything below. See `evisions-constraints.md`.

- **Hosting:** client hosting (Websupport…) / evisions servers
- **Stack:** follows from hosting, not preference
- **Repo:**
- **Domain + DNS access:** who has it, arranged yes/no

## 3. Sections and components
Per page, in order, from the approved design. For each: what it is, whether
it is static or has behaviour, and anything unusual.

| Page | Section | Behaviour | Notes |
|---|---|---|---|

## 4. Interactions
> Everything that reacts to a user. `web-build` requires a human to confirm
> this list before coding.

- Navigation (mobile menu, sticky header, scroll behaviour)
- Expand/collapse (FAQ, accordions, tabs)
- Button and link states (hover, focus, active, disabled)
- Animations — and their `prefers-reduced-motion` behaviour
- Anything JS-driven (carousels, calculators, filters)

## 5. Forms
Per form:
- **Fields** + types + which are required
- **Validation** — rules and error messages, client and server side
- **Where the data goes** — destination, and who owns it
- **After submit** — thank-you page, message, redirect
- **Spam protection**
- **GDPR** — consent wording, retention period, who is the controller
- **Notification** — who gets told about a new submission, how

## 6. Integrations
> Every external service. Each one must be declared in `externalCalls` in the
> manifest — CI greps the code, not just the manifest.

| Service | What for | Auth / keys | Declared in manifest |
|---|---|---|---|

Server-side vs client-side per integration — anything with a secret key is
server-side, no exceptions.

## 7. Data
- **What is collected:**
- **Where it is stored:** (evisions infrastructure — no hosted DB services)
- **Retention:**
- **Who can access it:**
- **Personal data:** what is personal, what the legal basis is

## 8. Measurement
- **GA4 / GTM:** account, who owns it, is access arranged
- **Events:** name, trigger, parameters
- **Consent:** measurement fires only after consent — wired in from the
  start, not added later
- **Cookie bar:** every site gets one

## 9. Content
- **Copy:** who writes it, when, in which language(s)
- **Images:** source, who resizes and compresses
- **Legal pages:** privacy policy, cookie policy, terms — who supplies them
- **Placeholder rule:** what ships if content is late

## 10. Non-functional requirements
- **Accessibility:** target WCAG level (check the segment profile — for B2C
  fintech in the EU, AA is a legal requirement, not a nice-to-have)
- **Performance:** targets, if any
- **SEO:** meta tags, structured data, sitemap, robots
- **Browsers:**
- **Staging:** URL, noindex while staging

## 11. Risks and dependencies
Things that can block or break the build. Access that takes days to arrange
(client Google account, domain, hosting credentials) belongs here — those are
the ones that bite at the end.

| Risk / dependency | Impact | Owner | Status |
|---|---|---|---|

## 12. Open questions
> Everything marked `⚠ OPEN` above, collected. Split them:

**Blocks the start:**

**Can be resolved during the build:**

## 13. Implementation plans
List of the plans this spec was cut into, in build order. Each links to its
own plan file. See `implementation-plans.md`.

| # | Plan | Depends on | Blocked by |
|---|---|---|---|
