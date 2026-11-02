# OpenCode Setup Guide (macOS)

Welcome. This guide walks you through setting up OpenCode on your Mac, step by step. OpenCode is a free, open-source program that runs in your Terminal and gives you a team of AI assistants that can read your files, write code, and run commands on your computer. This guide assumes you have never used an AI coding tool before. Follow the steps in order, and copy-paste commands exactly.

## A few words you will see

- **Terminal** — a built-in Mac app where you type text commands instead of clicking buttons. To open it: press Command+Space, type "Terminal", press Return.
- **API key** — a secret password that lets a program use your account (and spend your balance). Treat it like a password.
- **Model** — the actual AI "brain" that does the thinking. Different companies make different models; each has a name like `deepseek/deepseek-flash`.
- **API vs subscription** — a subscription (like ChatGPT Plus or Claude Pro) lets you chat inside a company's own app. API access is a separate pay-as-you-go balance that other programs, like OpenCode, can use. A subscription does not include API access.
- **Prepaid** — you put money into a provider's balance first, and each request is deducted from it. There are no monthly fees here, but there is also no way to use an agent for free: every provider you set up must be funded before it will work.
- **Prompt marker (`$`)** — commands sometimes start with `$`, which just marks where to paste: open Terminal, paste the command after the `$`, press Return. You never type the `$` itself — only the command after it. The setup page's copy buttons leave the marker off automatically.

## 1. What you're getting

The setup gives you a team of about 44 AI agents, arranged in tiers. The main agent (the "supervisor") plans the work and hands pieces to specialist agents.

| What | Model / provider | What it's for |
|---|---|---|
| Main assistant and "supervisor" | deepseek/deepseek-flash (DeepSeek) | The default brain — plans work and delegates |
| Junior agents | deepseek/deepseek-flash (DeepSeek) | Small, focused tasks |
| Mid-tier agents | anthropic/claude-sonnet-5 (Anthropic) | Everyday work, careful judgment |
| Mid researcher & planner | google/gemini-3.8-flash (Google) | Web research and structural planning |
| Senior agents | anthropic/claude-opus-4-8 (Anthropic) | The hardest, most careful work |
| Gemini agents (gemini-worker) | google/gemini-3.8-flash (Google) | Web research, long documents, images |
| Gemini mule (gemini-mule) | google/gemini-3.5-flash-lite (Google) | Web research, long documents, images |
| Observer | google/gemini-3.8-flash (Google) | Reads screenshots you paste into the chat |
| Local models | Ollama (runs on your Mac) | Free and private — optional extra |

A few things to know:

- You paste **two commands** into Terminal (install, then start). Everything after that happens in OpenCode's chat box — including connecting your keys and installing the config. You do not edit any files yourself.
- The default model is `deepseek/deepseek-flash` and the default agent is `supervisor`. You pick the model once in `/models` (Step 14) and the config supplies the agent.
- Plan on **30–45 minutes**, start to finish.

**Funding is never optional.** DeepSeek, Anthropic and Google (Full plan) all require prepaid funds or credits before the agents will run. A trial credit is not enough — buy credits before you start. There is exactly one exception in this whole guide: on the **Budget plan**, the Gemini free tier is acceptable for the Observer (pasted screenshots) only. See the [Budget path](#budget-path) at the end.

**Why three providers?** This setup deliberately spreads work across DeepSeek, Anthropic, and Google instead of running everything on one model. Each is used where it is genuinely strongest, and spend is spread across all three prepaid balances. DeepSeek is the primary workhorse — the supervisor, juniors, and mules run on V4.1 Flash. Claude (Anthropic) is used for quality gates — review, security, and exact-quote checking — and for mid/senior work where careful judgment matters. Gemini 3.8 Flash (Google) is used for research, planning, and vision (the Observer), where long-context synthesis shines at low quality risk. Effort is tiered too: juniors run at low effort with thinking on, mid/senior agents run at high effort with thinking on, and mules run with thinking off to keep them cheap. Researcher and planner are the only mid-tier roles that moved to Gemini, because that is where Gemini is genuinely competitive. The Observer reads screenshots, which is a vision task, so it needs a Google/Gemini key — not an Anthropic one.
- The **observer-bridge plugin**: when you paste a screenshot into the chat, it saves the image and automatically calls the observer agent to describe it. This needs the Google key you connect in Step 12.
- **Add-on tools (MCP servers)** — an MCP server is an optional add-on that gives OpenCode extra tools. All of them are switched off in this config (playwright, chrome-devtools, elevenlabs, yt-dlp, vercel, gemini-api-docs, context7, github, macos-use, railway) — you can ignore them. They stay dormant until you turn one on (see section 22). The macos-use one is Mac-only and switched off anyway. Railway is the one to leave off permanently — use the Railway CLI instead, because enabling the Railway MCP has been known to break the screenshot (Observer) feature.

## 2. What you'll need before you start

- Your Mac and about 30–45 minutes.
- **OpenCode 1.19.5** — installed in Step 8 with one command. You do **not** need Node.js.
- **Four accounts**: GitHub (free), DeepSeek (required), Anthropic (required on the Full plan), Google (required on the Full plan). Optional: xAI (only if you want Grok models — it is entirely optional and separate, and the Grok agent only exists if you add that key) and Ollama (free, no account).
- **Money on prepaid balances**: DeepSeek, Anthropic and Google are all pay-as-you-go — you add money before use, and the balance must be funded before the agents will work. Amounts and minimums change; verify in each console when you sign up. The only exception is the Budget plan's Gemini free tier, and only for the Observer.
- Nothing else — the configuration files are downloaded from a public GitHub page in Step 15. You do not need a GitHub account for that (the repository is public); the account is for keeping your own projects later.
- On a budget? Fund DeepSeek and take the free Gemini key for screenshots — see the [Budget path](#budget-path) section at the end of this guide.

## 3. Step 1 — Create a GitHub account

Free, and it takes a minute. You do **not** need it to download anything in this guide. It is where you will keep your own projects later, and some optional add-ons use it.

1. Go to https://github.com/signup and create an account.
2. Save the password in your password manager — as its own entry, separate from the provider keys below.

## 4. Step 2 — Create your DeepSeek account and add funds

DeepSeek powers the supervisor and all the junior agents, so this is the one provider every setup needs.

1. Create an account at https://platform.deepseek.com.
2. Add money to your prepaid balance **now** — before you go any further. DeepSeek is pay-as-you-go with no free tier, so the balance must be funded before the agents will work.
3. Payment methods and minimum top-up amounts are only shown inside the console. Amounts change, so verify there when you sign up.

## 5. Step 3 — Create your DeepSeek API key

1. Create your API key: go to https://platform.deepseek.com/api_keys and click **"Create API key"**. Copy the key and store it somewhere safe (a password manager or a private note). Never paste a key into a chat.
2. You will connect it in Step 10. OpenCode refers to this key as `DEEPSEEK_API_KEY`.

## 6. Step 4 — Create your Anthropic account and buy credits

Anthropic's Claude powers the mid and senior agents. On the Budget plan you skip this step — see the [Budget path](#budget-path).

1. Go to https://platform.claude.com and sign up with Google or an email address.
2. Buy prepaid usage credits: in the console, **Billing → Buy credits**. You must buy credits before you can use the API — the mid and senior agents will not work until you do. A small trial credit is not enough. Credits expire after one year and are non-refundable.

> **Do not use the Claude Pro/Max login.** Pro/Max is a subscription for chatting in Anthropic's own apps, and Anthropic's rules prohibit using it through tools like OpenCode. Use the API key route only.

## 7. Step 5 — Create your Anthropic API key

1. Create your API key: https://platform.claude.com/settings/keys → "Create key". Choose the longest expiration (up to "Never") so you do not have to redo this.
2. You will connect it in Step 11. The key name is `ANTHROPIC_API_KEY`.

## 8. Step 6 — Create your Google Gemini key and add billing

The Gemini key powers the screenshot Observer and the Gemini agents. On the Full plan you fund it like the rest.

1. Go to https://aistudio.google.com/apikey, sign in with any Google account, accept the terms, and click **"Create API key"**.
2. Add billing and fund it before you start. The free tier is rate-limited and not for regular use, so it is not a substitute for a funded key — except on the Budget plan, where the free tier is allowed for the Observer only.
3. You will connect it in Step 12. There is no file to create and no environment variable to set — the key goes in through `/connect`, like the others.

## 9. Step 7 — Create an xAI Grok key (optional)

xAI is entirely optional and separate — the Grok agent only exists if you add that key, and nothing else in the setup depends on it.

1. Skim this step only if you want Grok models — nothing else in the setup uses it.
2. Console at https://console.x.ai → API Keys page → create a key.
3. You will connect it in Step 13. There is no file to create and no environment variable to set.

## 10. Step 8 — Install OpenCode

This setup is built for **OpenCode 1.19.5**. Paste one command into Terminal.

If you have never used a command line: the `$` at the start of a command tells you to paste it into Terminal. You never type the marker itself — paste the command *after* the `$` and press Return. To open Terminal, press Command+Space, type **Terminal**, and press Return.

```
$ curl -fsSL https://opencode.ai/install | bash
```

Then check it worked. You should see `1.19.5`:

```
$ opencode --version
```

Close Terminal and open a new window afterwards — that is how it finds the new command.

- **Node.js is not needed.** The installer is a self-contained binary, and OpenCode installs its own plugin dependencies later. There is no `npm install` step anywhere in this guide.
- **Do not use `brew install opencode` or the download button on opencode.ai/download.** Both install OpenCode 2, which these config files are not built for. The OpenCode 1 installer refuses version 2 on purpose.
- If you insist on Homebrew, the OpenCode 1 formula is `brew install anomalyco/tap/opencode`. It needs Homebrew, which you can install from https://brew.sh if `brew --version` fails. This route works today but is not the tested path — prefer the one-line installer above.
- Release page for this version: https://github.com/anomalyco/opencode/releases/tag/v1.19.5

## 11. Step 9 — Start OpenCode

Paste this into the same terminal:

```
$ opencode
```

OpenCode opens its own chat screen. That was the last terminal paste in this guide — from now on you type in OpenCode's chat box, not the Terminal. Commands that start with `/` are typed there too.

## 12. Step 10 — Connect DeepSeek

In OpenCode's chat box, type `/connect`, choose **DeepSeek**, choose **API key**, paste your key, and press Enter.

OpenCode remembers keys itself — you will never edit a file to add one.

**Verify:** OpenCode lists DeepSeek as connected. (All your keys are re-checked in Step 18.)

## 13. Step 11 — Connect Anthropic

Type `/connect` again, choose **Anthropic**, choose **"Manually enter API Key"**, and paste. Do not pick the Pro/Max option (see the warning in Step 4).

**Budget plan:** skip — there is nothing to connect without an Anthropic key. Add it later with `/connect` to unlock the mid and senior agents; nothing else has to be redone.

## 14. Step 12 — Connect Google (Gemini)

Type `/connect`, choose **Google**, choose **API key**, and paste. The key goes in the same way as the others — there is no file to edit, and no `.env` to create.

This is what powers pasted screenshots (the Observer) and the Gemini agents.

**Budget plan:** still do this. The free tier is fine here, but only for screenshots (the Observer) — do not use the Gemini researcher/planner agents until the key is funded.

## 15. Step 13 — Connect xAI (optional)

Type `/connect`, choose **xAI**, and paste your Grok key. Skim or ignore this if you skipped the Grok key — xAI is not part of this setup unless you add it, and nothing here needs it.

## 16. Step 14 — Pick the default model

Type `/models` in the chat box, search for `deepseek`, and choose `deepseek/deepseek-flash` — the default brain for the **supervisor**, the agent that plans and delegates. Do this now because it is what makes your *first* messages work: the config that sets the default has not been installed yet.

- If `deepseek/deepseek-flash` is not listed, DeepSeek is not connected — redo Step 10.
- After Step 16 the config sets that same default, so nothing changes — this was not a temporary setting.

## 17. Step 15 — Ask OpenCode to install the config

OpenCode can fetch and install its own configuration. You do not copy files or run setup commands — you paste one message into the chat box and let the agent do the work.

Paste this exactly, then press Enter:

> Install your own configuration. Clone https://github.com/nigelianarmbuster-cmd/opencode-config.git into your global OpenCode config folder — ~/.config/opencode on macOS, %USERPROFILE%\.config\opencode on Windows — and set up its dependencies. If that folder already exists, keep the files that are already there and add the missing ones. Tell me when it is done.

On your Mac the folder it writes to is `~/.config/opencode` (that is `/Users/<your-name>/.config/opencode`). A few things to know while it runs:

- OpenCode runs the commands on your computer itself and does not ask before each one.
- It may ask **once** for permission to work outside your project folder. The folder it needs is `~/.config/opencode`. If the prompt names that folder, choose **allow**; if it names any other folder, choose **no** and stop.
- Ctrl+C stops the agent if anything looks wrong.
- It may need `git` — which comes with the Xcode command-line tools your Mac already prompted you to install when you first used Terminal. If OpenCode asks to install something, read the prompt first and allow it only if it names `git`.

**Only if you would rather do it yourself** — paste this into a terminal instead of the chat box:

```
$ git clone https://github.com/nigelianarmbuster-cmd/opencode-config.git ~/.config/opencode
```

If you get "destination path already exists", the config folder is not empty. Either let the chat instruction merge the files in (it keeps what is already there), or ask the person who shared this guide.

## 18. Step 16 — Restart OpenCode

Type `/exit` in the chat box, then run `opencode` again in your terminal. (Or close the terminal, open a new one, and run `opencode`.)

**Verify:** the agent name shown at the bottom of the OpenCode window reads `supervisor`, and pressing Tab cycles through the primary agents.

There is no `/agents` command to type — OpenCode 1 does not have one. The agent name at the bottom and the Tab key are how you see the team.

## 19. Step 17 — Confirm the team is loaded

Ask the supervisor to list the team. In the chat box:

> List your subagents by tier.

**Verify:** you should see the junior, mid and senior agent groups listed, plus the mule agents.

## 20. Step 18 — Check your connections

Ask the supervisor — it runs the check for you. In the chat box:

> Run opencode auth list and tell me which providers are connected.

**Verify:** `deepseek` is listed, plus `anthropic`, `google` and `xai` if you connected them. If one is missing, connect it again with `/connect`.

Prefer to check yourself? Run `opencode auth list` in a terminal. `opencode auth logout` removes a provider.

## 21. Step 19 — Run a first smoke test

Ask something simple. A normal answer means everything is wired up. In the chat box:

> What is 2+2?

**Verify:** a normal answer comes back.

An error here means the model is not connected — redo Step 10 (connect DeepSeek), then Step 16 (restart OpenCode).

## 22. Optional extras

### Ollama — free local models (no account, no key)

Ollama runs AI models on your own Mac — free, private, and offline, with no balance to top up. It is a good way to try things out without spending anything. Which model fits your machine depends on its memory and graphics card, so this setup does not assume everyone has the same hardware.

1. Download from https://ollama.com/download and install like any Mac app.
2. Let the setup pick the right model for your machine. In the OpenCode chat, ask: **"Set up local models that fit my machine."** The supervisor checks your Mac's memory, graphics card, and free disk space, then downloads **one** model that fits — a small one on modest hardware, a larger one only if your machine can run it comfortably. If you later want a bigger model, just ask for it in the chat.
3. The config already includes the Ollama provider (it points at `http://localhost:11434/v1`), so pulled models appear in `/models` automatically. Local models are slower and less capable than the cloud ones — that is the trade-off, and they never replace a funded provider for the tiered agents.

### Railway deployments (CLI)

Railway deployments work through the Railway CLI — no add-on tool needed. Install the Railway CLI, run `railway login`, then use `railway up` or `railway deploy` from a terminal. The config also contains a Railway MCP server, but leave it switched off: enabling it has been known to break the screenshot (Observer) feature, and the CLI covers the same ground. If you do not use Railway, just ignore it.

### GitHub integration (optional)

The config also includes a GitHub tool (MCP server) that is switched off. Turning it on lets OpenCode read and work with your GitHub repositories directly — this is where the GitHub account from Step 1 comes in handy. It needs Docker and a GitHub token:

1. Create a personal access token: on GitHub, go to Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token (classic), tick the `repo` scope, generate, and copy it.
2. Open `~/.config/opencode/opencode.json` in a text editor. Find the `github` entry under `mcp` and change `"enabled": false` to `"enabled": true`, and replace `"YOUR_GITHUB_TOKEN"` with your token.
3. Restart OpenCode.

Docker (https://www.docker.com) is a heavy install — only do this if you actually want OpenCode to work with GitHub. The other dormant MCP servers are listed in section 23 below ("Learn how to customize your OpenCode config"), with the same activation steps.

## 23. Learn how to customize your OpenCode config

Nothing to do now. Your setup ships with extra add-on tools called **MCP servers**. All of them are **dormant** — declared in the config but switched off — so they cost nothing and do nothing until you turn one on.

**Why they are off, and how to turn one on.** Every enabled MCP server loads at startup and adds tokens to each conversation, and some pull down packages or need their own accounts and keys. The core setup needs none of them, so they ship dormant — switch on exactly what you use.

To enable one: open `~/.config/opencode/opencode.json` in a text editor, find the tool's name under `"mcp"`, change `"enabled": false` to `"enabled": true`, save, and restart OpenCode. Check it is live with:

```text
$ opencode mcp list
```

## 24. Costs and safety

- Everything here is prepaid: you add money, and usage is deducted from your balance. Each console shows your usage — check occasionally. Funding is never optional: DeepSeek, Anthropic and Google (Full plan) must be funded before the agents will work.
- **The only free-tier allowance** is the Budget plan's Gemini free tier, and only for the Observer (pasted screenshots). Do not drive the Gemini agents on it.
- API keys are stored by OpenCode itself in its own credential store — not in this repo, and not in a file you edit. Never paste a key into a chat message, an issue, or a commit, and never share a screenshot of one. If a key leaks, revoke it in the provider console and make a new one.
- **The agent can run commands on your computer** when you paste an instruction — Step 15 is the main one here. Those commands do not prompt individually. You may be asked once to allow work in `~/.config/opencode`; that is the only folder it needs. If any other folder is named, choose **no**. Ctrl+C stops the agent.
- Anthropic credits expire after one year and are non-refundable.
- Dollar amounts and minimums change. Verify in each console when you purchase.

## 25. Troubleshooting

| Symptom | Fix |
|---|---|
| `opencode: command not found` right after installing | Close Terminal completely and open a new window (new windows pick up newly installed programs), then try again. |
| `opencode --version` does not say `1.19.5` | You have OpenCode 2 (usually from `brew install opencode` or opencode.ai/download). Uninstall it, then re-run the one-line installer from Step 8. |
| "model not found" or a model error | Usually a key or funding problem. Ask the supervisor to run `opencode auth list`, then re-paste the key with `/connect`. A funded balance is required — an empty balance fails the same way as a missing key. |
| The agent name at the bottom is not `supervisor` | The config did not load. Check that `~/.config/opencode/opencode.json` exists, then `/exit` and run `opencode` again. |
| No supervisor, or the agent team is missing | The config did not load. Redo Step 15 (ask OpenCode to install the config) and restart (Step 16). The agent name at the bottom of the window should read `supervisor`. |
| The config paste stops or asks for a folder you do not recognise | Ctrl+C stops it. Allow only a prompt that names `~/.config/opencode`; choose no for anything else. The manual `git clone` in Step 15 is the fallback. |
| A permission prompt names a folder you do not recognise | Choose **no**. The only folder this setup needs is `~/.config/opencode`. |
| `git: command not found` while installing the config | Install the Xcode command-line tools (your Mac offers them automatically the first time you use `git`), then re-run the Step 15 instruction. |
| `brew: command not found` | Homebrew is not installed. Use the one-line installer from Step 8 — you do not need Homebrew. |
| A command gives a syntax error | Copy the commands from this guide exactly, including the `~` and any quotes. If you re-typed them by hand, retype carefully. |
| Pasted screenshots are ignored, or observer errors | The Google key is missing or unfunded. Connect it again with `/connect` → Google → API key (Step 12). |
| Screenshots return nothing, or the Observer stays silent | The Gemini key is not reaching OpenCode. Run `opencode auth list` (or ask the supervisor to run it) and confirm `google` is listed; if not, reconnect with `/connect` → **Google** → **API key**. Also check no stale Google/Gemini environment variable is set — an environment variable overrides the connected key, so an old one makes every Gemini call fail with "API key not valid". Check with `printenv \| grep -i -E 'GOOGLE\|GEMINI'`. If anything appears, remove it: delete the export from your shell profile, reopen Terminal, and restart OpenCode. |
| Gemini agents fail or behave oddly in the Budget plan | In Budget the free tier is for pasted screenshots only; add billing to the Google key before using the Gemini researcher or planner. |
| Railway errors after you enable it | Use the Railway CLI instead of the MCP (see the Railway section). If OpenCode misbehaves with the Railway MCP on — for example the Observer returns empty screenshot results — set `mcp.railway` back to `"enabled": false` and restart. |

If something is still not right, ask the person who shared this guide — they have the same setup.

---

## 26. Budget path (alternative setup)

Running on a budget? Fund DeepSeek only. The core of the setup — the supervisor and all the junior agents (V4.1 Flash) — runs entirely on DeepSeek, so you get the full day-to-day workflow for the price of one prepaid balance. Everything in this guide still applies; you simply complete a smaller subset of Steps 2–10. DeepSeek-only is the intended budget minimum; the system is built so a beginner can start there and add Anthropic and Google keys later to unlock the full multi-provider tiers. The full setup spreads spend roughly 64% DeepSeek / 26% Anthropic / 10% Google — a deliberate deterministic choice, not prompt-guessing.

The one free-tier allowance in this setup is the **Gemini free tier, for the Observer (pasted screenshots) only**. Do not use the Gemini mid-tier researcher/planner agents until that key is funded.

1. Follow Steps 1, 2, 8, 9 (GitHub, DeepSeek with funds, install OpenCode, start it) as written.
2. In Step 4, skip it — no Anthropic account, no credits. The Claude mid-tier and senior agents (Sonnet 5 / Opus 4.8) stay configured but inactive.
3. In Step 6, create the Gemini key and add billing; the free tier is fine for screenshots, but do not use the Gemini agents on it. Connect it in Step 12.
4. Skip xAI (Step 7) — it is not part of this setup unless you add it.
5. In Steps 10 and 12, connect DeepSeek and Google only. Then pick `deepseek/deepseek-flash` in Step 14, install the config (Step 15), restart (Step 16), and verify (Steps 17–19).
6. Tell OpenCode to run in budget mode by prompting it: **"Use the budget instructions."** The supervisor sticks to junior-tier (DeepSeek) agents and will not call the paid tiers.
7. **Upgrade later without redoing anything:** connect the extra keys anytime with `/connect` and tell OpenCode **"Switch to the full production setup"** — everything is already configured and waiting. Fund the Anthropic and Google keys first: the mid and senior agents will not work until the balances are funded.
