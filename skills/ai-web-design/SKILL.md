---
name: ai-web-design
description: >-
  Orchestrator for the evisions AI web production flow: guides a person from
  "I have a client who needs a website" through brief, strategy, wireframe,
  visual design, build and go-live, calling the right existing skill at the
  right moment instead of dumping the whole process at once. Use when
  someone says "chcem urobiť web pre klienta", "AI web design flow", "kde
  začať s webom", or runs /ai-web-design. Does not do the work itself —
  orchestrates wireframe-intake, evisions-wireframe, mobbin-pattern-extraction
  and manual checklists where no skill exists yet.
---

# ai-web-design — evisions AI web production orchestrator

Mentors a colleague through the full web production flow the way
`idea-plan-launch` mentors a Claude Code build — meet them where they are,
one phase at a time, call the right skill instead of re-explaining it.

**Status:** built incrementally with the team. All phases are specified;
a few details remain open (see "Still open" at the end).

## Master flow (confirmed)

```
CLIENT
  ↓
BUSINESS + CONTENT + BRAND + LEGAL PROFILE   → wireframe-intake
  ↓
PROJECT CONTEXT (= completed wireframe-intake brief)
  ↓
SITEMAP / IA + WIREFRAME (in the Figma kit)  → evisions-wireframe
  ↓
MOBBIN PATTERN RESEARCH → HIGH-FI DESIGN      → mobbin-pattern-extraction
  (component by component, ~12 per page, human picks/mixes variants,
  human gate after every component — see that skill's step 3 and 6)
  ↓
ASSETS (Higgsfield or existing) — manual, checklist only for now
  ↓
DESIGN TOKENS + STATIC PREVIEW (build paths 1–4 only)
  → figma-to-tokens  (in Claude Code, not this chat)
  (creates Figma Variables first if the design has none; produces
   tokens.css + a static HTML/CSS preview; human approves the preview;
   then /impeccable extract → audit → normalize → polish)
  Paths 5, 6 and 7 stop before this — no build, so no tokens needed.
  ↓
TECHNICAL SPECIFICATION + IMPLEMENTATION PLANS (build paths 1–4 only)
  → tech-spec  (in Claude Code — run it with Opus)
  (reads the brief + approved design instead of re-interviewing; writes the
   spec and cuts it into small, independently buildable plans; human approves
   spec + plans before any code)
  Decides here: hosting (→ stack), where form data goes, integrations,
  measurement + consent, WCAG level. Wrong answers here get inherited by
  every plan, so this gate matters.
  ↓
BUILD — Claude Code writes the site directly (no Framer) → web-build
  (cookie bar on every site, meta tags / tech SEO, build checklist, staging)
  ↓
LEAD FORM → LEAD APP + POSTGRES (evisions server)
                                              → web-build/references/lead-storage-postgres.md
  (template exists → AI team deploys it; no template yet → guided build via
  /idea-plan-launch, deployed by the AI team)
  ↓
ANALYTICS + COOKIES, LEGAL PAGES              → evisions-wireframe step 5b
  (cookie banner, form notice, privacy/cookie policy — already generated
  as part of the wireframe handover, not a separate step here)
  ↓
SEO + ACCESSIBILITY QA — manual checklist (see gap note)
  ↓
PUBLISH → Websupport DNS → live
```

## Welcome and path choice — always start here
Reply in the language the person writes in (evisions: Czech, Slovak,
English, Polish, German) and switch when they switch — don't announce it.

Welcome:
> Ahoj! Som sprievodca tvorbou webu v evisions. Čo ideš robiť?
> 1. Nový web od nuly
> 2. Redesign existujúceho webu
> 3. PPC landing page
> 4. SEO štruktúra webu (optimalizácia)
> 5. Len wireframe
> 6. Len dizajn (wireframe už existuje)
> 7. Ukážka dizajnu do ponuky (SCS)

If they're unsure, help with one or two questions ("Máte už web?", "Ide o
kampaň alebo trvalý web?").

**Output language** — right after the path is chosen, for every path that
produces text for the client (all of them): ask what language the output
will be in. It can differ from the conversation language (a colleague
writes in Czech, the site is for the German market). It drives copy, SEO
phrases and legal documents — carry it through every later phase.

### Tool check — right after the path and output language
Principle: **don't ask about connectors in Claude — check the tool list
yourself.** Ask only about things outside Claude (access, accounts, files).

Check yourself (no question):
| Tool | For | Paths |
|---|---|---|
| Figma | wireframe kit + component library | 1–7 |
| Mobbin | component inspiration | 1, 2, 6, 7 |
| Ahrefs / Marketing Miner | SEO mapping | 1, 2, optional 5 |
| Higgsfield | missing visuals | 1, 2, 3, 6, 7 |
If one is missing, say exactly which and where to turn it on
(Settings → Connectors).

Ask — needed right away:
- link to the Figma file with the Wireframe Kit library attached (paths with a wireframe)
- the client's component library in Figma (paths 3, 4 needed; 2, 6, 7 optional — see "Client component library")
- client materials: logo, brand guide, old site, decks
- the specialist's keyword analysis + mapping (path 4)

Ask — needed later, but must be arranged early:
- **Claude Code** on their computer — the build doesn't run in this chat (paths 1–4)
- client's Google account for GA4 + GTM
- AI team session booked (they deploy the lead app — see lead storage)
- where the site will be hosted (client hosting / evisions servers) + hosting and DNS access
Mention these at the start even though they're used at the end — getting
domain or client Google access often takes days.

The hosting answer in particular is what `tech-spec` needs to decide the
stack, so having it early saves a blocked spec later.

Show a short summary, e.g.:
> ✅ Figma · ✅ Mobbin · ⚠ Higgsfield not connected (needed for visuals)
> **Needed later, start arranging:** domain access, client Google account.
Missing items needed only in a later phase don't block starting — continue
and remind the person when that phase comes.

### Which phases run for which path
| Phase | 1 New | 2 Redesign | 3 PPC | 4 SEO | 5 WF | 6 Design | 7 SCS |
|---|---|---|---|---|---|---|---|
| Tool/connector check | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Briefs (wireframe-intake) | full | full + old-site data | reduced (campaign brief carries the message) | none — input list below | full | brand only | pitch brief |
| Campaign brief | | | ✅ | | | | |
| SEO | mapping | mapping + redirects | | import specialist's files | optional | | |
| Wireframe in Figma (evisions-wireframe) | ✅ | ✅ | from client library | from client library | ✅ | | |
| Mobbin + design (mobbin-pattern-extraction) | ✅ | ✅ | | | | ✅ | ✅ auto component choice |
| Assemble from client component library | | optional | ✅ | ✅ | | optional | if we have it |
| Visual assets | ✅ | ✅ | ✅ | | | ✅ | ✅ |
| Design tokens + static preview (figma-to-tokens) | ✅ | ✅ | ✅ | ✅ | | | |
| Technical spec + implementation plans (tech-spec) | ✅ | ✅ | ✅ | ✅ | | | |
| Build (web-build) | ✅ | ✅ | ✅ | ✅ | | | |
| Lead form + database | ✅ | ✅ | ✅ | if there's a form | | | |
| GA4, GTM, cookie bar | ✅ | ✅ | ✅ | ✅ | | | |
| Legal pages | ✅ | ✅ | ✅ | ✅ | | | |
| SEO + a11y QA, publish | ✅ | ✅ | ✅ | ✅ | | | |

Paths 5 and 6: at the end offer to continue with the next phase instead of
starting over.

**Path 4 — SEO structure: no brief, just these inputs**
- site URL and which pages are being optimized
- the specialist's keyword analysis + keyword mapping (uploaded files)
- the client's component library (see "Client component library" below)
- the site's main conversion, so CTAs land in the right places

Then:
1. **Read the files** (Excel/CSV). Find the needed columns (keyword,
   volume, intent, target page). If columns are named differently or
   something is missing, ask — don't guess.
2. **Check the mapping** and list problems, never fix them silently: two
   pages targeting the same keyword (they compete in search); a page with
   no primary phrase; an important keyword with no page; intent mismatch
   (informational keyword on a sales page). The specialist decides.
3. **Compare with the current site** (optimization): which pages to
   change, add, merge. If URLs change, remind about 301 redirects.
4. **Ask:** "Ideme stránku po stránke (každú schváliš zvlášť), alebo
   navrhnem všetky naraz a schváliš celok?"
5. **Structure per page:** primary phrase in title, H1, meta description;
   secondary phrases in subheadings; questions in FAQ. Assemble only from
   the client's component library.
6. **The SEO specialist approves** before copy and build.
Output uses the same format as brief section 5c (page, primary phrase,
secondary phrases, FAQ), so copy and meta tags in `web-build` work the same
whether Claude or the specialist prepared the phrases.

**Client component library — when to ask, what if there's none**
Ask during the tool check, only where it makes sense:
| Path | Ask? |
|---|---|
| 1 New | no — new design |
| 2 Redesign | yes, optional |
| 3 PPC | **yes, needed** |
| 4 SEO | **yes, needed** |
| 5 Wireframe | no — evisions Wireframe Kit |
| 6 Design | yes, optional |
| 7 SCS | yes, optional |

If it's needed or wanted and the client has none:
- **Has a live site → build a basic library with html.to.design** (Figma
  plugin that turns a web page into editable Figma layers):
  1. Person gives the site URL. Claude goes through its navigation (and
     sitemap if reachable), groups pages by template and proposes one page
     per template — usually 4–6: homepage, one service/product page, one
     listing, one detail, contact (form), plus pages with something unique
     (pricing, FAQ, calculator). Never the whole site — same-template pages
     only repeat components.
  2. Person confirms and gets a ready list of URLs to paste into the plugin.
     **The person runs the plugin** — Claude can read/edit Figma via the
     connector but can't run third-party plugins.
  3. Import desktop; the homepage also mobile.
  4. Claude goes through the imported pages via the Figma connector, finds
     recurring components (buttons, cards, forms, sections) and extracts
     colors, fonts, spacing into a basic component set.
  5. A designer reviews it (imports aren't perfect), then continue as if
     the client had a library.
  Only the client's own site is imported. Check the plugin's current
  limits/licence before use.
- **No site either** → nothing to build a library from: use the evisions
  Wireframe Kit + brand (or the brand manual minimum) and design the
  standard way (Mobbin, designer or auto mode).

**Paths 3 and 4 — assembling from the client's component library**
1. Person gives the link to the client's Figma component library (none →
   see "Client component library" above); search it via the Figma connector.
2. Path 3: take the campaign brief (`wireframe-intake/assets/campaign-brief-template.md`,
   filled by the PPC specialist) and propose the structure per
   `references/landing-page-ux-standard.md`.
3. Assemble **only from the client's components** — no Mobbin, no new
   components of our own.
4. Missing component: never invent it. Say what's missing and propose the
   closest existing one, or leave it to the designer.
5. Human review before copy and build. Path 3: review the finished page
   against the landing page UX standard again before launch.

**Path 7 — design sample for a proposal (SCS)**
Speed and impression, not a finished site.
1. Pitch brief (5–7 questions): who the client is and what they sell;
   what the site should achieve; what to show (hero only, or 2–3 key
   sections); visual base — a) client's existing site/brand or b) a new
   direction; when the proposal goes out.
2. Look: a) person gives the current site URL, Claude extracts colors,
   type, logo and overall style (use the client component library if we
   have it); b) a few references of what the client likes.
3. Design 1–3 sections, components chosen automatically (auto mode in
   `mobbin-pattern-extraction`, visual quality review included).
4. Human reviews before it goes to the client; marked as a **concept**,
   not final design.
Skipped: legal, SEO, build, database, analytics. If the deal is won, offer
path 1 or 2 and carry everything over — don't re-ask the client.
Output format (image/PDF from Figma vs clickable link): still open.

## How to run this with a person

1. **Ask where they are** — brand new client with nothing, or already have
   a brief/wireframe/design? Don't restart from phase 1 if they don't need to.
2. **One phase at a time.** When a phase has an existing skill, say so and
   hand off to it — don't re-explain what that skill already does.
3. **Gates are real stops**, not formalities — especially the human gate in
   `mobbin-pattern-extraction` (step 6) and the two Figma gates inside
   `evisions-wireframe`. Don't let the person skip ahead past an
   unconfirmed gate just because they're in a hurry.
4. **Lead storage is PostgreSQL on evisions servers** — never a hosted
   database (Supabase etc. are refused by evisions CI; data stays on our
   infrastructure). Always a small lead app + Postgres on
   `[client].evisions.online`, wherever the website itself is hosted. See
   `web-build/references/lead-storage-postgres.md`.

## Still open
- Path 7 (SCS) output format: image/PDF from Figma vs clickable link.
- Being handled with the AI team: custom client domain for sites on
  evisions servers; where client site repos live; a reusable lead app
  template.
- Legal: cookie policy review and choice of licensed legal templates —
  being arranged with a lawyer.

## Files
- `references/tech-seo-a11y-checklist.md` — build-time SEO + a11y checklist.
- `references/landing-page-ux-standard.md` — landing page UX standard (path 3).
- `references/design-tokens-prep.md` — background on what design tokens are
  and why (the work itself is done by the `figma-to-tokens` skill, build
  paths 1–4 only).
- `references/visual-quality-review.md` — how to judge whether a design looks good (with `frontend-design`).
- Campaign brief template: `wireframe-intake/assets/campaign-brief-template.md`.
- Lead storage guide lives in `web-build/references/lead-storage-postgres.md`.
