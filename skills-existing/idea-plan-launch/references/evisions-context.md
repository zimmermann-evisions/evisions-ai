# What evisions is — context for the mentor (read this first)

> **Orientation file.** This is the entry point for the skill: read it before any other reference. It's background for *you* (the model), not a script to read aloud. The person in front of you already works here — orient yourself, don't lecture them.

## The company

**evisions** is a Czech digital marketing agency. It works for clients across the full marketing stack, organized into departments. Each department has people who may bring AI ideas; the repo-naming scopes map directly to these departments.

| Scope code | Department | What they do |
|---|---|---|
| `seo` | SEO | Organic search — keyword research, content, technical SEO, link building |
| `ppc` | PPC | Paid advertising — Google/Meta ads, campaign management, reporting |
| `ux` | UX | User experience — research, testing, interface and conversion design |
| `krea` | Krea (Creative) | Creative — visual design, content production, brand and campaign assets |
| `analytics` | Analytics | Data and measurement — tracking, dashboards, attribution, insights |
| `scs` | SCS (Client Service) | Client/account management — the bridge between clients and delivery teams |
| `hr` | HR | People — recruitment, internal culture, onboarding |
| `pr` | PR | Public relations and communications |
| `c-level` | C-level | Leadership and executive work |
| `ai` | AI team | The internal AI unit (see below) |
| `client` | — | Client-facing work; the literal word `client` is used so the real client name never leaks into a repo name |

## The AI team and the AI Champion program

evisions runs an internal **AI team** (Niki + Honza, under COO **Petr Hlaváč**, with external consultant Šimon Hradni reviewing technical/infra work). The AI team's job is to push AI adoption across the agency, set policy, and own the shared infrastructure (server, n8n, the AI roadmap).

Most people you mentor through this skill are **AI Champions** — a person in a department who drives AI use for their team and brings build ideas to the AI team. They are usually skilled in their domain (SEO, PPC, etc.) but not necessarily developers, so meet them at their level: concrete, one step at a time, no jargon dumps.

## The toolset (what we work in)

Know these so your advice points to the real tools, not generic ones:

- **Claude (Team plan)** — the agency's AI workspace. People use Claude.ai chat and Claude Code. This skill runs in **Claude Code** (the Code tab in the desktop app, or the terminal), because it needs to read files and run commands. When you mention "build it in Claude Code," this is what you mean.
- **Slack** — internal comms. The `#ai-insights` channel is the agency-wide place for quick AI questions and tips.
- **Teamwork** — project and task management. The AI roadmap lives here as tasks, "Book an AI session" is a Teamwork tasklist, and handovers are filed here. Sensitive inputs (numbers, client data, credentials) get attached **in Teamwork, never in an AI chat**.
- **Google Workspace / Drive** — docs, Sheets, and the shared AI Drive that is the single source of truth (strategy, projects, knowledge, the AI roadmap sheet). Accounts are `@evisions.cz`; app auth is Google OAuth restricted to that domain.
- **GitHub** — where code lives, in two places. Champion apps go in the **`evisions-apps`** Organization (departments are Teams inside it); `ai-evisions` holds the AI team's own dev infrastructure, skills and design system. Repos are private, and merging a PR in `evisions-apps` is what deploys an app.
- **n8n** (`evisions-n8n.evisions.cz`) — the automation platform. Only the AI team has access, but a champion can design and build the flow logic in Claude Code and hand it over to be implemented.
- **Hetzner server + Caddy + Docker** — production. Nobody gets a shell on it, including the deploy job, which runs one fixed command through a locked-down key. Builders never touch it directly.
- **Antigravity 2.0** — quick preview to see something live. A preview, not the build tool and not production. Vercel is not used here.

## How AI work flows here (why this skill exists)

The agency standardizes how AI solutions get built so they're maintainable, on-brand, secure with client data, and don't orphan when someone leaves:

1. **Idea** comes from anyone, usually a Champion.
2. **Plan** — a PRD defines what it must do before any code.
3. **Launch** — built in Claude Code on the agency's standard stack, following the build guardrails (`evisions-essentials.md`).
4. **Handover** — the AI team onboards the app to the deploy pipeline once, after which the builder's own merged pull request is what ships it. Every project is registered on the **AI roadmap** (the single source of truth for what's live, in progress, or planned).

This skill is the front door to that flow. Your role is the AI team's role, in person: friendly, honest about whether something is worth building, and protective of everyone's time and the client-data rules.

## Need more depth?

This file is the offline copy that travels inside the skill, so you always have it. If you (or the person) can reach the evisions Drive, the canonical and fuller source on departments, brand, company profile, and proof is the **`01_AI Context`** folder, which is company-public within evisions:

https://drive.google.com/drive/folders/1KO3m1L224VrguhK1xFBKHo43n0V1Gh8k

Use it when you need more than this summary and have Drive access. Most champions running this skill in Claude Code won't be able to open it from inside the session, so treat the link as a pointer, not a dependency: the context above is what you can always count on.
