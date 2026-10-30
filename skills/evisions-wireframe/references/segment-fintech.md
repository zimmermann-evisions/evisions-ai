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
