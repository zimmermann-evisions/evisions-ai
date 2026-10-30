# CLAUDE.md template

Fill this in for the project at the end of the build. It's the context file Claude and the next human read, and it's what you hand to the AI team. Replace every `<…>` and delete guidance lines. Keep it current as the project changes.

```markdown
# <project-name>

<One or two sentences: what this app/automation does and who it's for.>

- **Department / scope:** <krea / ppc / seo / ux / scs / hr / analytics / agency / client>
- **Owner (builder, responsible for fixes):** <name>
- **Status:** <prototype / in testing / live>
- **Lives at:** <repo URL> · <deployed URL once the AI team deploys it, e.g. your-app.evisions.cz>

## What it does

<Short description of the main features and the problem it solves.>

## Tech stack

<e.g. Next.js + TypeScript + Tailwind + Postgres 17 + Docker + Caddy. Note anything non-standard and why. Keep this consistent with the `stack` and `database` fields in evisions-app.json.>

## How to run locally

```bash
<commands — e.g. npm install, set up .env from .env.example, docker compose up>
```

## Deploy

- **Manifest:** `evisions-app.json` at the repo root is the source of truth for hostname, `hostPort`, database, auth mode, env var names, declared external calls and resource caps. Change it there, not here.
- **How a change ships:** branch `<surname>/<topic>` → PR to `main` → CI green → merge. **The merge is the deploy.** A direct push to `main` deploys nothing.
- **Verify a deploy by opening the hostname**, not by the PR being merged.
- **If a merge seems to do nothing**, check in this order: pushed to `main` instead of opening a PR; merged over red CI (allowed by GitHub, refused by the deploy); no review recorded for that commit; the deploy ran and rolled back, so the old version is still serving; the server was out of disk. The Slack notification and the deploy workflow log distinguish them.

## Environment variables

<List each env var and what it's for. Names only, and they must match the envVars array in evisions-app.json. Real values live in the root-owned .env on the server — never here, never in git.>

## Data & privacy

<Does it touch client data? Which classification (RED/ORANGE/GREEN)? How is sensitive data handled? Auth method.>

## Project structure

<Brief map of the important folders/files so a newcomer can orient.>

## Known issues / TODO

<Anything unfinished, fragile, or planned.>

## Decisions worth remembering

<Non-obvious choices and why — "we used X instead of Y because…". Move bigger ones into docs/adr/ if you run /appdoc.>
```

After filling this in, on a full Claude Code setup run `/appdoc` to scaffold the rest of the documentation (README, runbook, env-reference, security, ADRs), then hand over with `/ai-handover`.
