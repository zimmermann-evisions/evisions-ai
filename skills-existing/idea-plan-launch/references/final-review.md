# Final review — the gate before handover (run this every time)

When the build feels done, do NOT jump to handover. Run this gate first. You do the checking; the person doesn't have to judge anything, which is the whole point, because they can't yet.

## 1. The review gate

Work through these and report results plainly. Fix what fails before moving on.

- **Build / type-check passes** — `npm run build` and `npx tsc --noEmit`, or the project's equivalent. For an n8n flow, `validate_workflow` is clean.
- **It runs clean from scratch** — `docker compose up` starts with no errors on a fresh state. No "works on my machine."
- **It actually does what the PRD said** — walk the PRD's must-haves one by one and confirm each works. If there's no PRD, walk the original idea instead.
- **CI exists and is green** — `.github/workflows/ci.yml` is in the repo (section 11 of `evisions-essentials.md`), it has been seen to fail on purpose at least once, and the run on the current commit is green. "No checks defined", "queued" and "skipped" are not "passed".
- **Secrets scan** — no API keys, passwords, tokens, or service-account JSONs anywhere in the repo or the code. Grep the whole tree for `_KEY`, `_SECRET`, `TOKEN`, `password`, `BEGIN PRIVATE KEY` and token-shaped strings, and check `git log -p` too, not just the working tree. `.gitignore` covers `.env*`, `*-sa.json`, `*.key`, `*.pem`, `rclone.conf`, `/backups/`.
- **Auth is actually enforced** — and this one you *run*, you don't read. With the app up **and the database seeded**, request a protected page and a protected `/api/...` route with no session and check the status codes: page redirects to login, API returns 401, neither returns data. Then log in as each role and confirm role-filtered fields are absent from the JSON, not merely hidden on screen. An empty database passes this test by accident, which is exactly how a public org chart shipped (section 9 of `evisions-essentials.md`). Internal apps gate to `@evisions.cz`; client apps (`.evisions.online`) use Google login + password fallback. No secret reaches the browser bundle.
- **The docs don't claim more than the code does** — walk the security and auth claims in `README.md` and `CLAUDE.md` against what you just tested and delete or fix anything unproven. A doc asserting that pages are protected is how a real leak went unexamined for weeks.
- **Code review** — run the `/code-review` skill on the pull request and address what it finds. A review that was skipped, or that never ran, is not a passed review; say so plainly rather than letting silence read as approval.
- **Security review** — if the project touches auth, client data, or anything sensitive, run `/security-review` too.

Don't claim the gate passed unless you ran the checks and saw them pass. Evidence, not assumptions.

## 2. Get it onto GitHub (the right way)

This happens once the gate is green. **Read the person first.** If they already use git and GitHub, just do it and don't explain. If they look unsure, offer a one-line explanation ("GitHub is where the code lives so the AI team can deploy it and it isn't lost") and walk them through. Never force the beginner version on someone who already knows.

The steps:

1. **Sign in once** — `gh auth login` (it opens a browser). If `gh` isn't installed, point them to https://cli.github.com.
2. **Get the repo created in the `evisions-apps` Organization**, named `<scope>-<name>` (scopes and rules in `evisions-essentials.md`). Members can't create repos there, so this is an ask to the AI team, not a self-service step. A repo on a personal account is not deployable: the pipeline deploys from `evisions-apps`. If they already started on their own account, the AI team transfers it in.
3. **Make it private.** Internal code is never public.
4. **Confirm `.gitignore` is in place** before the first push, so no secrets ever enter git history.
5. **Push** — initialize, commit, push to the new repo. If they've never done this, do it for them, and explain what each step did in one line only if they want to know.
6. **Add the CI workflow** (section 11 of `evisions-essentials.md`) and prove once that it can fail. Do this now if it isn't already in the repo.
7. **Set the working rhythm from here on** — changes go on a branch, into a pull request, and merge only when CI is green. Explain it once, in one line ("the branch is your draft, the PR is where the checks run"), then just work that way with them. This is what the auto-deploy pipeline will be attached to later, so getting the habit now costs nothing and saves a broken production later.

Keep it **private** unless client-facing assets genuinely need public CDN access — see the public-repo guardrail in `evisions-essentials.md` §4. Full GitHub reference (account, repo inventory, serving public assets via jsDelivr) is the AI team's `github-setup.md`.

If a secret ever did land in git history, stop: it has to be rotated and the history purged before anyone else clones the repo.

## 3. Then hand over

Only now use the `/ai-handover` skill. The AI team runs the pre-deploy checks, onboards the app to the deploy pipeline (hostname, `hostPort`, the root-owned server `.env`, the deploy key) and registers it on the AI roadmap.

**This onboarding happens once.** After it, the person ships their own changes: branch, PR, green CI, merge, and the merge is the deploy. They never get server access. They stay responsible for fixes.

Then verify by result, not by proxy. A merged PR and a green pipeline are not the app being live: **open the hostname and check the behaviour you changed.**
