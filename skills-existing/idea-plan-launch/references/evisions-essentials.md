# Build for evisions — the essentials

Everything you need to build something that can actually run on evisions infrastructure. This travels inside the skill, so you have it even without access to the AI team's internal docs.

## The one non-negotiable rule

**Nobody gets a shell on the evisions server. Not you, not the AI team's deploy job.**

What changed in July 2026: **you now ship your own app.** Merging your pull request into `main` is what puts it on the server, automatically, and you never touch the server, learn Docker, or see a production secret. The AI team owns the pipeline and onboards your app to it once; after that the merge button is yours.

What did not change: **you stay responsible for what you built, including fixing it when it breaks.** The AI team hosts, you maintain. When it breaks, they ping you, you fix, you merge, it redeploys.

If you leave the company or move on, hand the project to a named successor (good docs, add them to the repo, brief them). A project nobody can maintain gets deprecated, and the AI team reserves that right.

## 1. Tech stack — use what we run

- **Apps:** Next.js + TypeScript + Tailwind + Docker + Caddy
- **Data:** **Postgres** (17 unless you have a reason), or **SQLite** for a small single-writer app, or a **JSON file** for something trivial. CI enforces this list and nothing else: no Supabase, PlanetScale, Firebase, MongoDB Atlas, Upstash, Neon or Xata. Company data stays on our infrastructure, and the check greps your code, not just your manifest, so an SDK import will fail the build.
- **Automation:** n8n (`evisions-n8n.evisions.cz`)
- **Auth:** four modes, and you must declare which one in the manifest. See section 9.

Don't pick a different stack (Python/Flask, MySQL, etc.) without checking with the AI team first — deviating is possible but needs a conversation.

## 2. Make it actually deployable (server constraints)

The server is small and Docker-based, so the app has to be built to fit:

- The app ships with a **`Dockerfile`**, a **`docker-compose.yml`**, and **`evisions-app.json`** (the manifest, section 2a — CI rejects the app without it).
- **One database container and one Docker network per app** — never share a database between unrelated projects.
- **Secrets live in a `.env` file**, referenced as `${VAR}` in compose. Never hardcode them in the compose file or the code. On the server that `.env` is root-owned, provisioned once by the AI team, and never in git.
- Every service uses **`restart: unless-stopped`**; databases use **named volumes**.
- **Your compose file must not publish ports.** No `ports:` section at all: you declare `hostPort` in the manifest and the platform generates the publish line itself, bound to loopback only, so Caddy stays the single entry point. CI fails any compose file with `ports:`, `privileged: true`, `network_mode: host`, a bind mount of a host path, or the Docker socket.
- Keep it **lightweight**. You declare your own caps in the manifest (`memLimit`, `cpus`) and the platform enforces them; the probe app runs in 128 MB and a quarter of a CPU, `hr-orgchart` in 1 GB and one CPU.
- **Which domain it lives on:**
  - **Trial and internal apps: `<slug>.internal-apps.evisions.cz`.** The wildcard DNS record exists, so a new app gets a working hostname with no DNS work at all.
  - **A promoted app graduates to the flat namespace**, like `orgchart.evisions.cz`, once it's a real daily-use tool. That's a conversation, not a self-service step.
  - **Client-facing apps use `[client].evisions.online` (never `.cz`)** — own subdomain, own database, on a separate client server. If you're not sure which bucket you're in, ask before you build.
- It must **`docker compose up` cleanly on a fresh machine** — no "works on my laptop."

You don't touch the server itself (SSH, Caddy config, firewall, backups). Nor does the deploy job: it authenticates with a key that can run exactly one command and nothing else.

## 2a. The manifest — `evisions-app.json`

One JSON file in the repo root that tells the platform how to run your app. CI checks it exists, is valid JSON, has every required field, declares a known auth mode, and lists env var **names only** (a value here means a secret in git). This is the real one from `hr-orgchart`:

```json
{
  "name": "hr-orgchart",
  "department": "hr",
  "owner": "nova@evisions.cz",
  "hostnames": ["orgchart.evisions.cz"],
  "appService": "app",
  "internalPort": 3000,
  "hostPort": 3130,
  "stack": "nextjs16/prisma/postgres17",
  "database": { "engine": "postgres", "version": "17", "extensions": [] },
  "migrate": "prisma",
  "auth": "google-oauth-domain-restricted",
  "envVars": ["DATABASE_URL", "AUTH_SECRET", "AUTH_GOOGLE_ID"],
  "externalCalls": ["accounts.google.com", "oauth2.googleapis.com"],
  "internalOnlyPaths": ["/api/eva/*"],
  "resources": { "memLimit": "1g", "cpus": "1.0" }
}
```

Three fields worth understanding rather than copying:

- **`auth`** is Šimon's "is it behind a login, yes or no" checkbox. `none` is allowed, but only as a conscious declaration: CI prints a warning saying anyone with the URL sees everything in the app. Omitting the field is not allowed.
- **`externalCalls`** lists every host your app talks to. CI greps your code for URLs and fails on any host you did not declare, so this doubles as a review of what your app actually reaches out to.
- **`internalOnlyPaths`** keeps service endpoints off the public hostname. `hr-orgchart`'s eva endpoint is a 404 from outside and a 401 from inside, because it is listed here.

Ask the AI team for a free `hostPort`; they are allocated per app so two apps cannot collide. Copy the whole setup from **`evisions-apps/ai-deploy-probe`**, which exists to be copied.

## 3. Naming

- **GitHub repo:** `<scope>-<name>` in the `evisions-apps` Organization (see section 4). Scopes: `hr`, `scs`, `ppc`, `seo`, `krea`, `ux`, `analytics`, `pr`, `c-level`, `ai`, `shared`, `agency`, or the literal word `client` (never the real client's name). Use `agency` for agency-wide tools no single department owns (internal assistants, all-employee dashboards). No `evisions-` prefix — the org name already says it.
- **Database:** snake_case singular tables, `id` + `created_at` + `updated_at` on every table, `is_*` for booleans, `_at` for timestamps.

## 4. GitHub — your app lives in the `evisions-apps` Organization

**Apps go in one Organization: `evisions-apps`.** Not your personal account, not one Org per department, and not `ai-evisions` (which stays for the AI team's own dev infrastructure). Departments are **Teams** inside that single Org: you sit in your department's team with write access **to your own repo only**, via a fine-grained token scoped to that one repository. Owners are the AI team.

Why one Org rather than eight: GitHub Team bills per member *per Organization*, so eight department Orgs at three people each would be roughly 96 USD a month for the same three people. Fine-grained tokens already scope per repository inside one Org, so the split bought nothing.

- Ask the AI team to create the repo. Members can't create repos in the Org, deliberately: the Org's first repo held 50 colleagues' phone numbers, so access is granted per repo rather than Org-wide.
- **Join with your own GitHub account**, the one with your name on it. Create one if you don't have one. The shared company login that lives in 1Password has **no access to these repos** and will show you a `404`, which looks like "the repo doesn't exist" but means "you are signed in as the wrong account". Your own account is also what makes the branch names, the merge and the Slack deploy message attributable to a person.
- **You get `write`, not `admin`, and that is enough.** Push, open PRs, merge, deploy. You cannot change repo settings, manage secrets, or make the repo public. That is by design, not something missing.
- **Your repo arrives pre-loaded.** It is created from `evisions-apps/ai-deploy-probe`, so `ci.yml`, `deploy.yml`, `Dockerfile`, `docker-compose.yml` and a manifest skeleton are already there. Fill them in. Do not hand-write your own CI or compose file: CI will reject a compose file that publishes ports, and a manifest still carrying the template's hostname or port is refused outright.
- Repo name follows `<scope>-<name>` as in section 3.
- **During development** the repo is already the real one, from day one. Small commits, clear messages. Git is where code lives; Drive folders are for documents.
- **If you started on your own account**, the AI team transfers it in (Settings → Danger Zone → Transfer ownership). That happened with `hr-orgchart`.

**Branch protection does not exist here, and that is a deliberate choice.** GitHub Free gives private repos no branch protection and no required reviews, and we are not buying Team (decided 2026-08-10). Two consequences you should know rather than discover:

- **GitHub will let you merge with red CI.** The button cannot be greyed out. What stops bad code reaching the server is that the deploy re-checks CI for that exact commit and refuses. A bad merge lands in `main` and simply does not ship.
- **A direct push to `main` deploys nothing**, because the deploy fires on a *merged pull request*, not on a push. It also posts a Slack warning, so it is noisy rather than silent. Use a branch and a PR.
- Internal code stays in **private** repos. Private is the default; don't make a repo public to "make sharing easier."
- **Going public is a deliberate exception.** The only reason to make a repo public is when client-facing assets must load without a login (e.g. images a deck or client page pulls via CDN). A public repo's history is **permanent and public**, so only client-safe, consent-cleared content goes in — never internal data, client work, or un-consented people photos. The sanctioned pattern is the `ai-evisions/presentation-assets` repo served through jsDelivr (`https://cdn.jsdelivr.net/gh/ai-evisions/<repo>@<tag>/<path>`, pinned to a git tag). Brand tokens (`ai-evisions/brand-tokens`) are **private** — consume them by copying `dist/`, not via a CDN URL.
- **No secrets in git, ever.** `.gitignore` must cover `.env*`, `*-sa.json`, `*.key`, `*.pem`, `rclone.conf`, `/backups/`. Use `.env.example` for shape only.
- Full GitHub reference (accounts, repo inventory, public-asset CDN): the AI team's `github-setup.md`. The as-built pipeline and the reasoning behind it: `champion-app-deploy-pipeline.md`.

## 5. Documentation

Every project ships with `README.md`, `CLAUDE.md`, and a `docs/` folder (runbook, env-reference, security if relevant, ADRs for non-obvious choices). Use the `/appdoc` skill — it scaffolds real, project-specific docs. This skill also gives you a starter `CLAUDE.md` (see `claude-md-template.md`).

## 6. Design — build on-brand

Follow the evisions design system from the start. See **`evisions-design.md`** (bundled next to this file) for colors, typography, and layout. Don't invent your own look.

## 7. Client data — follow the evisions AI policy

If the project touches client data (names, numbers, domains, performance metrics, or any combination that could identify a client), follow the policy **before any AI tool sees it**:

- 🔴 **RED** — client name + numbers together, passwords, margins, identifiable combinations → **never** goes into AI.
- 🟠 **ORANGE** — anonymized results, generic descriptions, Ahrefs/SEMrush data without internal context → only after anonymization + department-lead approval.
- 🟢 **GREEN** — your own methodology, generic research, fake test data → free.

Authoritative source (COO-signed): https://docs.google.com/document/d/1t1oRCTcK8NEDNDql-f3mjcXSrsb4FCPUG87i1L_zXl4/edit
Incident contact: **Petr Hlaváč (COO)** — escalate immediately on any suspected breach.

## 8. Secrets — never in git, never in AI

No API keys, passwords, tokens, or service-account JSONs in the repo or in any AI chat. If a secret ever lands in git history, rotate it **and** purge the history (`git filter-repo` or BFG) before anyone else can clone the repo. Rotating without purging leaves the old key readable in every clone; purging without rotating leaves a live key that was already exposed.

Three things about secrets here specifically:

- **The gitleaks step in CI is our only secret gate.** GitHub's own secret scanning and push protection are *not* available on Free private repos (verified against the API, 2026-07-27), so a push is never "scanned by GitHub". It runs over your full history, not just the diff, because a key committed and later removed is still leaked.
- **Production secrets never come from you.** The server holds a root-owned `.env` the AI team provisions once. You develop with your own throwaway keys and list only the variable *names* in the manifest.
- **Anyone who can push to a repo can print its Actions secrets from a workflow.** Masking doesn't prevent it. So new secrets are designed to be useless if stolen, the way the deploy key is: it runs one fixed command, for one app, on a commit that is already merged.

## 9. Auth & app security — every app is locked down by default

There is no such thing as an evisions app with open, unauthenticated pages that show real data. Build the app to *expect* to be behind a login from day one.

- **Every app sits behind login, and declares which kind in the manifest.** CI accepts exactly four values for `auth`, and rejects anything else:

| `auth` value | Use it when | What you write |
|---|---|---|
| `oauth2-proxy` | "employees only", no roles inside the app | **Nothing.** The platform puts the gate in front. Four existing apps work this way, and it is the right default if it fits. |
| `google-oauth-domain-restricted` | you need the person's identity inside the app (roles, "my" pages, an audit trail) | NextAuth restricted to `@evisions.cz`. This is what `hr-orgchart` runs. |
| `resend-magic-link` | you need in-app identity but want to avoid Google credentials | e-mailed magic links. Available, and the reason it exists is that every `@evisions.cz` account has GCP access, so a leaked OAuth credential is a bigger blast radius than it looks. |
| `none` | genuinely public | Nothing, but CI prints a warning that anyone with the URL sees everything, and the AI team will ask you about it. |

  Client apps on `.evisions.online` use Google login with a password fallback for clients on Microsoft or other identities. **Don't roll your own auth** or invent a login system.
- **No unauthenticated data.** No page, API route, or endpoint may return internal or client data without a valid session. Protect the **API routes**, not just the pages — a logged-out request to `/api/...` must be rejected, not just hidden from the menu.
- **Secrets stay server-side.** Env vars are read on the server only. Never ship a key, token, or service-account into the browser bundle (in Next.js: nothing secret in `NEXT_PUBLIC_*`, never import a secret into a client component).
- **Don't trust the client.** Validate and authorize on the server for every action; client-side checks are UX, not security. Scope every database query to the logged-in user / client so one tenant can never read another's data — this matters most on client apps, where each client has their own subdomain and database.
- **Run `/security-review`** before handover for anything that touches auth, client data, or external input, and fix what it finds.

### The trap that actually caught us: middleware that protects nothing

In **Auth.js v5** (NextAuth's current version), a `middleware.ts` that does `export { auth as middleware }` only **attaches** the session to the request and then lets it through. Allowing or denying is decided exclusively by the **`authorized` callback**, and if you don't write one, every page is public. In NextAuth v4 the same-looking code blocked by itself. There is no warning, no error, and the app looks completely normal.

This shipped on a real evisions app in August 2026: `orgchart.evisions.cz` answered `200` to anyone on the internet with no login. It was caught only because someone opened the URL while logged out.

So, concretely:

- If the app uses Auth.js v5, it has an `authorized` callback that **denies by default**, allows `/api/auth/*` and `/login` explicitly (otherwise signing in is itself blocked and the redirect loops), and returns **401 JSON** for `/api/*` rather than redirecting a programmatic caller to an HTML login page.
- **Don't rely on middleware alone.** Every page that reads real data checks the session itself too. Note that a helper like `getViewer()` usually does *not* throw for an anonymous visitor, it returns an empty viewer, so the query below it runs happily and renders.
- Whatever the framework, the check is the same: request a protected page and a protected API route **while logged out** and look at the actual status code.

You don't configure the server-side gate, Caddy, or the certificates — that's the AI team at deploy. Your job is to build the app so it refuses to leak data on its own.

## 10. Test locally before handover — localhost first, always

Nothing goes near the server until it runs and has been tested **end-to-end on your own machine**. "The AI wrote it and it looks done" is not tested — AI coding sandboxes often lack Docker or a database, so code that never actually ran WILL have surprises on first launch.

- `docker compose up --build` from a fresh checkout, with `.env` filled in from `.env.example`. If the app uses Google OAuth, build in a dev-login flag for local testing (e.g. `ALLOW_DEV_LOGIN=true`, off by default) so you don't need production OAuth credentials — the AI team simply won't set the flag on the server.
- Run the seed / data import against real or realistic data and check the result. **Test against a seeded database, never an empty one.** An empty database looks exactly like a working permission check: the page returns no data either way. That is precisely what hid the org-chart leak above — the app was serving the directory to the whole internet and looked fine, because nothing had been imported yet. "No data came back" proves nothing until there is data to come back.
- Where you can, make the test independent of the data instead: point `DATABASE_URL` at a dead port and try the protected page again. Any code path that reaches a query now throws a 500, so a clean redirect to `/login` proves the page body never ran at all.
- Walk a functional checklist of **every feature**. If the app has roles, test **every role** — including what each role must NOT be able to see or do.
- Verify auth at the **API level**, not just the UI: an unauthenticated request to any `/api/...` route must be rejected, and role-filtered fields must be absent from the JSON, not just hidden on screen.
- Fix everything that falls out, in small commits, before you hand over.

## 11. CI — the gate between your laptop and production

Your repo gets `.github/workflows/ci.yml` from **`evisions-apps/ai-deploy-probe`**, which exists to be copied. Don't write it yourself. It runs on every pull request to `main`, and since merging a PR is what deploys, it is the only thing standing between your laptop and the server.

**`platform-gate` — five checks, all of which can refuse your app:**

1. **Secret scan** (gitleaks, full history).
2. **Manifest present and valid** — every required field, a known `auth` value, env var names without values.
3. **Database policy** — Postgres, SQLite, JSON or none, and a grep of your code for third-party database SDKs.
4. **Container isolation** — no published ports, no `privileged`, no host networking, no host bind mounts, no Docker socket.
5. **External calls match the manifest** — every host your code contacts must be declared.

**`build`** then builds the image and checks the container actually starts and serves HTTP.

Three rules that make it worth having:

1. **Prove it can fail, once.** Break something on purpose, push, watch the run go red, then put it back. A check nobody has ever seen fail is not a check, it is a green badge. This is not hypothetical here: on this pipeline's first run a review step exited "success" in 1.5 seconds having done no work at all.
2. **Work on a branch.** The convention is `<surname>/<topic>`. Never push `main`.
3. **Never merge red.** GitHub will *let* you (section 4), and the deploy will then refuse, so the only thing you achieve is a broken `main` that doesn't ship. If the PR contains code, run `/code-review` on it before merging and fix what it finds.

Adding your own checks on top is welcome. Look out for one specific mistake when you do: **a check whose scope includes itself.** Two of this pipeline's four launch bugs were exactly that, including a database-policy grep that matched the provider names written in its own source.

If the project is an n8n flow rather than an app, the equivalent gate is `validate_workflow` clean before handover.

## What the AI team checks before deploying

Save yourself a round-trip — check these first:

- ✅ `docker compose up` runs cleanly on a fresh machine
- ✅ Type-check / build passes (`npx tsc --noEmit`, `npm run build`, or equivalent)
- ✅ CI exists, has been seen to fail on purpose, and is green on the current commit
- ✅ No secrets in the repo
- ✅ `README.md`, `CLAUDE.md`, and `docs/` exist and are current, and every claim in them is true
- ✅ `evisions-app.json` is present, valid, and honest (especially `auth` and `externalCalls`)
- ✅ The repo is in `evisions-apps`
- ✅ You completed the localhost end-to-end pass (section 10) — all features, all roles, seeded data

When it's ready, hand over with the `/ai-handover` skill. This onboarding is once per app: the AI team allocates the hostname and port, provisions the server-side `.env`, and installs the deploy key. After that you ship your own changes.

## What a correct evisions project looks like

If you're unsure what "done properly" means, read one that is. Both are private; ask in `#ai-insights` if you can't see them.

- **`evisions-apps/ai-deploy-probe`** — the smallest possible app that deploys. **Copy your manifest, CI and deploy workflow from here.** It exists for that.
- **`evisions-apps/hr-orgchart`** — the first champion-built app through this pipeline. Next.js + Prisma + Postgres, domain-restricted Google OAuth, role-filtered API, a real manifest.
- **`ai-evisions/hr-vibes`** — Next.js + Prisma + Postgres, internal survey platform. Sidebar shell, an anonymity-preserving schema, full `docs/` with ADRs. Predates the pipeline, so ignore its deploy setup.
- **`ai-evisions/scs-sales-dashboard`** — vanilla HTML + Chart.js, top-bar layout. A dense single-page data view, for when a full app would be overkill.

## Where it ends up

Machine-deployed apps live on the evisions Hetzner server under `/srv/apps/<slug>/`, behind Caddy on their hostname. (The older hand-deployed apps sit in `/root/apps/`; keeping the two sets apart is deliberate.) You never go in there; it's listed so the paths in a runbook make sense to you.

## After go-live: how your changes reach the server

This is the part that changed in July 2026, and it is the whole point of the pipeline:

```
Claude Code creates a <surname>/<topic> branch, never pushes main
      ↓
Pull request to main   ← this IS the deploy request. No PR, no deploy.
      ↓
CI: secrets, manifest, database policy, container isolation, external calls, build
Claude reviews the diff and posts a verdict
      ↓
you click Merge yourself   ← nothing merges automatically
      ↓
the deploy re-checks, for THAT EXACT COMMIT, that CI was green and a review exists
      ↓
pull the pinned commit, read the manifest, compose up, migrate, health check,
write the Caddy vhost, roll back on failure, announce it in Slack
```

Day-to-day for you: develop and test locally, push a branch, open a PR, merge when green. **The merge is the deploy.** Nothing here needs an AI-team human.

Two properties worth knowing, because they explain the behaviour you'll actually see:

- **The deploy pins an exact commit** and only accepts one that is already an ancestor of `origin/main`, so two people merging at the same moment cannot deploy each other's code.
- **A failed deploy rolls back** and the previous version keeps serving. Nothing half-deploys.

### "I merged it and nothing happened"

Five different causes look identical from the outside, so check them in this order before asking anyone:

1. **You pushed straight to `main`.** The deploy fires on a *merged pull request*, never on a push, so nothing ran. It does post a Slack warning, which you probably won't see. Open a PR instead.
2. **You merged with red CI.** GitHub lets you, because branch protection isn't available here, and the deploy then refuses. The PR looks merged and shipped, and nothing was.
3. **No review exists for that commit.** The deploy requires one and will not run without it. Three different things cause this, and only the last is yours to fix:
   - **You opened the PR from a shared login instead of your own account.** The reviewer refuses to run unless the account that triggered the run has write access to the repo, and answers `401 User does not have write access`. This is the concrete reason section 4 tells you to use your own named GitHub account.
   - **Your PR edits the reviewer's own workflow file.** It then refuses to run at all, saying `Workflow validation failed`. Nothing can make it review such a PR, so that one needs a human review instead. Rare unless you are working on the pipeline itself.
   - **The reviewer ran and published nothing.** This does still happen. If everything else looks right, it is the AI team's problem to fix, not yours.

   Historical note worth keeping, because it explains why people around you may distrust this check: until 2026-08-18 the gate itself could never pass. It fetched the base branch unauthenticated against a private repo, so it went red whether or not a review existed, including on PRs the reviewer had genuinely reviewed. A check that is red no matter what teaches everyone to merge over red, which is exactly what happened to the PR that then sat unshipped for a week. That bug is fixed.
4. **The deploy ran and rolled back.** The previous version keeps serving, so a failed deploy and a deploy that never started look exactly the same from the browser. The Slack message says which.
5. **The server was out of disk.** The deploy cancels before building anything and says so.

In all five cases the Slack notification and the deploy workflow's log say what happened. And when it did work, confirm it the only way that counts: **open the URL and check the behaviour you changed.** A merged PR is not a live change.

**One caveat, learned the hard way: merging is not shipping.** The merge triggers the deploy; it is not proof the deploy finished. A build can fail, a container can keep serving the previous image, and the PR still looks merged and green. Before you tell anyone the fix is live, **open the actual URL and check the actual behaviour**. A green pipeline is a proxy; the page is the result.

---

*This is the champion-facing copy. The AI team maintains the full versions (`build-for-evisions.md`, `hetzner-server-rules.md`, `ai-team-tech-stack.md`, `github-setup.md`) and keeps this in sync. If something here seems out of date, ask via Book an AI session (see `where-to-get-help.md`).*
