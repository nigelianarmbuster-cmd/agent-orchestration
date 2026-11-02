# OpenCode Setup Guide (macOS)

Welcome. This guide walks you through setting up OpenCode on your Mac, step by step. OpenCode is a free, open-source desktop app that gives you a team of AI assistants that can read your files, write code, and run commands on your computer. This guide assumes you have never used an AI coding tool before. Follow the steps in order, and paste each message exactly as written.

## A few words you will see

- **The app** — OpenCode itself: an ordinary window you open from Launchpad or your Applications folder, with a chat box at the bottom.
- **Providers** — a page inside the app's Settings where you paste each company's API key. This is the only place keys go in.
- **Model picker** — the menu in the message box where you choose which AI brain answers. It lists every model your keys allow.
- **Agent picker** — the menu in the message box where you choose which assistant answers — for example the `supervisor`, which plans and delegates to the rest of the team.
- **API key** — a secret password that lets a program use your account (and spend your balance). Treat it like a password.
- **Model** — the actual AI "brain" that does the thinking. Different companies make different models; each has a name like `deepseek/deepseek-flash`.
- **API vs subscription** — a subscription (like ChatGPT Plus or Claude Pro) lets you chat inside a company's own app. API access is a separate pay-as-you-go balance that other programs, like OpenCode, can use. A subscription does not include API access.
- **Prepaid** — you put money into a provider's balance first, and each request is deducted from it. There are no monthly fees here, but there is also no way to use an agent for free: every provider you set up must be funded before it will work.

## 1. What you're getting

The setup gives you a team of about 44 AI agents, arranged in tiers. The supervisor plans the work and hands pieces to specialist agents.

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

Work is split across three providers instead of running on one model, and spend is spread across all three prepaid balances: a structural ~64/26/10 DeepSeek/Anthropic/Google split.

DeepSeek is the primary workhorse — the supervisor, juniors, and mules run on V4.1 Flash. Claude (Anthropic) is used for quality gates — review, security, and exact-quote checking — and for mid/senior work where careful judgment matters. Gemini 3.8 Flash (Google) is used for research, planning, and vision (the Observer). Optional free local models run via Ollama.

Effort is tiered too: juniors run at low effort with thinking on, mid/senior agents run at high effort with thinking on, and mules run with thinking off to keep them cheap. Researcher and planner are the only mid-tier roles routed to Gemini, because structural planning and long-context web research carry little quality risk.

Download the app once (Step 8). Everything after that happens in OpenCode's own windows and chat box — adding keys, installing the config. You do not edit any files yourself.

The default model is `deepseek/deepseek-flash` and the default agent is `supervisor`. You pick the model once in the model picker (Step 14); the config supplies the agent.

- The **observer-bridge plugin**: when you paste a screenshot into the chat, it saves the image and automatically calls the observer agent to describe it. This needs the Google key you add in Step 12.
- **Add-on tools (MCP servers)** — all of them ship switched off, so turn one on only when you need it (see section 23). The list is playwright, chrome-devtools, elevenlabs, yt-dlp, vercel, gemini-api-docs, context7, github, macos-use and railway. The macos-use one is Mac-only, and Railway is the one to leave off permanently: enabling it can break the Observer, so use the Railway CLI instead.

## 2. What you'll need before you start

- Your Mac and about 30–45 minutes.
- **OpenCode 1.19.5** — installed in Step 8 as a normal Mac app.
- **Four accounts**: GitHub (free), DeepSeek (required), Anthropic (required on the Full plan), Google (required on the Full plan). Optional: xAI (only if you want Grok models — entirely optional and separate, and the Grok agent only exists if you add that key) and Ollama (free, no account).
- **Money on prepaid balances**: DeepSeek, Anthropic and Google are all pay-as-you-go — you add money before use. Amounts and minimums change; verify in each console when you sign up.
- Nothing else — the config comes from a public GitHub repository in Step 15. You do not need a GitHub account for that; the account is for keeping your own projects later.
- On a budget? Fund DeepSeek and take the free Gemini key for screenshots — see the [Budget path](#budget-path) section at the end of this guide.

**Funding is never optional.** DeepSeek, Anthropic and Google (Full plan) all require prepaid funds or credits before the agents will run. A trial credit is not enough — buy credits before you start. There is exactly one exception: on the **Budget plan**, the Gemini free tier is accepted for the Observer only.

> **Caution:** API keys are secret. Never post one in a chat, an issue, or a screenshot — paste it only into **Settings → Providers** inside the app.

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
2. You will add it in Step 10. OpenCode refers to this key as `DEEPSEEK_API_KEY`.

## 6. Step 4 — Create your Anthropic account and buy credits

Anthropic's Claude powers the mid and senior agents. On the Budget plan you skip this step — see the [Budget path](#budget-path).

1. Go to https://platform.claude.com and sign up with Google or an email address.
2. Buy prepaid usage credits: in the console, **Billing → Buy credits**. You must buy credits before you can use the API — the mid and senior agents will not work until you do. A small trial credit is not enough. Credits expire after one year and are non-refundable.

> **Do not use the Claude Pro/Max login.** Pro/Max is a subscription for chatting in Anthropic's own apps, and Anthropic's rules prohibit using it through tools like OpenCode. Use the API key route only.

## 7. Step 5 — Create your Anthropic API key

1. Create your API key: https://platform.claude.com/settings/keys → "Create key". Choose the longest expiration (up to "Never") so you do not have to redo this.
2. You will add it in Step 11. The key name is `ANTHROPIC_API_KEY`.

## 8. Step 6 — Create your Google Gemini key and add billing

The Gemini key powers the screenshot Observer and the Gemini agents. On the Full plan you fund it like the rest.

1. Go to https://aistudio.google.com/apikey, sign in with any Google account, accept the terms, and click **"Create API key"**.
2. Add billing and fund it before you start. The free tier is rate-limited and not for regular use, so it is not a substitute for a funded key — except on the Budget plan, where the free tier is allowed for the Observer only.
3. You will add it in Step 12. There is no file to create and no environment variable to set — the key goes into **Settings → Providers**, like the others.

## 9. Step 7 — Create an xAI Grok key (optional)

xAI is entirely optional and separate — the Grok agent only exists if you add that key, and nothing else in the setup depends on it.

1. Skim this step only if you want Grok models — nothing else in the setup uses it.
2. Console at https://console.x.ai → API Keys page → create a key.
3. You will add it in Step 13. There is no file to create and no environment variable to set.

## 10. Step 8 — Install the OpenCode app

This setup is built for **OpenCode 1.19.5**. You install it as a normal Mac app — there is no command line involved. First, check which kind of Mac you have: click the Apple menu → **About This Mac** → look at **Chip**. If it says **Apple M…** (for example "Apple M2"), download the Apple Silicon file. If it says **Intel**, download the Intel file.

- **[Download for Apple Silicon](https://github.com/anomalyco/opencode/releases/download/v1.19.5/opencode-desktop-mac-arm64.dmg)** (Chip says "Apple M…")
- [Download for Intel](https://github.com/anomalyco/opencode/releases/download/v1.19.5/opencode-desktop-mac-x64.dmg) (Chip says "Intel")

These are direct links to that one version. Do **not** use https://opencode.ai/download — it currently offers OpenCode 2 (2.2.5), which these config files are not built for. Release page for reference: https://github.com/anomalyco/opencode/releases/tag/v1.19.5

1. Click your link. Your Mac downloads a `.dmg` file — open it when it finishes.
2. A window opens with the OpenCode icon and an **Applications** folder. Drag **OpenCode** onto the **Applications** folder, then close the window.
3. Open **Applications** and double-click **OpenCode**. macOS may say it cannot check the app for malicious software, or that it is from an unidentified developer. If so, click **Done**, then **right-click** (or Control-click) the OpenCode icon → **Open** → **Open** again in the dialog that appears. If the button is not offered, open **System Settings → Privacy & Security** and click **Open Anyway** next to the OpenCode message.

> **Later, the app may offer an OpenCode 2 update.** The app updates itself automatically. Decline any update that says it moves OpenCode to version 2 — these config files are only built for 1.19.5.

## 11. Step 9 — Open the app

Open **Launchpad**, or **Applications** in Finder, and click **OpenCode**. The welcome screen appears.

**Verify:** OpenCode's welcome screen appears.

## 12. Step 10 — Add your DeepSeek key

Everything from here on happens in the app — keys, models and the config all go in through its own windows.

Open **Settings → Providers**, choose **DeepSeek**, paste the key from Step 3, and save.

The app stores the keys itself — you will never edit a file to add one.

**Verify:** **Providers** shows DeepSeek with a key saved. (All your keys are re-checked in Step 18.)

## 13. Step 11 — Add your Anthropic key

In **Settings → Providers**, choose **Anthropic**, paste an API key, and save. Do not use the Claude Pro/Max sign-in option if the app offers one (see the warning in Step 4).

**Budget plan:** skip — there is nothing to add without an Anthropic key. Add it later in **Settings → Providers** to unlock the mid and senior agents; nothing else has to be redone.

## 14. Step 12 — Add your Google key

In **Settings → Providers**, choose **Google**, paste the key from Step 6, and save.

This is what powers pasted screenshots (the Observer) and the Gemini agents.

**Budget plan:** still do this. The free tier is fine here, but only for screenshots (the Observer) — do not use the Gemini researcher/planner agents until the key is funded.

## 15. Step 13 — Add your xAI key (optional)

In **Settings → Providers**, choose **xAI**, and paste your Grok key. Skim or ignore this if you skipped the Grok key — xAI is not part of this setup unless you add it, and nothing here needs it.

## 16. Step 14 — Pick the default model

Open the **model picker** in the message box, search for `deepseek`, and choose `deepseek/deepseek-flash` — the default brain for the **supervisor**, the agent that plans and delegates. Do this now because it is what makes your *first* messages work: the config that sets the default has not been installed yet.

- If `deepseek/deepseek-flash` is not listed, DeepSeek has no key saved — redo Step 10.
- After Step 15 the config sets that same default, so nothing changes — this was not a temporary setting.

## 17. Step 15 — Ask OpenCode to install the config

OpenCode can fetch and install its own configuration. You paste one message into the chat box and let the agent do the work.

Paste this exactly, then press Enter:

> Install your own configuration. Clone https://github.com/nigelianarmbuster-cmd/opencode-config.git into your global OpenCode config folder — ~/.config/opencode on macOS, %USERPROFILE%\.config\opencode on Windows — and set up its dependencies. If that folder already exists, keep the files that are already there and add the missing ones. Tell me when it is done.

On your Mac the folder it writes to is `~/.config/opencode` (that is `/Users/<your-name>/.config/opencode`). A few things to know while it runs:

- OpenCode runs the commands on your computer itself. Any permission requests appear as dialogs in the app.
- If a dialog asks for permission to work outside your project folder, review it carefully before you approve.
- You can stop the run from the app at any time if anything looks wrong.
- It may need `git` — which comes with the Xcode command-line tools. If the app offers to install them, read the prompt first.

If it does not seem to be working, ask the app to try again — or ask the person who shared this guide.

## 18. Step 16 — Answer its questions and let it finish

While the config installs, the agent may ask you questions in the chat box (for example, which folder to use, or whether to install `git`). Answer them, and read each permission request before you approve it. **If you are not sure what a request means, choose no and ask the agent to explain it in plain English before allowing anything.** Then wait for it to say it is done. Do not close the app while it is working, or it may stop halfway.

## 19. Step 17 — Restart the app

Quit OpenCode with **⌘Q**, then reopen it from **Launchpad** or **Applications**. The app reloads the new config on startup.

**Verify:** the **agent picker** shows `supervisor`.

## 20. Step 18 — Check your models in the model picker

Open the **model picker** in the message box and confirm the models are there: `deepseek/deepseek-flash`, plus Anthropic, Google and xAI models for whichever keys you added. (On the Budget plan you will see DeepSeek and Gemini.)

If a provider's models are missing, re-add that key in **Settings → Providers**.

## 21. Step 19 — Run a first smoke test

Ask something simple. A normal answer means everything is wired up. In the chat box:

> What is 2+2?

**Verify:** a normal answer comes back.

An error here means the model is not connected — redo Step 10 (add DeepSeek), then Step 17 (restart the app).

A saved key can still fail if the account is not funded — check the provider's console that billing is set up and the balance is topped up. This is the most common cause of a first-attempt error.

## 22. Optional extras

### Ollama — free local models (no account, no key)

Ollama runs AI models on your own Mac — free, private, and offline, with no balance to top up. It is a good way to try things out without spending anything. Which model fits your machine depends on its memory and graphics card, so this setup does not assume everyone has the same hardware.

1. Download from https://ollama.com/download and install like any Mac app.
2. Let the setup pick the right model for your machine. In the OpenCode chat, ask: **"Set up local models that fit my machine."** The supervisor checks your Mac's memory, graphics card, and free disk space, then downloads **one** model that fits — a small one on modest hardware, a larger one only if your machine can run it comfortably. If you later want a bigger model, just ask for it in the chat.
3. The config already includes the Ollama provider (it points at `http://localhost:11434/v1`), so pulled models appear in the model picker automatically. Local models are slower and less capable than the cloud ones — that is the trade-off, and they never replace a funded provider for the tiered agents.

### Railway deployments (CLI)

Railway deployments work through the Railway CLI — no add-on tool needed. Install the Railway CLI, run `railway login`, then use `railway up` or `railway deploy` from a terminal. The config also contains a Railway MCP server, but leave it switched off: enabling it has been known to break the screenshot (Observer) feature, and the CLI covers the same ground. If you do not use Railway, just ignore it.

### GitHub integration (optional)

The config also includes a GitHub tool (MCP server) that is switched off. Turning it on lets OpenCode read and work with your GitHub repositories directly — this is where the GitHub account from Step 1 comes in handy. It needs Docker and a GitHub token:

1. Create a personal access token: on GitHub, go to Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token (classic), tick the `repo` scope, generate, and copy it.
2. Open `~/.config/opencode/opencode.json` in a text editor. Find the `github` entry under `mcp` and change `"enabled": false` to `"enabled": true`, and replace `"YOUR_GITHUB_TOKEN"` with your token.
3. Restart OpenCode.

Docker (https://www.docker.com) is a heavy install — only do this if you actually want OpenCode to work with GitHub. The other dormant MCP servers are listed in section 24 below ("Learn how to customize your OpenCode config"), with the same activation steps.

## 23. Learn how to customize your OpenCode config

Nothing to do now. Your setup ships with extra add-on tools called **MCP servers**. All of them are **dormant** — declared in the config but switched off — so they cost nothing and do nothing until you turn one on.

**Why they are off, and how to turn one on.** Every enabled MCP server loads at startup and adds tokens to each conversation, and some pull down packages or need their own accounts and keys. The core setup needs none of them, so they ship dormant — switch on exactly what you use.

To enable one: open `~/.config/opencode/opencode.json` in a text editor, find the tool's name under `"mcp"`, change `"enabled": false` to `"enabled": true`, save, and restart OpenCode. Check it is live by asking the agent: "List the MCP servers and which ones are enabled."

## 24. Costs and safety

- Everything here is prepaid: you add money, and usage is deducted from your balance. Each console shows your usage — check occasionally. Funding is never optional: DeepSeek, Anthropic and Google (Full plan) must be funded before the agents will work.
- **The only free-tier allowance** is the Budget plan's Gemini free tier, and only for the Observer (pasted screenshots). Do not drive the Gemini agents on it.
- API keys are stored by OpenCode itself in its own credential store — not in this repo, and not in a file you edit. Never paste a key into a chat message, an issue, or a commit, and never share a screenshot of one. If a key leaks, revoke it in the provider console and make a new one.
- **The agent can run commands on your computer** when you paste an instruction — Step 15 is the main one here. Those commands do not prompt individually. You may be asked once to allow work in `~/.config/opencode`; that is the only folder it needs. If any other folder is named, choose **no**. You can stop the agent from the app.
- Anthropic credits expire after one year and are non-refundable.
- Dollar amounts and minimums change. Verify in each console when you purchase.

## 25. Troubleshooting

| Symptom | Fix |
|---|---|
| The app will not open, or macOS says it is from an "unidentified developer" | Click **Done**, then **right-click** (or Control-click) the OpenCode icon in Applications → **Open** → **Open**. If the button is not offered, go to **System Settings → Privacy & Security** and click **Open Anyway** next to the OpenCode message. |
| A key does not stick in Settings → Providers | Make sure you copied the whole key, with no spaces or line breaks, and paste it directly into the key field — never retype it. Save, then check the provider shows its key. A key that was deleted or revoked in the provider's console will not work either; make a new one. |
| A model is missing from the model picker | Its provider's key is missing or not saved. Re-add the key in **Settings → Providers** (Step 10 for DeepSeek, Step 11 for Anthropic, Step 12 for Google, Step 13 for xAI) and save it again — the models appear as soon as the key is saved. |
| The app offers to update to OpenCode 2 | Decline it. These config files are built for 1.19.5 only; an OpenCode 2 update can break the setup. Keep the 1.19.5 app from Step 8. |
| "model not found" or a model error | Usually a key or funding problem. Check the provider shows a key in **Settings → Providers**, then pick the model again in the model picker. A funded balance is required — an empty balance fails the same way as a missing key. |
| The supervisor is missing from the agent picker | The config did not load. Redo Step 15 (ask OpenCode to install the config), finish it (Step 16), and restart the app (Step 17). The agent picker should then show `supervisor`. |
| The config install stops or asks for a folder you do not recognise | Allow only a dialog that names `~/.config/opencode`; choose **no** for anything else. Stop the run from the app, then ask it to try again (Step 15). If it still fails, ask the person who shared this guide. |
| A permission dialog names a folder you do not recognise | Choose **no**. The only folder this setup needs is `~/.config/opencode`. |
| It asks to install `git` while installing the config | `git` comes with the Xcode command-line tools. Review the permission request, complete the installer, then ask the app to continue (Step 15) — if anything is unclear, ask the agent to explain it before you approve. |
| Pasted screenshots are ignored, or observer errors | The Google key is missing or unfunded. Add it again in **Settings → Providers** → Google (Step 12). |
| Screenshots return nothing, or the Observer stays silent | Check the Google key is saved in **Settings → Providers**. Also check no stale Google/Gemini environment variable is set — an environment variable overrides the saved key, so an old one makes every Gemini call fail with "API key not valid". Check from a terminal window with `printenv \| grep -i -E 'GOOGLE\|GEMINI'`. If anything appears, remove it: delete the export from your shell profile, then restart the app. |
| Gemini agents fail or behave oddly in the Budget plan | In Budget the free tier is for pasted screenshots only; add billing to the Google key before using the Gemini researcher or planner. |
| Railway errors after you enable it | Use the Railway CLI instead of the MCP (see the Railway section). If OpenCode misbehaves with the Railway MCP on — for example the Observer returns empty screenshot results — set `mcp.railway` back to `"enabled": false` and restart. |

If something is still not right, ask the person who shared this guide — they have the same setup.

---

## 26. Budget path (alternative setup)

Running on a budget? Fund DeepSeek only. The core of the setup — the supervisor and all the junior agents (V4.1 Flash) — runs entirely on DeepSeek, so you get the full day-to-day workflow for the price of one prepaid balance. Everything in this guide still applies; you simply complete a smaller subset of Steps 2–7, then 8–19. DeepSeek-only is the intended budget minimum; the system is built so a beginner can start there and add Anthropic and Google keys later to unlock the full multi-provider tiers. The full setup spreads spend roughly 64% DeepSeek / 26% Anthropic / 10% Google — a deliberate deterministic choice, not prompt-guessing.

The one free-tier allowance in this setup is the **Gemini free tier, for the Observer (pasted screenshots) only**. Do not use the Gemini mid-tier researcher/planner agents until that key is funded.

1. Follow Steps 1, 2, 8, 9 (GitHub, DeepSeek with funds, install the app, open it) as written.
2. In Step 4, skip it — no Anthropic account, no credits. The Claude mid-tier and senior agents (Sonnet 5 / Opus 4.8) stay configured but inactive.
3. In Step 6, create the Gemini key and add billing; the free tier is fine for screenshots, but do not use the Gemini agents on it. Add it in Step 12.
4. Skip xAI (Step 7) — it is not part of this setup unless you add it.
5. In Steps 10 and 12, add DeepSeek and Google only. Then pick `deepseek/deepseek-flash` in Step 14, install the config (Step 15), finish it (Step 16), restart the app (Step 17), and verify your models and a first question (Steps 18–19).
6. Tell OpenCode to run in budget mode by prompting it: **"Use the budget instructions."** The supervisor sticks to junior-tier (DeepSeek) agents and will not call the paid tiers.
7. **Upgrade later without redoing anything:** add the extra keys anytime in **Settings → Providers** and tell OpenCode **"Switch to the full production setup"** — everything is already configured and waiting. Fund the Anthropic and Google keys first: the mid and senior agents will not work until the balances are funded.
