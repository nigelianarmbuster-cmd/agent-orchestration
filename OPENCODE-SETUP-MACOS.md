# OpenCode Setup Guide (macOS)

Welcome. This guide walks you through setting up OpenCode on your Mac, step by step. OpenCode is a free, open-source program that runs in your Terminal and gives you a team of AI assistants that can read your files, write code, and run commands on your computer. This guide assumes you have never used an AI coding tool before. Follow the steps in order, and copy-paste commands exactly.

## A few words you will see

- **Terminal** — a built-in Mac app where you type text commands instead of clicking buttons. To open it: press Command+Space, type "Terminal", press Return.
- **API key** — a secret password that lets a program use your account (and spend your balance). Treat it like a password.
- **Environment variable (env var)** — a small setting stored on your computer that programs can read, so secrets do not have to be written into files.
- **Model** — the actual AI "brain" that does the thinking. Different companies make different models; each has a name like `deepseek/deepseek-v4-pro`.
- **API vs subscription** — a subscription (like ChatGPT Plus or Claude Pro) lets you chat inside a company's own app. API access is a separate pay-as-you-go balance that other programs, like OpenCode, can use. A subscription does not include API access.

## 1. What you're getting

The setup gives you a team of about 43 AI agents, arranged in tiers. The main agent (the "supervisor") plans the work and hands pieces to specialist agents.

| What | Model / provider | What it's for |
|---|---|---|
| Main assistant and "supervisor" | deepseek/deepseek-v4-pro (DeepSeek) | The default brain — plans work and delegates |
| Junior agents | deepseek/deepseek-v4-pro (DeepSeek) | Small, focused tasks |
| Mid-tier agents | anthropic/claude-sonnet-5 (Anthropic) | Everyday work |
| Senior agents | anthropic/claude-opus-4-8 (Anthropic) | The hardest, most careful work |
| Gemini agents | google/gemini-2.5-flash and google/gemini-3.1-pro-preview (Google) | Web research, long documents, images |
| Observer | google/gemini-3.5-flash (Google) | Reads screenshots you paste into the chat |
| Local models | Ollama (runs on your Mac) | Free and private — optional extra |

A few things to know:

- The default model is `deepseek/deepseek-v4-pro` and the default agent is `supervisor`. You do not configure any of this yourself — the files you copy in Step 2 handle it.
- The **observer-bridge plugin**: when you paste a screenshot into the chat, it saves the image and automatically calls the observer agent to describe it. This needs a Google Gemini key (Step 3) — the free tier is enough.
- **Add-on tools (MCP servers)** — an MCP server is an optional add-on that gives OpenCode extra tools. Most are switched off in this config (playwright, chrome-devtools, elevenlabs, yt-dlp, vercel, gemini-api-docs, context7, github, macos-use) — you can ignore them. One, called "railway", is on by default, but it only does anything if you install the Railway CLI and log in (optional, Step 8). The macos-use one is Mac-only and switched off anyway.

## 2. What you'll need before you start

- Your Mac and about 30–45 minutes.
- **Node.js** (free) from https://nodejs.org — click the "LTS" button and run the installer. You need it even if you install OpenCode another way, because Step 2 uses it.
- **Three accounts**: DeepSeek (required), Anthropic (required), Google (required only for the screenshot feature and Gemini agents).
- **Money on prepaid balances**: DeepSeek and Anthropic are pay-as-you-go — you add money before use. Amounts change; verify in the console when you sign up. Google has a free tier.
- Optional: an xAI account (only if you want Grok models), Ollama (free, no account), a Railway account (only for the Railway add-on).
- Nothing else — the configuration files are downloaded from a public GitHub page in Step 2. No GitHub account needed.
- On a budget? Fund DeepSeek only — see the [Budget path](#budget-path) section at the end of this guide.

## 3. Step 1 — Install OpenCode

Pick one option. Option A is easiest.

**Option A — recommended, via Node.js.** Install Node.js from https://nodejs.org (LTS), then in Terminal:

```
npm install -g opencode-ai
```

**Option B — if you have Homebrew** (a popular Mac package manager; check with `brew --version`):

```
brew install anomalyco/tap/opencode
```

If you get `brew: command not found`, either install Homebrew from https://brew.sh or use Option A.

**Option C — official one-line installer:**

```
curl -fsSL https://opencode.ai/install | bash
```

Whichever you chose, verify:

```
opencode --version
```

It should print a version number. If it says "command not found", close Terminal, open a new window, and try again.

Note: Options B and C install OpenCode but not Node.js. You still need Node.js for Step 2 — install it from https://nodejs.org if you have not already.

## 4. Step 2 — Download and install the configuration files

The setup is a folder of configuration files, published at https://github.com/nigelianarmbuster-cmd/opencode-config. It contains these seven items:

- `opencode.json`
- `opencode.jsonc`
- `AGENTS.md`
- `agents/` (a folder containing about 43 agent files ending in .md)
- `observer-bridge.js`
- `package.json`
- `package-lock.json`

It does not contain a `node_modules` folder — that is on purpose; you will generate it yourself in 4c.

**Option A — easy: download the ZIP (no git needed).**
1. Open https://github.com/nigelianarmbuster-cmd/opencode-config in your browser.
2. Click the green **Code** button → **Download ZIP**.
3. Find the downloaded file (usually in `~/Downloads`), double-click it to unzip. You get a folder named `opencode-config-main`.

Then follow **4a–4c** below.

**Option B — with git.** If `git --version` works in Terminal, this one-liner downloads the files and creates the config folder for you. Then skip straight to **4c**:

```
git clone https://github.com/nigelianarmbuster-cmd/opencode-config.git ~/.config/opencode
```

If `git: command not found`, just use Option A — you do not need git. If you get "destination path already exists", the config folder is not empty — either use Option A and let the files merge, or ask the person who shared this guide.

**4a. Create the destination folder (Option A only — the clone already created it).** In Terminal:

```
mkdir -p ~/.config/opencode
```

The `~` means your home folder, so this creates `/Users/<your-name>/.config/opencode`. The `-p` flag means "create it quietly, and do not complain if it already exists". Folders starting with a dot are hidden in normal Finder view — that is normal.

**4b. Copy the seven items in (Option A only).** In Finder, press Command+Shift+G, type `~/.config/opencode`, press Return — the folder opens. Open the unzipped `opencode-config-main` folder and drag its contents — the seven items listed above, not the outer folder itself — into it.

**4c. Install the plugin (both options).** Back in Terminal:

```
cd ~/.config/opencode
npm install
```

This installs `@opencode-ai/plugin`, a helper package the config depends on, and creates a `node_modules` folder inside — that is expected. You will not touch this folder again.

## 5. Step 3 — Create your accounts and add funds

> **Budget path?** Fund DeepSeek only and skip the accounts you don't want — the supervisor and all junior agents run entirely on DeepSeek. Full instructions in the [Budget path](#budget-path) section at the end of this guide.

### DeepSeek (required)

DeepSeek powers the main assistant, the supervisor, and all the junior agents.

1. Create an account at https://platform.deepseek.com.
2. Add money to your prepaid balance. DeepSeek is pay-as-you-go and has no documented free tier. Payment methods and minimum top-up amounts are only shown inside the console — amounts change, so verify there when you sign up.
3. Create your API key: go to https://platform.deepseek.com/api_keys and click "Create API key". Copy the key and store it somewhere safe (a password manager or a private note — never paste it into a chat).
4. You will connect it in Step 4. OpenCode refers to this key as `DEEPSEEK_API_KEY`.

### Anthropic Claude (required)

Anthropic's Claude powers the mid-tier and senior agents.

1. Go to https://platform.claude.com (the older console.anthropic.com address now redirects there).
2. Sign up with Google or an email address.
3. Add money: Anthropic uses prepaid "usage credits". You must buy credits before you can use the API. In the console: Billing → Buy credits. New users get a small amount of free credits to test with. Credits expire after one year and are non-refundable.
4. Create your API key: https://platform.claude.com/settings/keys → "Create key". Choose the longest expiration (up to "Never") so you do not have to redo this.
5. Important: the console also offers a "Claude Pro/Max" sign-in. That is a subscription for chatting in Anthropic's own apps. Anthropic's rules prohibit using a Pro/Max subscription through tools like OpenCode — use the API key route only.
6. In OpenCode you will connect it via `/connect` → Anthropic → "Manually enter API Key" (Step 4). The env var name is `ANTHROPIC_API_KEY`.

### Google Gemini (required for the screenshot feature and Gemini agents)

1. Go to https://aistudio.google.com/apikey, sign in with any Google account, accept the terms, and click "Create API key".
2. There is a free tier (its limits change — check in AI Studio).
3. Paid tier: set up billing inside AI Studio. New users default to a prepay plan (the minimum is around $10 — verify at purchase, prices and minimums change).
4. OpenCode has no login flow for Gemini. Instead you set the key as an environment variable called `GEMINI_API_KEY` (OpenCode also accepts `GOOGLE_API_KEY` or `GOOGLE_GENERATIVE_AI_API_KEY`). The easiest way is a `.env` file — covered in Step 4.

### xAI Grok (optional)

Only needed if you want to use Grok models — nothing in this setup requires it.

1. Console at https://console.x.ai → API keys page → create a key.
2. Grok uses prepaid credits with auto top-up (the minimum is around $25 — verify at purchase).
3. Env var: `XAI_API_KEY`.

## 6. Step 4 — Connect your API keys to OpenCode

OpenCode stores keys in a small credentials file on your computer. The `/connect` command is typed inside OpenCode's chat screen, and it walks you through pasting a key. You do not need `pbcopy` or any clipboard tricks — just paste.

**DeepSeek.** Start `opencode`, type `/connect`, search for "DeepSeek", and paste your key. DeepSeek has no browser-login option — the key paste is the way.

**Anthropic.** In the same `/connect` screen, pick Anthropic and choose "Manually enter API Key", then paste. Do not pick the Pro/Max option (see the warning in Step 3).

**Gemini (via a `.env` file).** OpenCode reads `.env` files from your project folder — a plain text file of `KEY=value` lines. Create one in Terminal:

```
mkdir ~/ai-test
cd ~/ai-test
echo 'GEMINI_API_KEY=PASTE_YOUR_KEY_HERE' > .env
```

Replace `PASTE_YOUR_KEY_HERE` with your actual Gemini key (no spaces, no quotes around the key itself). The file must be named exactly `.env` — the leading dot is part of the name. If you want Grok too, add a second line using `>>` (append) instead of `>` (overwrite):

```
echo 'XAI_API_KEY=PASTE_YOUR_KEY_HERE' >> .env
```

The `.env` file must sit in the folder where you run OpenCode (your project folder). Each project can have its own.

**Check your connections** at any time:

```
opencode auth list
```

This shows which providers are connected. `opencode auth logout` removes one.

## 7. Step 5 — Try it out

In Terminal:

```
mkdir ~/ai-test
cd ~/ai-test
opencode
```

If you already created `~/ai-test` in Step 4, the first line just prints "File exists" — harmless.

- A chat screen opens.
- Type `/models` — you should see the model list. Pick `deepseek/deepseek-v4-pro`.
- Type `/agents` — you should see the agent team (about 43 agents).
- Ask anything simple, like "What is 2+2?" — a normal reply means everything is wired up.
- To leave, press Ctrl+C (twice if it is busy).

## 8. Optional extras

### Ollama — free local models (no account, no key)

Ollama runs AI models on your own Mac — free, private, and offline. It is a good way to test the setup before paying for anything. Which model fits your machine depends on its memory and graphics card, so this setup does not assume everyone has the same hardware.

1. Download from https://ollama.com/download and install like any Mac app.
2. Let the setup pick the right model for your machine. In the OpenCode chat, ask: **"Set up local models that fit my machine."** The supervisor agent will check your Mac's memory, graphics card, and free disk space, then download a model that fits — a small one on modest hardware, a larger one only if your machine can run it comfortably. It downloads **one model by default** — plenty to try things out. If you later want a bigger model, just ask for it in the chat.
3. The config already includes the Ollama provider (it points at `http://localhost:11434/v1`), so pulled models appear in `/models` automatically. Local models are slower and less capable than the cloud ones — that is the trade-off.

### Railway add-on (optional)

The config includes an optional Railway tool (it runs `railway mcp`). It is on by default but does nothing unless you install the Railway CLI and run `railway login`. If you do not use Railway, just ignore it.

### GitHub integration (optional)

The config also includes a GitHub tool (MCP server) that is switched off. Turning it on lets OpenCode read and work with your GitHub repositories directly. It needs Docker and a GitHub token:

1. Create a personal access token: on GitHub, go to Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token (classic), tick the `repo` scope, generate, and copy it.
2. Open `opencode.json` in a text editor. Find the `github` entry under `mcp` and change `"enabled": false` to `"enabled": true`, and replace `"YOUR_GITHUB_TOKEN"` with your token.
3. Restart OpenCode.

Docker (https://www.docker.com) is a heavy install — only do this if you actually want OpenCode to work with GitHub.

## 9. Costs and safety

- Everything here is prepaid: you add money, and usage is deducted from your balance. Both DeepSeek's and Anthropic's consoles show your usage — check occasionally.
- API keys are secrets. Never paste them into a chat message, never share screenshots of them, and never put them into files you share, upload, or commit to git. If a key leaks, revoke it and make a new one in the console.
- The `.env` file contains secrets too — keep it out of chats and out of git.
- Anthropic credits expire after one year and are non-refundable.
- Dollar amounts in this guide were checked in August 2026. Prices and minimums change — verify in each console when you purchase.

## 10. Troubleshooting

| Symptom | Fix |
|---|---|
| "model not found" or a model error | Usually a key problem. Run `opencode auth list` to see which providers are connected, then re-paste the key with `/connect`. |
| `npx: command not found` | Node.js is missing. Install the LTS version from https://nodejs.org, then close and reopen Terminal. |
| `git: command not found` | You do not need git. Use Option A (Download ZIP) in Step 2 instead. |
| `brew: command not found` | Homebrew is not installed. Install it from https://brew.sh, or use Option A or C from Step 1. |
| `opencode: command not found` right after installing | Close Terminal completely and open a new window (new windows pick up newly installed programs), then try again. |
| A command gives a syntax error | Copy the commands from this guide exactly, including the `~` and any quotes. If you re-typed them by hand, retype carefully. |
| Pasted screenshots are ignored, or observer errors | The Gemini key is missing. Make sure the project folder's `.env` contains a `GEMINI_API_KEY=` line with your key (Step 4). |
| Railway errors when OpenCode starts | The Railway add-on is optional. Either install the Railway CLI and run `railway login`, or ignore the message. |

If something is still not right, ask the person who shared this guide — they have the same setup.

---

## Budget path (alternative setup)

Running on a budget? Fund DeepSeek only. The core of the setup — the supervisor and all the junior agents — runs entirely on DeepSeek, so you get the full day-to-day workflow for the price of one prepaid balance. Everything in this guide still applies; you simply complete a smaller subset of Step 3 and Step 4.

1. Follow Steps 1–2 as written (install OpenCode and the configuration files).
2. In Step 3, do only the DeepSeek steps: account, prepaid funds, and API key.
3. **Optional but recommended:** also grab the free Gemini key (the Google Gemini part of Step 3) — no payment needed, it has a free tier. That keeps the screenshot feature and the Gemini agents working.
4. Skip the Anthropic and xAI accounts. The mid-tier and senior agents (Claude Sonnet 5 / Opus 4.8) stay configured but inactive — the supervisor won't use them until you add an Anthropic key later; Grok models remain an unused optional extra. (The team table in section 1 describes the full setup — budget mode runs the DeepSeek tier plus whatever keys you have added.)
5. In Step 4, connect only DeepSeek (and add the `GEMINI_API_KEY=` line to your `.env` file if you took the optional key). Then tell OpenCode to run in budget mode by prompting it: **"Use the budget instructions."** The supervisor sticks to junior-tier (DeepSeek) agents and won't call the paid tiers.
6. **Upgrade later without redoing anything:** connect the extra keys anytime with `/connect` (or add them to your `.env` file) and tell OpenCode **"Switch to the full production setup"** — everything is already configured and waiting.
