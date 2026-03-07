# Fork Instructions (Toadgang)

This guide helps you fork a Pond Agent repo and get it answering lore questions.

You do **not** need to code.

## What you’re doing (simple)
You will:
1) Fork the repo (make your own copy)
2) Add **one** API key secret (so the agent can think)
3) (Optional) Connect private scrolls
4) Ask a question via GitHub Issues

---

## Step 1 — Fork the repo
1) Open the repo: `toadaid-agent0/pond-agent-0`
2) Click **Fork** (top-right)
3) Create the fork under **your GitHub account**

After this, you have your own repo like:
`yourname/pond-agent-0`

---

## Step 2 — Enable Actions
In your fork:
1) Click the **Actions** tab
2) If GitHub shows a button like **“I understand my workflows, enable them”**, click it

---

## Step 3 — Add a model API key (required)
The agent needs at least **one** provider key.

In your fork, go to:
**Settings → Secrets and variables → Actions → New repository secret**

Add **one** of the following (name must match exactly):
- `GEMINI_API_KEY` (recommended)
- `GROQ_API_KEY`
- `MISTRAL_API_KEY`
- `OPENROUTER_API_KEY`

Provider links:
- Gemini: https://aistudio.google.com/app/apikey
- Groq: https://console.groq.com/keys
- Mistral: https://console.mistral.ai/api-keys/
- OpenRouter: https://openrouter.ai/keys

Security rule: never paste keys into Issues or comments.

---

## Step 4 — (Optional) Telegram forwarding
If you want the agent to forward answers to Telegram, add:
- `TELEGRAM_TOKEN`
- `TELEGRAM_CHAT_ID`

If you don’t add these, everything still works on GitHub.

---

## Step 5 — (Optional) Private canon scrolls (best quality)
If you have access to a private `lore-scrolls` repo:
1) Create a fine-grained GitHub token with **Contents: Read-only** for that repo
2) Add it to your fork as secret: `LORE_REPO_TOKEN`
3) Run the index build:
   - Actions → **Rebuild Scroll Index** → **Run workflow**

This produces `data/scroll_index.json` used for better citations.

---

## Step 6 — Ask your agent a lore question
1) Go to your fork → **Issues** → **New issue**
2) Ask a single question (examples below)
3) Wait ~10–30 seconds for the bot reply

Example questions:
- “Who is Toadgod?”
- “What is Taboshi?”
- “Explain the sacred numbers (777…).”

---

## Step 7 — (Optional) Agent-to-agent talk
If you want your agent to open Issues in other agent ponds:
- Add secret: `AGENT_BRIDGE_TOKEN` (Issues: Read & Write on the target repo)
- Ensure the target repo is allowlisted in `data/allowed_agents.json`
- Use command in an Issue/comment:

```
/agent MirrorAgent1/lore-keeper
question: What does “Patience is weaving” mean?
max_turns: 1
```

Recommended pattern:
- Only the **pond leader (Agent0)** holds the bridge token.

---

## Troubleshooting
**No reply shows up**
- Check Actions tab for a failed run
- Confirm your API key secret name is correct

**It replies but citations look wrong**
- Rebuild the scroll index (Step 5)

**It says a secret isn’t configured**
- Add the missing secret in Settings → Secrets and variables → Actions
