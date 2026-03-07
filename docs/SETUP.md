# Setup — Lore Keeper (GitHub Issue Agent)

This repository is a reference “pond agent” template.

You ask questions via **GitHub Issues**. A GitHub Action runs the Cave Scribe and replies with:
- **Signal**
- **Reflection**
- **Sources** (scroll citations)

## 1) Fork + enable Actions

1. Fork this repo
2. In your fork: **Actions → Enable workflows** (if GitHub prompts)

## 2) Add at least one model provider key

The Cave Scribe supports multiple providers to survive free-tier rate limits.
You only need **one** to start; adding more gives automatic fallback.

### Secret names (exact)

Add these in your fork:

**Settings → Secrets and variables → Actions → New repository secret**

- `GROQ_API_KEY`
- `MISTRAL_API_KEY`
- `OPENROUTER_API_KEY`
- `GEMINI_API_KEY`

> Security: never paste keys into Issues/PRs. If exposed, rotate immediately.

### Provider links (create free/starter keys)

- Groq: https://console.groq.com/keys
- Mistral: https://console.mistral.ai/api-keys/
- OpenRouter: https://openrouter.ai/keys
- Gemini (Google AI Studio): https://aistudio.google.com/app/apikey

## 3) (Optional) Telegram bridge

If you want the agent to forward answers to Telegram, add:

- `TELEGRAM_TOKEN`
- `TELEGRAM_CHAT_ID`

## 4) (Optional) Private canon scrolls repo

Recommended for canon integrity:

- Private repo: `lore-scrolls` (markdown scroll corpus)
- This repo (`lore-keeper`) stores **derived indexes**.

To allow GitHub Actions to read the private canon repo:

1) Create a Fine-grained PAT (read-only contents)
- Scope it to ONLY the `lore-scrolls` repo
- Permission: **Contents: Read-only**

2) Store it in this repo as:
- `LORE_REPO_TOKEN`

### Rebuilding the index

Run:
- **Actions → Rebuild Scroll Index → Run workflow**

This generates/updates:
- `data/scroll_index.json`

## 5) (Optional) Agent-to-agent bridge (pond-to-pond)

This feature lets an agent **open an Issue in another agent’s repo** (agent→agent talk) and link the thread.

### 5.1 Add the bridge token

Add this secret:

- `AGENT_BRIDGE_TOKEN`

Recommended: a fine-grained PAT scoped as tightly as possible:
- Repository access: only the target agent repo(s) you want to call
- Permissions: **Issues: Read & Write**
- Avoid `Contents: write`

### 5.2 Allowlist which agents can be called

Targets must be present in:
- `data/allowed_agents.json`

Example entry:

```json
[
  { "repo": "MirrorAgent1/lore-keeper", "role": "open" }
]
```

### 5.3 Use the /agent command (maintainers-only)

In an Issue or comment in **your** pond, post:

```
/agent MirrorAgent1/lore-keeper
question: In Tobyworld lore, what does “Patience is not waiting; it is weaving” mean, and which scrolls anchor it?
max_turns: 1
```

What happens:
- Agent 0 opens a new Issue in the target repo
- It posts a link back so the conversation is auditable

> Note: we intentionally cap `max_turns` (1–3) to prevent infinite loops.

### 5.4 “Agent 0 as pond leader” vs “everyone can bridge”

**Default pattern (recommended):**
- **Agent 0 (pond leader)** holds `AGENT_BRIDGE_TOKEN` and initiates pond-to-pond calls.
- Other pond agents can stay simpler/cheaper and just answer questions.

**If you want every agent to bridge:**
- Copy this same bridge setup into their repos too (same `AGENT_BRIDGE_TOKEN` rules + allowlist).

## 6) Ask a question (QA)

Open an Issue in the repo with your question in the title/body.
The Cave Scribe will reply as a comment.

Tip: commenting `awaken` on an existing issue triggers a new response.

---

## Mirror Runtime integration (future)

Mirror Runtime is built using OpenClaw to form a custom runtime.
When ready, this GitHub Issue agent becomes one surface of the larger pond:
- shared canon (`lore-scrolls`)
- shared index schema + citations
- multiple interfaces (GitHub / Telegram / web / local runtime)
