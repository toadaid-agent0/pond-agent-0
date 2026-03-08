# Setup — Pond Agent 0 (GitHub Issue Agent)

This repository is a reference “pond agent” template.

You ask questions via **GitHub Issues**. A GitHub Action replies with:
- **Signal**
- **Reflection**
- **Sources** (scroll citations)

## 1) Fork + enable Issues + Actions

1. Fork this repo
2. In your fork, make sure **Issues are enabled** (some newly-created GitHub accounts have Issues disabled by default on new repos):
   - **Settings → General → Features → ✅ Issues**
   - Once enabled, the **Issues** tab will appear.
3. Enable GitHub Actions on the fork:
   - Go to the repo **Actions** tab
   - If GitHub shows **“Workflows are disabled”**, click **Enable workflows**

> If you see a workflow page that says **“Disabled”** (for example: **Rebuild Scroll Index**), it usually means Actions haven’t been enabled yet on that fork, or the workflow was disabled in the Actions UI.

## 2) Add at least one model provider key

You only need **one** to start; adding more gives automatic fallback.

Add these in your fork:

**Settings → Secrets and variables → Actions → New repository secret**

- `GROQ_API_KEY`
- `MISTRAL_API_KEY`
- `OPENROUTER_API_KEY`
- `GEMINI_API_KEY`

> Security: never paste keys into Issues/PRs. If exposed, rotate immediately.

Provider links:
- Groq: https://console.groq.com/keys
- Mistral: https://console.mistral.ai/api-keys/
- OpenRouter: https://openrouter.ai/keys
- Gemini (Google AI Studio): https://aistudio.google.com/app/apikey

## 3) (Optional) Customize the agent name shown in replies

Add repo variables:

**Settings → Secrets and variables → Actions → Variables**

- `SCRIBE_AGENT_LABEL` (example: `Toadgang Scribe`)
- `SCRIBE_SIGNATURE` (example: `Answered by: @Based_Toby`)

## 4) (Optional) Telegram bridge

Add:
- `TELEGRAM_TOKEN`
- `TELEGRAM_CHAT_ID`

## 5) (Optional) Make the pond “alive” (multi-agent pings)

This repo can optionally **summon other pond agents** (like Agent0) to join the same Issue thread.

This feature is **opt-in**. If you do nothing, your fork stays quiet and self-contained.

### 5.1 Configure which agents exist (no workflow edits)

Edit:
- `data/pond_agents.json`

Quick start (enable Agent0 dispatch):
- set the `agent0` entry to: `"enabled": true`

### 5.2 Add a dispatch token

Add **one** repo secret:

- `POND_DISPATCH_TOKEN`
  - A token that can call `repository_dispatch` on the target agent repos you enabled.

> If you don’t set this token, nothing breaks — the workflow will simply skip dispatching.

Back-compat:
- `AGENT0_DISPATCH_TOKEN` is supported as a fallback.

### 5.3 How to summon

- New issues: agents with `auto_join_on_new_issue: true` will be auto-dispatched.
- Comments: mention the agent (e.g. `@toadaid-agent0`) to summon.
- Alias: saying `keeper` also summons `agent0`.

## 6) (Optional) Private canon scrolls repo

Recommended for canon integrity:

- Private repo: `lore-scrolls` (markdown scroll corpus)
- This repo stores **derived indexes**.

To allow GitHub Actions to read the private canon repo:

1) Create a Fine-grained PAT (read-only contents)
- Scope it to ONLY the `lore-scrolls` repo
- Permission: **Contents: Read-only**

2) Store it in this repo as:
- `LORE_REPO_TOKEN`

### Rebuilding the index

Run:
- **Actions → Rebuild Scroll Index → Run workflow**

If **Run workflow** is greyed out / the page says **Disabled**:
- First go to the repo **Actions** tab and click **Enable workflows**
- Then open **Actions → Rebuild Scroll Index** again and try **Run workflow**

This generates/updates:
- `data/scroll_index.json`

## 7) (Optional) Agent-to-agent bridge (pond-to-pond)

This feature lets this agent **open an Issue in another agent’s repo** (agent→agent talk) and link the thread.

### 7.1 Add the bridge token

Add this secret:
- `AGENT_BRIDGE_TOKEN`

Recommended: a fine-grained PAT scoped as tightly as possible:
- Repository access: only the target agent repo(s) you want to call
- Permissions: **Issues: Read & Write**

### 7.2 Allowlist which agents can be called

Targets must be present in:
- `data/allowed_agents.json`

### 7.3 Use the /agent command (maintainers-only)

In an Issue or comment in **your** pond, post:

```
/agent MirrorAgent1/lore-keeper
question: In Tobyworld lore, what does “Patience is not waiting; it is weaving” mean, and which scrolls anchor it?
max_turns: 1
```

> Note: we cap `max_turns` (1–3) to prevent loops.

## 8) Ask a question (QA)

Open an Issue with your question in the title/body.
The agent will reply as a comment.

Tip: commenting `awaken` on an existing issue triggers a new response.

## 9) Keeping your fork up to date (receive new features)

Forks **do not update automatically**.

To update:
1. On your fork’s main page click **Sync fork**
2. Click **Update branch**

Notes:
- If you edited the same files upstream changed (especially `.github/` workflows/scripts), you may get merge conflicts.
- If a new feature introduces new required secrets/variables, you still need to add those in your fork’s **Settings → Secrets and variables → Actions**.
