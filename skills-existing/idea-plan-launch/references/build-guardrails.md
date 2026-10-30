# Build guardrails — read this while building (for you, the model)

These are the mistakes a non-developer cannot catch, because they don't yet know what "right" looks like. The person can't review your work here, so you have to hold the line yourself. Apply the relevant section silently, don't turn it into a lecture.

## If it's an app

- **Use the evisions stack, nothing else.** Next.js + TypeScript + Tailwind + Docker + Caddy, with Postgres, SQLite or a JSON file for data (full list in `evisions-essentials.md`). Don't reach for a different framework because it's familiar to you. Deviating needs a conversation with the AI team first.
- **Never reach for a hosted database or a hosted deploy target.** Supabase, PlanetScale, Firebase, MongoDB Atlas, Upstash, Neon and Xata are refused by CI, which greps the code and not just the manifest, so an SDK import fails the build. Vercel is not used at evisions; an app that only runs there cannot be deployed. Company data stays on our infrastructure.
- **Don't publish ports in `docker-compose.yml`.** No `ports:`, no `privileged`, no host networking, no host bind mounts, no Docker socket. The port goes in `evisions-app.json` as `hostPort` and the platform publishes it on loopback. CI rejects each of these individually.
- **Write the manifest honestly.** `auth` and `externalCalls` are the two fields someone will rely on. Declaring `auth: "none"` is allowed and gets a warning; quietly declaring an auth mode the app doesn't actually enforce is the failure in section 9 of `evisions-essentials.md`, dressed up as a config field.
- **Don't add dependencies you don't need yet.** Every extra package is one more thing that can break and one more thing the AI team has to trust. If the standard tools already do the job, use them. Don't build features, config, or abstractions nobody has asked for.
- **Secrets go in `.env`, never in code and never in the compose file.** Reference them as `${VAR}`. If a key, password, or token ever lands in a file that gets committed, stop and fix it before going further.
- **One database, one Docker network per app.** Never wire an app into another project's database.
- **Run the build before you ever say it works.** `npm run build` and `npx tsc --noEmit`, and confirm `docker compose up` starts cleanly on a fresh state. "It should work" is not "it works." If the person is taking your word for it, see `beginner-support.md` on verification.
- **Add CI yourself, early, and prove it can fail.** The workflow in section 11 of `evisions-essentials.md` goes in as soon as there's a repo, not at handover. Then break something on purpose once and watch the run go red. The person cannot tell the difference between a check that passes and a check that cannot fail; you can, so it's on you.
- **Never claim a check passed that you did not run.** "Tests pass", "the build is clean", "CI is green" are claims about the world, and the only thing that settles them is the command's actual output. If you couldn't run it, say which one and why, in the same breath. This matters more here than on your own projects: the person has no way to audit you.
- **Auth: check the framework's real semantics, don't pattern-match.** The Auth.js v5 middleware trap in section 9 of `evisions-essentials.md` shipped a public org chart because the code looked exactly like the version that worked. Before you tell anyone an app is protected, request a page AND an `/api/...` route logged out, against a **seeded** database, and read the status codes.
- **Don't invent libraries or APIs.** If you're unsure a package or method exists, check, don't guess. Hallucinated imports are a classic failure the user will not spot.

## If it's an n8n automation

The champion can design the flow and build the node logic in Claude Code; only the AI team has n8n access to run it. So build it correctly before handover.

- **Follow the n8n MCP workflow, don't guess.** Read the SDK reference (`get_sdk_reference`), get suggested nodes, then `search_nodes` and `get_node_types` for the exact parameter names *before* writing workflow code. Guessing node shapes produces invalid workflows.
- **Prefer native / integration nodes over the generic HTTP Request node.** If n8n has a dedicated node for the service (Gmail, Slack, Google Sheets, and so on), use it. It handles auth, pagination, and errors for you. This is the single most common mistake to avoid: reaching for an HTTP Request node when a proper node already exists.
- **Only use HTTP Request or Code nodes when there is genuinely no native node** for what you need. When you do, say so clearly so the AI team knows why.
- **Never hardcode credentials.** Use n8n credentials, not tokens pasted into node fields or code.
- **Validate before handing over.** Run `validate_workflow` and fix everything it flags before declaring it done.
