---
name: ai-handover
description: Package an AI team output (app, dashboard, workflow, prompt, doc) for handover to a colleague or team for testing and feedback. Produces a ready-to-paste Teamwork task body for the "🗣️ Feedback our AI outputs" tasklist. Use when the user says "create handover", "hand over this project", "prepare feedback task", "předat projekt", "předávka", "handover to [team/person]", "feedback our AI output", or runs /ai-handover. Asks Czech or English first, walks through the 10-item checklist, outputs the formatted block.
---

# ai-handover — prepare a Teamwork handover for a finished AI output

This skill wraps evisions' canonical **AI Handover Prompt**. The goal: turn a finished AI output (an app, workflow, Gem, dashboard, prompt, doc, slide deck — anything the AI team built) into a clean, ready-to-paste task body for the **🗣️ Feedback our AI outputs** tasklist in Teamwork.

The reviewer won't fill in a template; they just read and reply as a comment. So the block must stand on its own.

## When to use

Trigger on natural language like:
- "create a handover", "hand over [X]", "prepare the handover"
- "create a feedback task", "post to Feedback our AI outputs"
- "předat projekt", "předávka", "vytvořit předávku" (Czech)
- The user explicitly invokes `/ai-handover`

Also useful right after an app is shipped (e.g. "vibes is live, let's send it to Hedvika to test"), or when the user mentions a Teamwork task for testing.

## Source of truth

The canonical prompt is a Google Doc that both the Gemini Gem and Claude Project reference — **editing the Doc updates all three surfaces** (Gem, Project, this skill when we sync). Do not embed a diverging version inline.

- **Google Doc**: `https://docs.google.com/document/d/1udL9jOaAtHzMH1wUTkojFxpfuz0pVGELUTefyBnf5JM/edit`
- **Doc ID**: `1udL9jOaAtHzMH1wUTkojFxpfuz0pVGELUTefyBnf5JM`
- **Local snapshot fallback**: `prompt.md` in this skill folder (last synced from the Doc on install).

### Which prompt to use — fallback logic

The skill ships with a bundled snapshot at `prompt.md` that works offline for anyone. **However, whenever possible, prefer the live Google Doc** so Doc edits propagate to this skill the same way they propagate to the Gemini Gem and the Claude Project.

**Resolution order (first hit wins):**

1. **Try the live Google Doc via the authenticated-user token** at `~/.config/google-api/token.json`. This works only if:
   - The user has run Google API auth on this machine before
   - The token has the `documents.readonly` or `documents` scope
   - Network is available

   Attempt:

   ```bash
   test -f ~/.config/google-api/token.json && python3 - <<'PY'
   from google.oauth2.credentials import Credentials
   from googleapiclient.discovery import build
   creds = Credentials.from_authorized_user_file("/Users/$USER/.config/google-api/token.json")
   docs = build("docs", "v1", credentials=creds)
   doc = docs.documents().get(documentId="1udL9jOaAtHzMH1wUTkojFxpfuz0pVGELUTefyBnf5JM").execute()
   out = []
   def walk(e):
       if "paragraph" in e:
           for x in e["paragraph"]["elements"]:
               if "textRun" in x:
                   out.append(x["textRun"]["content"])
       elif "table" in e:
           for row in e["table"]["tableRows"]:
               for cell in row["tableCells"]:
                   for c in cell["content"]:
                       walk(c)
   for e in doc["body"]["content"]:
       walk(e)
   print("".join(out))
   PY
   ```

   If it prints the prompt cleanly → use it. **Do not alert the user** when this succeeds; silent is fine, this is the happy path.

2. **If step 1 fails for ANY reason** (no file, expired token, missing Python deps, no network, API error, non-zero exit code), **silently fall back** to reading the bundled `prompt.md` next to this SKILL.md. **Do not show the user the error** — most team members running this skill won't have Google API creds set up locally, and that's fine. The bundled snapshot is the offline-compatible canonical version.

   ```bash
   cat "$(dirname "$0")/prompt.md"   # or: cat <path-to-this-skill>/prompt.md
   ```

3. **Only warn the user** if BOTH fail (no live fetch AND prompt.md is missing/unreadable). In that case: stop, ask the user to re-install the skill, do not guess the prompt from memory.

**Rule of thumb**: the bundled snapshot IS the canonical prompt for 99% of use. Live fetch is a nice-to-have for the skill author to verify alignment with the Google Doc during development. Either path produces the same Teamwork handover block.

## Execution flow

Whatever prompt source you end up using, follow its instructions **verbatim** — it's the canonical logic.

At a high level, the prompt will have you:

1. **Ask language first**: Czech or English. Use that for every downstream message, including the final block.
2. **Offer input methods**: user describes in their own words, pastes a spec / README, uploads files, or requests a guided Q&A.
3. **Cover the 10-item checklist** (don't re-ask what's already answered):
   1. Reviewer's name (for greeting)
   2. What it is — one-liner
   3. Why it exists — problem solved
   4. Who it's for
   5. Access — file paths, URLs, dashboards (no credentials)
   6. How to use — 2-3 step quickstart
   7. What the reviewer should look at specifically (2-4 points)
   8. Known limitations / out of scope
   9. Feedback deadline
   10. Sender's name (signoff)
4. **Output the formatted block** — only the block, in the chosen language. See `prompt.md` for the exact template.
5. **Remind the user** where to paste it: a new task in the **🗣️ Feedback our AI outputs** tasklist (Teamwork project "AI Insights"). Sensitive inputs (numbers, credentials) go directly in Teamwork, not in the block.

## Hard rules (do NOT relax)

Inherited from the canonical prompt:

- **Client names are OK** in the block.
- **Never accept and never include** in the block:
  - Budgets, revenues, margins, performance numbers
  - Passwords, API keys, account logins, tokens
  - Personal data (employee health info, salaries, HR records beyond role/department)
- If the user volunteers any of the above, tell them to skip it here and attach directly in the Teamwork task.
- If the user uploads a file (spec, README, screenshots, presentation), read it carefully and use it as the primary source before asking follow-up questions.

## After the handover is produced

The destination is a new task in the **🗣️ Feedback our AI outputs** tasklist (Teamwork project "AI Insights", tasklist ID `826590`). Reviewer assignment happens in Teamwork; the reviewer replies as a free-form comment.

### Optional: post directly to Teamwork (opt-in per user)

**Only if the current user has Teamwork API credentials configured locally** does this skill offer to create the task via API. Users without credentials get the "paste manually" path automatically — no setup required, no error, no friction.

#### Detection

Check for credentials in this order (first hit wins):

```bash
# Preferred: personal bearer token
[ -n "$TW_MCP_BEARER_TOKEN" ] && echo "bearer"
# Fallback: basic-auth credentials
[ -n "$TEAMWORK_API_KEY" ] && echo "apikey"
[ -n "$TEAMWORK_USER" ] && [ -n "$TEAMWORK_PASSWORD" ] && echo "basic"
```

If none are set → **skip the question**, go straight to the copy-paste instructions below. Do NOT prompt the user to set them up unless they ask how to enable automation.

#### If credentials are detected

After presenting the formatted block, ask:

> "Want me to post this directly as a new task in Teamwork → 🗣️ Feedback our AI outputs? (yes / no)"

If the user says **yes**:
1. Draft a task **title** matching the output language — suggest `Handover: <what it is>` (EN) or `Předávka: <co je to>` (CZ). Confirm with the user.
2. POST to the Teamwork REST API:

```bash
TITLE="<confirmed title>"
# $BODY = the full handover block

# Pick whichever auth is available
if [ -n "$TW_MCP_BEARER_TOKEN" ]; then
  AUTH=(-H "Authorization: Bearer $TW_MCP_BEARER_TOKEN")
elif [ -n "$TEAMWORK_API_KEY" ]; then
  AUTH=(-u "$TEAMWORK_API_KEY:X")
else
  AUTH=(-u "$TEAMWORK_USER:$TEAMWORK_PASSWORD")
fi

BODY_JSON=$(python3 -c 'import json,sys; print(json.dumps(sys.stdin.read()))' <<< "$BODY")

curl -sS -X POST "https://evisions.teamwork.com/projects/api/v3/tasklists/826590/tasks.json" \
  "${AUTH[@]}" \
  -H "Content-Type: application/json" \
  -d "{\"task\": {\"name\": \"$TITLE\", \"description\": $BODY_JSON}}"
```

3. On success → parse the task ID from the response, report the clickable URL (`https://evisions.teamwork.com/#/tasks/<task-id>`), remind the user to:
   - **Assign the reviewer(s)** in Teamwork
   - **Attach any sensitive inputs** (numbers, credentials) to the task directly — not in the block

4. On failure (non-2xx) → show the HTTP status + error body, and fall back to the copy-paste path.

### Updating an existing task (instead of creating new)

If the user says *"submit to task <id>"* / *"update task <id>"* / *"put this into existing task X"* — do not create a new task. Instead, update task `<id>` in place. Steps:

```bash
AUTH=(-H "Authorization: Bearer $TW_MCP_BEARER_TOKEN")
BASE="https://evisions.teamwork.com/projects/api/v3"

# 1. PATCH the task: name, description, assignees, tags all at once
PAYLOAD=$(python3 - <<PY
import json
# Read body from a temp file or here-doc so special chars don't break JSON
body = open("/tmp/handover-body.md").read()
payload = {"task": {
    "name": "<confirmed title>",
    "description": body,
    "assignees": {"userIds": [<reviewer user id>]},
    "tagIds": [<tag id, e.g. 11436 for 'hr'>]
}}
print(json.dumps(payload))
PY
)
curl -sS -X PATCH "$BASE/tasks/<taskId>.json" "${AUTH[@]}" \
  -H "Content-Type: application/json" -d "$PAYLOAD"

# 2. Move to a workflow stage — this is a SEPARATE call (cannot be done via PATCH).
#    PATCH /tasks/{id}.json silently ignores workflowStages / stageId / columnId fields.
#    The working endpoint is:
curl -sS -X POST "$BASE/workflows/<workflowId>/stages/<stageId>/tasks.json" "${AUTH[@]}" \
  -H "Content-Type: application/json" \
  -d "{\"taskIds\":[<taskId>]}"
# Expect HTTP 204 No Content on success. `{"taskIds":[X]}` is correct — plural + array.
# `{"taskId":X}` (singular) returns 400.
```

#### Discovering the IDs you need

```bash
AUTH=(-H "Authorization: Bearer $TW_MCP_BEARER_TOKEN")
BASE="https://evisions.teamwork.com/projects/api/v3"

# Find a user by name
curl -sS "${AUTH[@]}" "$BASE/people.json?searchTerm=<name>" | jq '.people[] | {id, firstName, lastName, email}'

# Find a tag by name
curl -sS "${AUTH[@]}" "$BASE/tags.json" | jq '.tags[] | select(.name|ascii_downcase|contains("<tag>")) | {id, name}'

# Find the workflow for a tasklist (from an existing task in it)
curl -sS "${AUTH[@]}" "$BASE/tasks/<anyTaskIdInTasklist>.json" | jq '.task.workflowStages'

# List stages in a workflow
curl -sS "${AUTH[@]}" "$BASE/workflows/<workflowId>/stages.json" | jq '.stages[] | {id, name}'
```

#### Known IDs for the 🗣️ Feedback our AI outputs tasklist

Hardcoded in this skill (saves discovery calls for the common case):

- **Tasklist ID**: `826590`
- **Workflow ID**: `9982`
- **Stages**: TESTING = `51223`, DEV = `51224`, FINAL = `51225`
- **"hr" tag ID**: `11436`

Verify these haven't drifted if something looks off.

#### If no credentials are detected

Skip the question. End with:

> "Paste the block into a new task in **🗣️ Feedback our AI outputs** (tasklist 826590 in the AI Insights project). Attach any sensitive inputs there directly.
>
> If you want this skill to post for you in the future, add `TW_MCP_BEARER_TOKEN` (or `TEAMWORK_USER` + `TEAMWORK_PASSWORD`) to `~/.env`. Generate a token at https://evisions.teamwork.com/app/settings/profile/api-tokens."

## Also

- **File attachments**: if the handover block references supporting files (screenshots, exported data), remind the user to attach them in Teamwork under the task.
