---
name: appdoc
description: Scaffold a standard app-documentation folder for a shipped project. Use when the user says "create app doc", "document the application", "document this app", "app is ready for testing", "generate documentation", "set up the docs folder", "bootstrap docs", or runs /appdoc. Detects the project's tech stack, checks for existing docs under any naming, creates only what's missing (never overwrites), populates with project-specific content, and reminds the user to publish to the evisions GitHub org without secrets.
---

# appdoc — scaffold standardized app documentation

This skill creates a consistent documentation structure for any app we ship. It reads the current project to produce real, project-specific content rather than empty templates.

## When to run

- User asks to create, generate, or scaffold app documentation.
- An app we built together is live / ready for testing.
- User explicitly invokes `/appdoc`.
- The project is missing standard docs (no `README.md`, no `CLAUDE.md`, no `docs/` folder).

**Do NOT run** for personal / throwaway scripts or when the user is in the middle of something unrelated. If unsure, ask first.

## Core principles

1. **Never overwrite existing files.** Always skip if a doc already exists. Link to it from new docs instead.
2. **Propose before writing.** Show the user the full plan and wait for approval before creating files.
3. **Generate real content, not placeholders.** Scan the project and draft project-specific content. TODOs are fine for things we genuinely don't know; avoid them for things we can derive.
4. **No secrets, ever.** Before reminding the user to publish to GitHub, check the repo (staged files + git log if available) for secret patterns. Refuse to proceed with the publish reminder if secrets are found — surface them so the user can rotate + purge history.
5. **Docs are English by default.** If the team prefers another language, the user will say so in the invocation.

## Step-by-step flow

### Phase 1 — Scan the project

Determine the project root (usually cwd, but walk up if we're in a subfolder and find a `package.json` / `pyproject.toml` / `go.mod` / `Cargo.toml` / `Gemfile` / `pom.xml` / `build.gradle` etc. parent).

Detect the stack. Look for markers:

| Language / stack | Marker files |
|---|---|
| Node / TypeScript | `package.json`, `tsconfig.json`, `next.config.*`, `remix.config.*`, `vite.config.*` |
| Python | `pyproject.toml`, `requirements.txt`, `setup.py`, `Pipfile`, `manage.py` (Django), `pyproject.toml` with `[tool.poetry]` |
| Go | `go.mod` |
| Ruby | `Gemfile`, `config/application.rb` (Rails) |
| Rust | `Cargo.toml` |
| Java / Kotlin | `pom.xml`, `build.gradle`, `build.gradle.kts` |
| PHP | `composer.json`, `artisan` (Laravel) |
| .NET / C# | `*.csproj`, `*.sln` |

Detect project characteristics:

- **Has a web UI?** (indicates user-guide.md is warranted): `src/app/` or `pages/` (Next.js), `templates/` + views (Django/Rails), `public/index.html` with JS framework, `components/`, React Native, etc.
- **Has sensitive/auth data?** (indicates security.md is warranted): Prisma models with `password`/`email`/`token`/`auth`/`session`, OAuth libraries in deps, `users` table in schema, encryption libraries, any "anonymity" / "anonymous" / "gdpr" / "privacy" / "consent" string anywhere in schema or code.
- **Containerized?** `Dockerfile`, `docker-compose.yml` → runbook will have Docker-based commands.
- **Deploy hints?** Look for `.github/workflows/`, `Caddyfile`, Vercel/Netlify config, fly.toml, render.yaml.

Find existing documentation under common names (some projects call them different things):

- Doc folders: `docs/`, `documentation/`, `doc/`, `app-docs/`, `app-documentation/`, `wiki/`
- Dev context: `README.md`, `README.rst`, `README.txt`, `CLAUDE.md`, `AGENTS.md`, `.cursor/rules/*`, `.github/copilot-instructions.md`
- Runbook: `runbook.md`, `RUNBOOK.md`, `operations.md`, `ops.md`, `ops/`, `deploy.md`, `OPERATIONS.md`
- Env ref: `env-reference.md`, `.env.example`, `ENV.md`, `docs/env.md`, `config.md`
- User guide: `user-guide.md`, `USAGE.md`, `guide.md`, `docs/usage.md`, `docs/guide.md`, `HOWTO.md`
- Security: `security.md`, `SECURITY.md`, `privacy.md`, `PRIVACY.md`, `docs/security.md`
- ADRs: `docs/adr/`, `adr/`, `decisions/`, `docs/decisions/`, `docs/architecture/decisions/`

Record what exists vs what's missing. Note the **full path** of what exists so we can link to it.

### Phase 2 — Propose plan

Present to the user a summary like:

```
Detected: Next.js + TypeScript + Prisma + Postgres, containerized, has web UI, has auth tables.

I found existing docs:
  ✓ README.md (root) — will update to point to new sibling docs
  ✓ CLAUDE.md (root) — keeping as-is (use /cmd to refresh it separately)
  ✓ docs/adr/ with 4 ADRs — keeping, adding to new index

I would create these (skip if any already exist under a different name):
  docs/runbook.md
  docs/env-reference.md
  docs/user-guide.md       (because the project has a web UI)
  docs/security.md         (because the project has auth/PII concerns)
  .github/PULL_REQUEST_TEMPLATE.md

Proceed? (yes / adjust / no)
```

Wait for approval. If the user says adjust, ask what to change.

### Phase 3 — Generate

For each file to create, use the corresponding template from `templates/` as a structural guide, then populate sections with real project-specific content by:

- Reading `package.json` / equivalent for tech stack, scripts, deps
- Reading the compose file for containers, ports, volumes, env vars
- Greping the codebase for `process.env.*` / `os.environ[*]` / `System.getenv` to populate `env-reference.md`
- Reading the schema for data model content in `security.md`
- Reading existing `CLAUDE.md` (if present) to avoid duplicating content

**Templates are in `templates/` relative to this SKILL.md.** They contain placeholder sections marked with `{{...}}` — substitute these based on the project scan. Sections that need human input (business-specific stuff like SLAs, on-call names) can stay as `TODO:` lines with instructions.

**Interlinking rule**: every new file's "See also" footer lists the sibling docs with relative links. If existing docs were found under different names, link to their actual paths. The README at root should have a "Documentation" section linking to all of them.

### Phase 4 — Pre-publish safety check

Before closing, run a secret-leak scan on the project:

```bash
# Rough grep list — tune for actual project files
grep -rn -E "(api[_-]?key|secret|password|token|private[_-]?key|bearer)[[:space:]]*[:=][[:space:]]*['\"][^'\"]{16,}" \
  --include="*.md" --include="*.ts" --include="*.tsx" --include="*.js" --include="*.py" --include="*.env*" \
  --exclude-dir=node_modules --exclude-dir=.next --exclude-dir=.git \
  . 2>/dev/null
```

Also check `.gitignore` covers:
- `.env`, `.env.*` (except `.env.example`)
- `*-sa.json`, `*.key`, `*.pem`
- `node_modules/`, `.next/`, `__pycache__/`, build artifacts

If anything looks suspicious, list it and tell the user to rotate + purge before publishing.

### Phase 5 — Closing checklist

Print:

```
✓ Generated docs. Review them — they're drafts, fill in remaining TODOs.
✓ Commit: `git add README.md docs/ .github/ CLAUDE.md && git commit -m "docs: scaffold app documentation"`

Before publishing:
✓ Confirm no secrets in staged files (skill checked — clean / ⚠ found N issues).
✓ Confirm `.gitignore` covers .env, SA keys, private keys, rclone.conf.

Repo ownership + access at evisions:

  Mandatory — at least ONE of these must be true before the repo is
  considered "shipped":

    (A) Repo owned by the personal GitHub account of the builder, with
        `ai-evisions` added as a collaborator (read or write). This
        ensures continuity if the builder ever leaves the company.

        # CLI to invite ai-evisions as collaborator:
        gh api -X PUT /repos/<user>/<repo>/collaborators/ai-evisions \
          -f permission=push      # or "pull" for read-only
        # ai-evisions must then accept the invitation via
        # https://github.com/<user>/<repo>/invitations (logged in as
        # ai-evisions) or via email.

    (B) Repo transferred to `ai-evisions` (encouraged — fully centralized
        ownership; survives personnel changes by default).

        gh api -X POST /repos/<user>/<repo>/transfer \
          -f new_owner=ai-evisions \
          -f new_name=<scope>-<app-name>
        # ai-evisions must accept the invitation from ai@evisions.cz.

  If neither is done, the skill's closing checklist MUST NOT mark the
  repo as ready. Surface this clearly and ask the user to pick.

  Naming (applies to both paths — on transfer, rename to match):

      <scope>-<descriptor>

      scope      = department code (hr, scs, ppc, seo, krea, ux, analytics,
                   pr, c-level, ai), the literal word "client" (never the
                   actual client name), or "shared" for cross-team work.
      descriptor = product / topic name WITHOUT an "evisions-" prefix.

    Examples:
      ai-evisions/hr-vibes
      ai-evisions/scs-sales-dashboard
      ai-evisions/client-product-catalog
      nikoldlab/hr-vibes       # OK if ai-evisions is a collaborator

Rule: no keys or secrets on GitHub, ever. If any history contains secrets,
rotate the secret first, then rewrite history with `git filter-repo` or BFG
before publishing.
```

## Generated file specifications

### `README.md` (root, created if missing)
1-page intro. Pulls from `package.json` description if present. Sections:
- Project name + 1-line purpose
- Live URL (from CLAUDE.md or `.env.example` NEXTAUTH_URL if present; else "TBD")
- What it does (bullet points derived from API routes or main feature pages)
- Stack (derived from deps)
- Running locally (copy from `package.json` scripts + docker-compose if present)
- Documentation section — links to `CLAUDE.md`, `docs/runbook.md`, `docs/user-guide.md`, `docs/security.md`, `docs/adr/`

### `CLAUDE.md` (root, created ONLY if missing)
If present, skip and tell user to run `/cmd` to refresh it. If absent, use template — generate a full version based on project scan. Important: **CLAUDE.md for Claude Code consumption** should follow the evisions-vibes CLAUDE.md structure as a reference (the user has a well-built example at `~/Library/.../evisions-vibes/CLAUDE.md`).

CLAUDE.md must include a short "Local dev — onboarding checklist" sub-section with: accounts/access needed, local prereqs, first-time setup commands, common pitfalls.

### `docs/runbook.md`
Operator-focused: "what to do when X breaks". Sections:
- Health checks (how to tell the app is up)
- Common failures (db connection, OAuth callback, SMTP, etc.) and fixes
- Backup + restore procedure
- Log locations
- Escalation: who to call (leave TODO for names)

### `docs/env-reference.md`
One row per env var used by the project. Scan for `process.env.*`, `os.environ[*]`, etc. Columns: name | required? | dev default | prod source | what it does.

### `docs/user-guide.md` (conditional)
Only if web UI detected. Non-technical. One section per major page/feature. Start each section with "As an admin/user/whoever, I want to X → navigate to Y, click Z". Leave screenshot placeholders as TODOs.

### `docs/security.md` (conditional)
Only if auth/sensitive data detected. Sections:
- **Data handled** (what PII does this app touch)
- **Access control** (who can see what; roles)
- **Authentication** (OAuth provider, session model)
- **Data retention** (how long responses/logs are kept — TODO if not coded)
- **Anonymity / privacy guarantees** (if applicable — explain the invariant)
- **GDPR considerations** (for EU-employee data): right to deletion, data portability, consent basis (legitimate interest / contract / consent), data processor vs controller. Leave TODOs for legal review.
- **Secrets management** (where keys live, rotation cadence)
- **Incident response** (who to alert, where logs are)

### `docs/adr/README.md` + `docs/adr/0001-example.md`
Only if `docs/adr/` (or any ADR-like folder) doesn't exist. README explains ADR format. 0001 is a filled-in example from the actual project (pick one real decision from the CLAUDE.md's "design decisions" section if present).

### `.github/PULL_REQUEST_TEMPLATE.md`
Generic template with sections: Summary / Test plan / Screenshots (if UI) / Checklist.

### ISSUE_TEMPLATE — intentionally NOT generated
Two issue templates (bug / feature) force GitHub to show a picker on every new issue — friction for small teams. Free-form issues work fine until a project grows enough to need triage structure. If later needed, add `.github/ISSUE_TEMPLATE.md` (legacy single file, no picker) or a `.github/ISSUE_TEMPLATE/` folder.

### CODEOWNERS — intentionally NOT generated
Skipping `.github/CODEOWNERS` by default — it's only useful when the team has ≥3 contributors AND branch protection rules require owner approval. For small/internal projects it adds noise (GitHub logs "unknown owner" warnings for TODOs). If the project later grows and needs it, add manually.

## Notes for Claude running this skill

- The `templates/` folder next to this SKILL.md contains scaffolding files. Read them to understand the structure; adapt content to the actual project.
- Always use the Write tool (not Edit) for new files since they don't exist yet.
- If the project has no git repo, note that in the closing checklist and skip the "commit" step.
- If the project has an `AGENTS.md` or `.cursor/rules/` already, treat it like an existing CLAUDE.md: note it, don't duplicate.
- The skill should leave the project in a state where `git status` shows only new files and (optionally) an updated README — never silent modifications elsewhere.
