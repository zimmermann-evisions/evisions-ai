# evisions — design → code workflow

Everything needed for the path from a client brief to a built website:
the skills, the supporting references, and the process documentation.

## Structure

```
skills/            built in this workstream — edit these
skills-existing/   already in the evisions plugin set, copied here for
                   completeness — the canonical versions live in the plugin
packaged/          .skill files ready to upload to Claude
docs/              process documentation
```

> **On `skills-existing/`:** these are a snapshot for reference and for
> reading the workflow end to end in one place. If you edit one, it has to go
> back into the plugin set — otherwise the copies diverge and nobody knows
> which is current.

## The workflow

```
ai-web-design (orchestrator — start here)
  → wireframe-intake
  → evisions-wireframe              🚦 IA sign-off
  → mobbin-pattern-extraction       🚦 per component
  → figma-to-tokens                 🚦 preview approval      [Claude Code]
  → /impeccable extract → audit → normalize → polish
  → tech-spec                       🚦 spec + plans approval [Claude Code, Opus]
  → web-build
  → appdoc → ai-handover
```

Paths 5 (wireframe only), 6 (design only) and 7 (SCS pitch) stop before
`figma-to-tokens` — no build, so no tokens or spec needed.

Separate branch: `idea-plan-launch` when the result is an app rather than a
site. It is also the single source for the evisions build rules (stack, repo,
CI, client data).

## The skills

### Built in this workstream (`skills/`)

| Skill | Does |
|---|---|
| `ai-web-design` | **Orchestrator.** Asks which of 7 paths you're on, checks tools, flags what takes days to arrange, calls the others in order. |
| `wireframe-intake` | Messy client input → structured brief. Asks 2–4 questions at a time, records stakeholder conflicts without resolving them. |
| `evisions-wireframe` | Brief → IA blueprint (approved as text) → wireframe built from the Figma kit. Includes fintech and healthcare segment profiles. |
| `figma-to-tokens` | Approved design → `tokens.css` + static HTML/CSS preview. Creates Figma Variables first if the design has none. |
| `tech-spec` | Approved design + brief → technical specification → small, independently buildable implementation plans. |

### Already in the plugin set (`skills-existing/`)

| Skill | Does |
|---|---|
| `mobbin-pattern-extraction` | Mobbin references → hi-fi design, component by component, without producing a design traceable to one real site |
| `web-build` | Builds the site (static HTML/CSS/JS on client hosting). Cookie bar, GTM/GA4, lead form, meta tags, staging. |
| `appdoc` | Documentation folder for a shipped project, with templates |
| `ai-handover` | Handover to a colleague for testing — output formatted for Teamwork |
| `idea-plan-launch` | Idea → deployed app. **Also the single source of evisions build rules** (`references/evisions-essentials.md`, `build-guardrails.md`). |
| `prd-creator` | PRD when WHAT/WHY needs defining. Goes *before* `tech-spec`, not instead of it. |

### External, not ours
`/impeccable` — frontend design quality (24 commands). In this flow it runs
**after** `figma-to-tokens`: `extract` → `audit` → `normalize` → `polish`.
Note it does **not** extract tokens from Figma; that's the Figma MCP.

## Install

Upload each `.skill` file from `packaged/` in Claude:
`Settings → Skills → Add skill`. One zip per skill — no bulk upload.

For Claude Code, source folders go in `.claude/skills/` (project) or
`~/.claude/skills/` (global).

`figma-to-tokens` and `tech-spec` run in **Claude Code**, not chat — they
need filesystem access. The rest work in either.

### Prerequisites for the Claude Code stage
- Figma MCP connected
- `npx impeccable install` in the project
- `/impeccable init` run once (writes `PRODUCT.md`)

`figma-to-tokens` checks all of these and walks you through anything missing.

## Docs

| File | |
|---|---|
| `evisions-wireframe-workflow-complete.md` | Full documentation of the wireframe system — decisions and rationale, Figma API findings, real project history, open questions. Written for external review. |
| `dev-pipeline-design-to-code.md` | ⚠️ **Outdated — needs rewriting.** Written before two corrections: Impeccable does *not* extract tokens from Figma (that's Figma MCP; `/impeccable extract` consolidates from code afterwards), and simple client sites don't need Docker/Caddy/Next.js (that's only for apps on evisions servers). The skills are correct; this document is not. |
| `example-tokens-nemocnica-bory.css` | Example `figma-to-tokens` output, from the Nemocnica Bory homepage wireframe. |

## Still missing from the pipeline

- **Assets phase** (between design and tokens) — manual checklist, no skill
- **Code review** — none
- **Security review** — none
- **Real end-to-end testing** — none. Running the app and walking through it,
  not just running the test suite. The most valuable gap of the three; they
  may make sense as one skill rather than three.

Also open: the Wireframe Kit has no general spacing scale (only section
paddings), which means gaps come from individual auto-layouts rather than
tokens.
