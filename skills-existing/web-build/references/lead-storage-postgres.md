# Lead storage — PostgreSQL on evisions servers

> evisions rules (see `idea-plan-launch` → evisions-essentials): data stays
> on evisions infrastructure, **Postgres** is the standard, hosted
> databases (Supabase, Firebase, Neon…) are refused by CI. So leads always
> go to a small lead app + Postgres on the evisions client server
> (`[client].evisions.online`, one database per client) — no matter where
> the website itself is hosted (client's hosting or evisions servers).

## How it fits together
```
Form on the website (client hosting or evisions server)
        ↓  sends the filled-in data
Lead app on the evisions server ([client].evisions.online)
   – validates the data, filters spam
        ↓  writes
PostgreSQL database (same server, one per client)
```
The browser never talks to the database directly — it would need the
database password in public code.

## Step 0 — Does a lead app template exist?
- **Yes** → the AI team deploys it for the new client. Inputs to hand
  over: the form fields (from the design), the website domain allowed to
  send data, e-mails that get access to leads, notification e-mail if any.
- **No (current state)** → run the guided process below. The first app
  built this way is the natural candidate to become the template — flag
  it to the AI team at handover.

## Guided process — building the lead app now
Walk the person through this one step at a time; don't dump it all.

1. **Book the AI team early.** Deployment is done by the AI team
   (Teamwork → AI Insights). Book before starting — on a hackathon day
   this is the bottleneck.
2. **Prepare the spec with the person** (this goes into `/idea-plan-launch`):
   - form fields — taken from the actual form in the design, confirmed by
     the person; plus fixed technical fields: `created_at`, `updated_at`,
     `source`, `utm_source`, `utm_medium`, `utm_campaign`,
     `is_marketing_consent`, `privacy_version`
   - table name `lead` (evisions DB naming: snake_case singular, `id` +
     `created_at` + `updated_at`, `is_*` booleans)
   - which website domain may send data (CORS allow-list)
   - who sees leads: e-mails allowed to log in; notification e-mail on a
     new lead (yes/no)
   - spam protection (below)
3. **Hand off to `/idea-plan-launch` in Claude Code** with that spec. It
   owns the evisions build rules (stack Next.js + Postgres 17 + Docker,
   repo naming, CI, client data) — follow it, don't duplicate it here.
4. **Test locally end-to-end** as `idea-plan-launch` requires: submit a
   test lead, see it in the database, confirm the leads page is only
   visible when logged in (test against a database that has data in it).
5. **Hand over with `/ai-handover`**; the AI team deploys to
   `[client].evisions.online` and sets allowed log-in e-mails.
6. **Connect the form** in `web-build` (section 6) to the deployed app
   address and test on the live/staging site.

## Spam protection (in the lead app, invisible to visitors)
- **Honeypot field:** hidden field humans don't see, bots fill → rejected.
- **Time check:** submitted within a couple of seconds of page load → bot.
- **Rate limit:** only a few submissions per minute from one source.
- **Server-side validation:** valid e-mail, required fields, sane length.
- Only if spam still gets through: a CAPTCHA such as Cloudflare Turnstile —
  it's a third party, so it must then be added to the cookie/privacy docs.

## Who sees the leads — and credentials
- Client logs in with **their own Google account**; the AI team sets the
  allowed e-mails at deployment (evisions standard for client apps).
- Password fallback for clients without Google: created by the AI team at
  deployment and handed to the client securely **outside AI tools**.
- Never create, paste or handle passwords/API keys in Claude — evisions AI
  policy (RED data).
