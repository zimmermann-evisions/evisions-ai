# Setup — when a precondition is missing

Check all four before starting. If something is missing, **walk the person
through fixing it, then continue** — don't just report the problem and stop.
Most people hitting this are setting the pipeline up for the first time.

Check everything first and report all gaps at once. Discovering them one at a
time, with a round trip each, is the annoying version.

## 1. Running in Claude Code

Needs filesystem access to write `tokens.css` and the preview.

If this is running somewhere without it (e.g. the chat), say so plainly —
there is no workaround, the work has to move to Claude Code.

## 2. Figma MCP connected

Test: call `get_variable_defs` on any node. If the tool is unavailable, it is
not connected.

Fix: connect the Figma MCP server in Claude Code's connector settings, then
reload. Authentication happens through Figma on first use.

## 3. Impeccable installed

Test: is there a `.claude/skills/impeccable/` in the project, or `.impeccable/`
at the root?

Fix — from the project root:
```bash
npx impeccable install
```
It detects the harness, asks project-vs-global scope, and installs the skill
plus the design hook. Reload Claude Code afterwards.

## 4. `/impeccable init` has been run

Test: does `PRODUCT.md` exist at the project root?

Fix: run `/impeccable init` in the session. It inspects the project, asks only
about material gaps in durable product truth (audience, purpose, constraints,
voice), and writes `PRODUCT.md`.

**This one is genuinely needed, not a formality.** The handoff commands in
step 5 (`audit`, `normalize`, `polish`) read `PRODUCT.md` for context. Without
it they judge the design against generic assumptions instead of this product.

`init` also asks about `buildPath` (comp-first vs code-first). Either answer is
fine for this pipeline — the design already exists in Figma, so that choice
does not affect this skill.

## 5. A Figma frame link with a node id

Needed: `https://figma.com/design/<fileKey>/<name>?node-id=119-1319`

A link without `node-id` points at the whole file, and there is no sensible
default frame to guess. Ask the person to open the frame in Figma and use
**Copy link to selection** — that always includes the node id.

## After fixing

Re-check, then continue into step 0. Don't ask for permission to proceed
once the preconditions are met — that was the only thing blocking the run.
