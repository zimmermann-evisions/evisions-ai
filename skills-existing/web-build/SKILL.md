---
name: web-build
description: >-
  Guides building the actual client website in Claude Code (static
  HTML/CSS/JS, hosted on Websupport — no Framer) once the brief, wireframe,
  copy and high-fi design exist. Covers cookie consent (every site gets a
  cookie bar), meta tags / tech SEO, the build-time SEO + accessibility
  checklist, staging noindex and go-live. Use when someone says "poďme
  kódiť web", "postav web", "build the site", "cookie lišta", "meta tagy",
  or when ai-web-design reaches the Build phase. Asks the person questions
  and gives step-by-step procedures — never silently decides on tools,
  accounts or keys on their behalf.
---

# web-build — building the client site in Claude Code

**Status:** Done: build process, cookie consent, meta tags / tech SEO,
checklist, staging, GA4 + GTM, lead form, visual QA. Upload to client hosting is manual (section "Build process", step 8). Nothing blocking left — see ai-web-design "Still open".

## Build process — order of work
1. **Setup.** Build in **Claude Code** on the person's computer, not in chat.
   Site type: for client hosting a **static site** (HTML, CSS, JS) that runs
   on any hosting. If the site goes on evisions servers, the tech follows
   evisions rules — decide with the AI team. Keep the code in a GitHub
   repo for history and recovery; where exactly, confirm with the AI team.
2. **Figma → code.** Via the Figma connector, read the design page by page,
   section by section. Colors, fonts, spacing only via design tokens
   (`tokens.css`) — never a raw value like `16px` or `#D0021B` in the code,
   so one change in the tokens updates the whole site; texts from the approved copy, never invented; images resized and
   compressed.
3. **Responsive.** Every section for desktop, tablet and mobile per the
   brief's breakpoints (default 1440 / 768 / 390).
4. **Interactions.** From the design, list what happens on interaction:
   mobile menu, FAQ accordion, button and form states, animations. The
   person confirms the list before it's coded. Animations respect "reduce
   motion".
5. **Integrations, in this order:** cookie bar (section 1) → GTM + GA4
   (section 5) → lead form (section 6) → legal pages → meta tags + SEO
   (section 2). Measurement must be tied to consent from the start.
6. **Checks.** Compare with the Figma screenshot (does the site match the
   design?); tech SEO + a11y checklist and Lighthouse (section 3); visual
   QA (section 7); browsers Chrome, Safari, Firefox + mobile.
7. **Staging.** Test address, password-protected, `noindex`. Person (and
   client if needed) approves there.
8. **Deploy.** Client hosting: the person **uploads the files manually**
   (hosting file manager or SFTP). Prepare for them: one folder with the
   finished files, and a short checklist (upload everything, keep the
   folder structure, check the site loads on the domain). evisions
   servers: hand over to the AI team via `/ai-handover`.
9. **Go-live.** Remove staging `noindex`, connect the domain, verify HTTPS,
   submit sitemap in Google Search Console, send a test lead and confirm
   GA4 measures it.

## Inputs — never invent, always take from earlier steps
| What goes into the code | Source |
|---|---|
| Page title + meta description | brief 5c SEO table — primary phrase of that page |
| H1 + headings | approved copy; H1 contains the page's primary phrase |
| Alt texts | asset list + each image's purpose on the page |
| Redirects, noindex pages, language versions | brief 7b Tech brief |
| Company data for structured data | brief 5b Legal profile |
| Colors, fonts, sizes, radius, spacing | design tokens file `tokens.css` (see `../ai-web-design/references/design-tokens-prep.md`) — use only token names, never a raw value in the code |
| Cookie bar + form texts | `wireframe-intake/references/legal-templates/cookie-policy.md` |

If an input is missing, stop and ask — don't fill a title, meta description
or legal text with a guess.

## 1. Cookie consent — every site gets a cookie bar
evisions standard: **every site gets a cookie bar**, no exceptions. (Even a
site with only technically necessary cookies gets one — then it just
informs; the consent logic below matters once any analytics/marketing tool
is added.)

**Step 1 — confirm what will run.** Read brief 5b "Third-party tools" and
confirm with the person what will actually run on the site: GA4, GTM,
Meta Pixel, Hotjar/Clarity, embedded YouTube/Google Maps, others. This list
drives the categories and later the cookie policy table.

**Step 2 — ask: external service or own code?**

| | External service (e.g. Cookiebot, Consentio) | Own code (open-source library) |
|---|---|---|
| When | several tools, marketing pixels, client wants to manage consents themselves | only GA4 via GTM, simple site |
| Plus | scans cookies and generates the cookie list itself | no fees, full control over look |
| Minus | subscription (check current price when choosing) | cookie list for the policy is manual |

For own code use the free, established library **vanilla-cookieconsent**
(supports categories + Google Consent Mode). Never build consent logic
from scratch.

**Procedure A — external service**
1. Person creates the account and enters the domain (account ownership:
   client vs evisions — check brief 5b "Who has access to leads").
2. Let the service scan the site; review categories (necessary /
   analytics / marketing).
3. Turn on Google Consent Mode v2.
4. Insert its script as the very first script in `<head>`.
5. Run the shared test below.

**Procedure B — own code**
1. Claude Code adds the library and sets three categories: necessary,
   analytics, marketing.
2. Default state: everything except necessary is **off** until the visitor
   consents (Consent Mode default = denied).
3. "Přijmout vše" and "Odmítnout" equally prominent (same size/contrast).
4. "Nastavení cookies" link in the footer so consent can be changed anytime.
5. Texts from `cookie-policy.md` (companion copy section).
6. Run the shared test below.

**Shared test — mandatory for both**
1. Open the site in an incognito window.
2. **Before clicking consent:** no measurement fires and no `_ga` cookie
   is stored.
3. After "Přijmout" measurement fires; after "Odmítnout" it stays off.
4. Only then fill the cookie table in the cookie policy from the cookies
   actually deployed (never from assumption — see `cookie-policy.md`).

## 2. Meta tags + tech SEO
Every page gets a `<head>` filled from the inputs table, e.g.:

```html
<head>
  <title>[primary phrase + benefit] | [Company]</title>
  <meta name="description" content="[from approved copy]">
  <meta property="og:title" content="[...]">
  <meta property="og:description" content="[...]">
  <meta property="og:image" content="/img/share.jpg">
  <link rel="canonical" href="https://[domain]/[page]">
</head>
```

Also generate `sitemap.xml`, `robots.txt`, 301 redirects (redesign, from
7b), hreflang (if 7b lists more languages) and JSON-LD structured data
(Organization; FAQPage where there's an FAQ).

**Human reviews text, not code:** at the end show one table of every page's
title + meta description so the person can read and approve them together,
like the rest of the copy.

## 3. Build-time checklist
After the build, go through every page against
`../ai-web-design/references/tech-seo-a11y-checklist.md`. Where possible
run an automated check too (e.g. Lighthouse for SEO / accessibility /
performance). Show the person the result as a list: passed / to fix.

## 4. Staging and go-live
- While on a test address: `noindex` on the whole site.
- At go-live: **remind the person to remove the staging noindex** — a
  common mistake that hides a live site from Google.
- Keep noindex only on pages listed in 7b (e.g. thank-you page).
- Upload to Websupport: procedure pending hosting type (ask).

## 5. GA4 + GTM
GA4 = the analytics itself (where the numbers are read). GTM = the "switch
board" measurement codes run through, so new tracking doesn't need to be
coded into the site each time.

**Questions to ask first**
1. Does the client already have GA4 and GTM? If not, create them on the
   **client's Google account**, not evisions'; evisions gets access. The
   data belongs to the client.
2. What's the main conversion? Take primary + secondary conversion from
   brief 1b and confirm with the person — these become the key events.
3. Will ad tools run (Google Ads, Meta)? If yes, leave room for them, but
   they're configured with the PPC specialist, not here.

**Setup procedure**
1. GA4: create a property + web data stream for the domain → Measurement
   ID (`G-XXXX`).
2. GTM: create a web container → container ID (`GTM-XXXX`).
3. Claude Code inserts the GTM snippet on every page, directly **after**
   the cookie bar script.
4. In GTM: add the Google tag with the GA4 Measurement ID, set to fire
   only after analytics consent (Consent Mode).
5. Events: Claude Code adds a `dataLayer.push` signal in the site code for
   every event in the approved table below; in GTM each signal triggers a
   GA4 event tag. (dataLayer signals, not fragile GTM click selectors.)
6. In GA4, mark the key events as key events (conversions).
7. Test: GTM Preview (Tag Assistant) + GA4 DebugView. After rejecting
   cookies, nothing may be measured.
8. Publish the GTM container.

**Event plan — proposed from the design, approved before coding**
Go through the high-fi design section by section and propose an event
table. Don't implement anything until the person approves it.

Base set — every site:

| Event | Fires when | Parameters | Key event? |
|---|---|---|---|
| `cta_click` | click on a main CTA button | `cta_location`, `cta_text` | no |
| `form_start` | first field of a form filled | `form_name` | no |
| `form_submit` | successful submission | `form_name` | **yes** |
| `phone_click` | click on a phone number | `section` | **yes** |
| `email_click` | click on an email address | `section` | yes |

Add design-specific events where the design has the element, e.g.
`faq_open` (`question`), `calculator_use`, `pricing_toggle`. Naming:
snake_case, verb at the end, consistent parameters.

Don't duplicate what GA4 enhanced measurement already tracks
automatically: scroll, outbound clicks, file downloads, YouTube video
plays.

## 6. Lead form
Storage is always a lead app + PostgreSQL on the evisions server — see
`references/lead-storage-postgres.md` (includes the template check and the
guided build process when no template exists yet).

In the site code:
1. **Fields = the form in the design.** Confirm the field list with the
   person; it must match the lead app's table.
2. **Connect** the form to the deployed lead app address (the form sends
   its data there, like mailing it to an address). Also send `source` and
   the UTM parameters from the page URL.
3. **States the visitor sees:**
   - sending: button shows "Odosílám…" and is disabled (no double submit)
   - success: "Děkujeme, ozveme se" or redirect to the thank-you page
     (thank-you page is noindex, see 7b)
   - error: "Nepodařilo se odeslat, zkuste to znovu nebo nám zavolejte";
     filled-in data stays in the form
   - field errors shown next to the field, readable by screen readers
4. **Honeypot field** in the markup (the lead app rejects it when filled).
5. Form notice + separate unchecked marketing consent from
   `cookie-policy.md` companion copy.
6. Fire `form_start` / `form_submit` events (section 5).
7. **Test:** send a test lead from staging, confirm it arrives, and that
   the error state shows when the app is unreachable.

## 7. Visual QA of the built site
Review the built pages from screenshots, desktop and mobile, against
`../ai-web-design/references/visual-quality-review.md` (and the public
`frontend-design` skill). Output a list: fine / to improve (which element,
what's wrong, proposed fix). Final art direction call belongs to a human,
ideally a designer.
