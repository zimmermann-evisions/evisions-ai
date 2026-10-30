# Filling the spec — section by section

For each section: where the answer usually already is, and what genuinely has
to be asked. **Read first, ask second.** Re-asking what the brief answers
signals the spec was written without reading the project.

## 1. What is being built
**Usually known:** from the brief and the approved design.
**Ask:** only if the page list is ambiguous — e.g. the design has one page but
the brief implies more.

The out-of-scope line matters more than it looks. "Blog is not in scope" in
writing prevents a conversation three weeks later.

## 2. Hosting and stack
**Ask — this is the highest-value question in the spec.** It decides the
stack, the deploy path, and whether evisions app rules apply at all. See
`evisions-constraints.md`.

Also ask early about **domain and DNS access**, even though it is used last.
Getting it from a client often takes days.

## 3. Sections and components
**Known:** from the approved design — read it through the Figma connector,
page by page, section by section.
**Ask:** anything visible in the design whose behaviour is ambiguous.

## 4. Interactions
**Partly known:** the design implies some (a FAQ list implies expand/collapse).
**Ask:** everything else. A static design cannot express hover states,
animation timing, or what happens on error.

`web-build` requires the person to confirm this list before coding, so
produce it as a list they can tick through, not prose.

Every animation needs a `prefers-reduced-motion` answer.

## 5. Forms
**Known from the brief:** that a form exists, and its business purpose.
**Ask nearly everything else** — this is where specs are thinnest and
rework is most common:

- Exact fields, and which are required
- **Where submissions go** — inbox, CRM, database. If a form collects emails
  for later marketing, that is a different system than a contact form, with
  different consent requirements.
- Who gets notified, and how
- What the user sees after submitting
- Consent wording, retention, controller

If the answer to "where does it go" is unclear, mark it `⚠ OPEN` and treat
it as blocking. A form built against a guessed destination gets rebuilt.

## 6. Integrations
**Ask:** which services, what they do, who owns the accounts and keys.

Two rules that are not negotiable:
- Every external call is declared in `externalCalls` in the manifest. CI
  greps the code, so an undeclared call fails the build.
- Anything using a secret key is **server-side**. A key in client-side code
  is public, whatever the docs of the service say.

## 7. Data
**Ask:** what is stored, where, for how long, who can read it.

Storage stays on evisions infrastructure — hosted database services are
refused by CI. See `evisions-constraints.md` for the list.

If nothing is stored (a form that only sends an email), say so explicitly.
"No data storage" is a valid and useful answer.

## 8. Measurement
**Ask:** GA4/GTM account ownership and access — another one that takes days
to arrange.

**Consent first.** Measurement fires only after consent, wired in from the
start. Retrofitting consent onto an already-instrumented site is both more
work and a compliance risk in the meantime.

## 9. Content
**Ask:** who writes the copy and when.

This is the most common cause of a build stalling at 90%. Agree the
placeholder rule up front: what ships if copy is late, and who decides.

## 10. Non-functional
**Known from the brief:** breakpoints, language versions, sometimes WCAG.
**Check the segment profile** — for B2C fintech in the EU, WCAG AA is a legal
requirement under the EAA, not an optional target. Don't take "AA is nice to
have" at face value without checking which segment the client is in.

## 11. Risks and dependencies
**This section earns its place by being written early.** Client Google
account, domain access, hosting credentials, a booked AI team session — all
take days and all are needed at the end. Listing them in week one is the
difference between a smooth go-live and a stalled one.

## 12. Open questions
Split **blocks the start** from **resolvable during the build**. Without that
split the list is just anxiety; with it, it is a to-do with priorities.

## 13. Implementation plans
See `implementation-plans.md`.
