# OpenCode Setup Guide (Windows)

Welcome. This guide walks you through setting up OpenCode on Windows, step by step. OpenCode is a free, open-source desktop app that gives you a team of AI assistants that can read your files, write code, and run commands on your computer. This guide assumes you have never used an AI coding tool before. Follow the steps in order. Everything happens by clicking and typing in the app — you never open a terminal.

## A few words you will see

- **App window** — the OpenCode desktop app: a normal Windows window you open from the Start menu. It has a message box for chatting, a model picker, an agent picker, and a Settings screen.
- **Providers** — the screen where you give OpenCode your API keys. You reach it from inside the app: **Settings → Providers**. Each provider (DeepSeek, Anthropic, Google, xAI) gets its own entry and its own key.
- **Model picker** — the list of available models, opened from the message box. You search it and click a model to use it.
- **Agent picker** — the list of agents (the "supervisor" and its team), also opened from the message box. It shows which agent is answering.
- **API key** — a secret password that lets a program use your account (and spend your balance). Treat it like a password.
- **Prepaid balance** — money you add to a provider's console in advance. Usage is deducted from it. Every provider in this setup works this way: you cannot use a provider until you have funded it.
- **Model** — the actual AI "brain" that does the thinking. Different companies make different models; each has a name like `deepseek/deepseek-flash`.
- **API vs subscription** — a subscription (like ChatGPT Plus or Claude Pro) lets you chat inside a company's own app. API access is a separate pay-as-you-go balance that other programs, like OpenCode, can use. A subscription does not include API access.

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
| Local models | Ollama (runs on your PC) | Free and private — optional extra |

A few things to know:

- The default model is `deepseek/deepseek-flash` and the default agent is `supervisor`. You do not configure any of this yourself — the config you install in Step 15 handles it.

**Why three providers?** This setup deliberately spreads work across DeepSeek, Anthropic, and Google instead of running everything on one model. Each is used where it is genuinely strongest, and spend is spread across all three prepaid balances. DeepSeek is the primary workhorse — the supervisor, juniors, and mules run on V4.1 Flash. Claude (Anthropic) is used for quality gates — review, security, and exact-quote checking — and for mid/senior work where careful judgment matters. Gemini 3.8 Flash (Google) is used for research, planning, and vision (the Observer), where long-context synthesis shines at low quality risk. Effort is tiered too: juniors run at low effort with thinking on, mid/senior agents run at high effort with thinking on, and mules run with thinking off to keep them cheap. Researcher and planner are the only mid-tier roles that moved to Gemini, because that is where Gemini is genuinely competitive — structural planning and long-context web research carry little quality risk. The Observer reads screenshots, which is a vision task, so it needs a Google/Gemini key — not an Anthropic one.

- The **observer-bridge plugin**: when you paste a screenshot into the chat, it saves the image and automatically calls the observer agent to describe it. This needs a Google Gemini key (Step 6).
- **Add-on tools (MCP servers)** — an MCP server is an optional add-on that gives OpenCode extra tools. All of them are switched off in this config (playwright, chrome-devtools, elevenlabs, yt-dlp, vercel, gemini-api-docs, context7, github, macos-use, railway) — you can ignore them. They stay dormant until you turn one on (see the "Learn how to customize your OpenCode config" section of the setup page, or section 20 below). The macos-use one is for Macs only, and it is switched off in this config anyway. Railway is the one to leave off permanently — use the Railway CLI instead (see the Railway section), because enabling the Railway MCP has been known to break the screenshot (Observer) feature.

## 2. What you'll need before you start

- A PC running Windows 10 or 11, and about 30–45 minutes.
- **A web browser** — Steps 1–7 happen entirely in the browser. Step 8 is a single download in that same browser; Steps 9 onward happen inside the OpenCode app.
- **Accounts and keys for three providers** — DeepSeek, Anthropic, and Google Gemini — plus xAI if you choose to add it. xAI is entirely optional and separate — the Grok agent only exists if you add that key, and nothing else in the setup depends on it. You create these in Steps 1–7.
- **Funded prepaid balances**: DeepSeek, Anthropic, and Google (Gemini) all require you to add money or buy credits before they work. Amounts change — verify in each console when you purchase.
- **A way to store secrets** — a password manager, or a private note. You will keep four API keys in it.
- Optional: a GitHub account (free — it is where you keep your own projects later, and some optional add-ons use it), Ollama (free, no account), a Railway account (only if you deploy to Railway).
- On a budget? See the [Budget path](#budget-path) section at the end of this guide. You still have to fund DeepSeek; the only free allowance is the Gemini free tier, for the Observer.

**Funding is never optional for a provider you set up.** If you create a DeepSeek, Anthropic or Gemini (full setup) account, you must add funds or credits before you use it. Finishing the sign-up is not the same as being able to use the model. The single exception is the Budget plan, below.

## 3. Step 1 — Create a GitHub account

Free, and it takes a minute. You do **not** need it to download anything on this page. It is where you keep your own projects later, and some optional add-ons use it.

1. Go to https://github.com/signup and create an account.
2. Save the password in your password manager — but as its own entry, separate from the provider keys below.

## 4. Step 2 — Create your DeepSeek account and add funds

DeepSeek powers the supervisor and all the junior agents — the bulk of the work.

1. Create an account at https://platform.deepseek.com.
2. Add money to your prepaid balance. DeepSeek is pay-as-you-go with no free tier, so your balance must be funded before the model will answer.
3. Payment methods and minimum top-up amounts are only shown inside the console — verify there when you sign up.

## 5. Step 3 — Create your DeepSeek API key

1. Create your API key: go to https://platform.deepseek.com/api_keys and click "Create API key". Copy the key into your password manager — never paste a key into a chat. OpenCode refers to this key as `DEEPSEEK_API_KEY`, but you will never type that name yourself.
2. You will connect it in Step 10.

## 6. Step 4 — Create your Anthropic account and buy credits

Anthropic's Claude powers most mid-tier agents (except the researcher and planner, which run on Gemini) and all the senior agents.

1. Go to https://platform.claude.com and sign up with Google or an email address.
2. **Buy credits before you start.** Anthropic uses prepaid "usage credits", and the API does not work without them. In the console: Billing -> Buy credits. (New accounts sometimes show a small amount of trial credit — it is not enough to run this setup, so treat buying credits as part of the sign-up.) Credits expire after one year and are non-refundable.

**Warning — do not use the Claude Pro/Max login.** The console also offers a "Claude Pro/Max" sign-in. That is a subscription for chatting in Anthropic's own apps, and Anthropic's rules prohibit using it through tools like OpenCode. Use the API key route only.

On the Budget plan, skip this whole part. Anthropic only powers the mid/senior agents, and you can add the key later in **Settings → Providers** — nothing else needs redoing.

## 7. Step 5 — Create your Anthropic API key

1. Create your API key: https://platform.claude.com/settings/keys -> "Create key". Choose the longest expiration (up to "Never") so you do not have to redo this. Copy it into your password manager.
2. You will connect it in Step 11.

## 8. Step 6 — Create your Google Gemini key and add billing

The Gemini key does two jobs: it lets the Observer read screenshots you paste into the chat, and it powers the Gemini agents (long-context research, the mid researcher and planner, and the gemini-worker).

1. Go to https://aistudio.google.com/apikey, sign in with any Google account, accept the terms, and click "Create API key".
2. **Add billing.** Open the plan/billing settings in AI Studio and set up the paid prepay plan. Google shows the free tier prominently, but the free tier is not part of the full setup — the Gemini mid-tier agents need a funded key. Minimums are only shown at purchase, so check there.
3. Copy the key into your password manager. You will paste it in Step 12 — there is no file to edit.

**Budget plan exception:** the Gemini free tier is acceptable, but only for the Observer (pasted screenshots). It is not a substitute for funding the key, so do not use the Gemini mid-tier researcher/planner agents until the key is funded.

## 9. Step 7 — Create an xAI Grok key (optional)

xAI is entirely optional and separate — the Grok agent only exists if you add that key, and nothing else in the setup depends on it.

1. Console at https://console.x.ai -> API Keys page -> create a key.
2. This step is optional and separate: skip it unless you want Grok models.
3. Copy the key into your password manager. You will connect it in Step 13.

Everything up to here happens in the browser. Everything after it happens in the app; the only typing is your keys and two chat messages.

## 10. Step 8 — Install the OpenCode app

**Everything happens in the app.** From here on there is nothing to type into a terminal — the only typing is your API keys and two chat messages.

This setup is built for **OpenCode 1.19.5**, so download exactly this file:

- **[Download OpenCode 1.19.5 for Windows (64-bit)](https://github.com/anomalyco/opencode/releases/download/v1.19.5/opencode-desktop-win-x64.exe)**
- [Download for Windows on an ARM chip](https://github.com/anomalyco/opencode/releases/download/v1.19.5/opencode-desktop-win-arm64.exe)

Most PCs use the 64-bit link; use the ARM link only if you know your PC has an ARM processor.

Then:

1. Open your Downloads folder and double-click `opencode-desktop-win-x64.exe`.
2. If Windows shows a blue **"Windows protected your PC"** screen, click **More info**, then **Run anyway**. Windows shows this for any program it has not seen before.
3. Let the installer run and finish. It installs for your user account and adds an OpenCode entry to the Start menu.

**Why this download and not the download page?** The download page at opencode.ai/download currently serves **OpenCode 2**, which these config files are not built for. This direct link is the pinned OpenCode 1.19.5 app.

> **Later, the app may offer an OpenCode 2 update.** The app updates itself automatically. Decline any update that says it moves OpenCode to version 2 — these config files are only built for 1.19.5.

## 11. Step 9 — Open the app

Press the Windows key, type "OpenCode", and click the OpenCode entry in the Start menu (or double-click the desktop shortcut if the installer made one).

OpenCode opens its home screen. **From here on you work inside the app** — every step below is a click, a key, or a chat message.

**Verify:** OpenCode's home screen appears.

## 12. Step 10 — Add your DeepSeek key

In the app, open **Settings → Providers**, choose **DeepSeek**, paste the key from Step 3 into its key field, and save.

OpenCode stores the keys itself, in its own credential store. You will never edit a file to add or change a key.

**Verify:** DeepSeek shows as connected in Settings → Providers, with its key filled in. (You re-check the models they unlock in Step 18.)

## 13. Step 11 — Add your Anthropic key

In **Settings → Providers**, choose **Anthropic**, paste the key from Step 5, and save. Do not use the Claude Pro/Max sign-in option — paste an API key (see the warning in Step 4). **Budget plan: skip this.**

**Verify:** Anthropic shows as connected in Settings → Providers.

## 14. Step 12 — Add your Google (Gemini) key

In **Settings → Providers**, choose **Google**, paste the key from Step 6, and save. This replaces the old way of setting an environment variable: the key goes in the same way as the others, and there is no file to create or edit.

**Verify:** Google shows as connected in Settings → Providers.

## 15. Step 13 — Add your xAI key (optional)

In **Settings → Providers**, choose **xAI**, paste your Grok key, and save. Skip if you skipped the Grok key — xAI is not part of this setup unless you add it.

**Verify:** xAI shows as connected in Settings → Providers.

## 16. Step 14 — Pick your default model

Open the **model picker** in the message box, search for `deepseek`, and choose `deepseek/deepseek-flash` — the default brain for the **supervisor**, the agent that plans and delegates.

Do this now: it is what makes your *first* messages work, because the config that sets the default has not been installed yet. If `deepseek/deepseek-flash` is not in the list, DeepSeek is not connected — redo Step 10.

## 17. Step 15 — Ask OpenCode to install the config

You do not copy files, extract ZIPs, or run setup commands. You paste **one message** into OpenCode's message box and let the agent do the work.

Paste this into OpenCode's message box and press Enter:

> Install your own configuration. Clone https://github.com/nigelianarmbuster-cmd/opencode-config.git into your global OpenCode config folder — ~/.config/opencode on macOS, %USERPROFILE%\.config\opencode on Windows — and set up its dependencies. If that folder already exists, keep the files that are already there and add the missing ones. Tell me when it is done.

How it behaves:

- OpenCode runs a few commands on your computer itself, and does not ask before each one. It writes to `%USERPROFILE%\.config\opencode`, which is `C:\Users\<you>\.config\opencode` — the same folder written as `%USERPROFILE%\.config\opencode`.
- It may ask **once** for permission to work outside your project folder. The prompt appears as an app dialog. **Approving:** if the dialog names that config folder, choose **allow**; if it names any other folder, choose **no** and stop.
- It may need `git`. If it asks to install something, read the dialog first and allow it only if it names `git`.
- **Windows Defender Firewall or SmartScreen** may pop up; Windows does this for any program it has not seen before.
- **Stop the run from the app** if anything looks wrong — use the stop button in the app while it is working.
- If the folder already exists and is not empty, the agent adds the missing files instead of failing. If it fails or says "destination path already exists" and stops, ask the app to try again — or ask the person who shared this guide.

## 18. Step 16 — Answer its questions and let it finish

While the config installs, the agent may ask you questions or ask to install `git`.

- Answer its questions in the message box.
- Approve only what it names: the config folder (`%USERPROFILE%\.config\opencode`), or installing `git`. If a dialog names anything else, choose **no**.
- Wait for it to say it is done. Do not close the app while it is working.

**Verify:** the agent tells you the configuration is installed.

## 19. Step 17 — Restart the app

Quit OpenCode and open it again from the Start menu. A restart is what loads the new configuration.

**Verify:** the **agent picker** in the message box lists `supervisor`, and it is the selected agent.

## 20. Step 18 — Check your models in the model picker

Open the **model picker** in the message box and confirm your models are there: `deepseek/deepseek-flash`, plus Anthropic, Google or xAI models for whichever keys you added. (Budget plan: `deepseek/deepseek-flash` plus Gemini models.)

**Verify:** every provider you set up has its models listed. If a provider's models are missing, re-add that key in **Settings → Providers** (Step 10 for DeepSeek, Step 11 for Anthropic, Step 12 for Google), then restart the app.

## 21. Step 19 — Run a first smoke test

Ask something simple in the message box:

> What is 2+2?

**Verify:** a normal answer comes back, which means everything is wired up.

An error here means the model is not connected — redo Step 10 (add the DeepSeek key), then restart the app (Step 17). To leave OpenCode, close the app or start a new chat.

If you are on the Budget plan, tell the supervisor: **"Use the budget instructions."** This switches it into budget mode: it sticks to the junior-tier (DeepSeek) agents and never calls the paid mid/senior tiers. The full production setup stays installed and untouched.

## 22. Optional extras

### Ollama — free local models (no account, no key)

Ollama runs AI models on your own PC — free, private, and offline. It is a good way to try local models without spending anything, and it is the only part of this setup where "free" replaces a paid provider. Which model fits your machine depends on its memory and graphics card, so this setup does not assume everyone has the same hardware.

1. Download the Windows installer from https://ollama.com/download and run it.
2. Let the setup pick the right model for your machine. In the OpenCode chat, ask: **"Set up local models that fit my machine."** The supervisor agent will check your PC's memory, graphics card, and free disk space, then download a model that fits — a small one on modest hardware, a larger one only if your machine can run it comfortably. It downloads **one model by default** — plenty to try things out. If you later want a bigger model, just ask for it in the chat.
3. The config already includes the Ollama provider (it points at `http://localhost:11434/v1`), so pulled models appear in the model picker automatically. Local models are slower and less capable than the cloud ones — that is the trade-off. They are a try-out, not a replacement for the funded cloud providers.

### Railway deployments (CLI)

Railway deployments work through the Railway CLI — no add-on tool needed. Install the Railway CLI, run `railway login`, then use `railway up` or `railway deploy` from a terminal. The config also contains a Railway MCP server, but leave it switched off: enabling it has been known to break the screenshot (Observer) feature, and the CLI covers the same ground. If you do not use Railway, just ignore it.

### GitHub integration (optional)

The config also includes a GitHub tool (MCP server) that is switched off. Turning it on lets OpenCode read and work with your GitHub repositories directly. It needs Docker and a GitHub token:

1. Create a personal access token: on GitHub, go to Settings -> Developer settings -> Personal access tokens -> Tokens (classic) -> Generate new token (classic), tick the `repo` scope, generate, and copy it.
2. Open `opencode.json` in a text editor (right-click -> Open with -> Notepad). Find the `github` entry under `mcp` and change `"enabled": false` to `"enabled": true`, and replace `"YOUR_GITHUB_TOKEN"` with your token. Save the file.
3. Restart OpenCode.

Docker (https://www.docker.com) is a heavy install — only do this if you actually want OpenCode to work with GitHub.

## 23. Learn how to customize your OpenCode config

Nothing to do now. Your setup ships with extra add-on tools called **MCP servers**. All of them are **dormant** — declared in the config but switched off — so they cost nothing and do nothing until you turn one on.

**Why they are off, and how to turn one on.** Every enabled MCP server loads at startup and adds tokens to each conversation, and some pull down packages or need their own accounts and keys. The core setup needs none of them, so they ship dormant — switch on exactly what you use.

To enable one: open `opencode.json` in your config folder (`%USERPROFILE%\.config\opencode`), find the tool's name under `"mcp"`, change `"enabled": false` to `"enabled": true`, save, and restart OpenCode. To check it is live, ask the agent: **"List the MCP servers and which ones are enabled."**

## 24. Costs and safety

- **Everything here is prepaid**: you add money, and usage is deducted from your balance. All three providers' consoles show your usage — check occasionally.
- **Funding is required, not optional.** DeepSeek, Anthropic and Google (Gemini, full setup) each need a funded balance before they work. Finishing sign-up without funds leaves that agent tier unusable.
- **API keys are stored by OpenCode itself** in its own credential store — not in this repo, and not in a file you edit. Never paste a key into a chat message, never share screenshots of them, and never put them into files you share, upload, or commit to git. If a key leaks, revoke it and make a new one in the console.
- **The agent can run commands on your computer** when you paste an instruction — Step 15 is the main one here. The commands do not prompt individually. You may be asked once to allow work in your home config folder (that is the only folder it needs here); if any other folder is named, choose **no**. You can stop the run from the app.
- **Amounts change — verify in each console when you purchase.** Anthropic credits expire after one year and are non-refundable.

## 25. Troubleshooting

| Symptom | Fix |
|---|---|
| "model not found" or a model error | Usually a key or funding problem. Open **Settings → Providers** and check that the provider's key is there, then open the model picker to confirm the model is listed. Check the provider's console that your prepaid balance is funded. |
| The app will not open, or SmartScreen blocked the installer | If Windows showed **"Windows protected your PC"**, open the downloaded `opencode-desktop-win-x64.exe` again, click **More info**, then **Run anyway**. If it still will not install, restart the computer and run the installer again. |
| A provider key does not stick in Settings → Providers | Re-copy the key from your password manager — a missing character or a trailing space is the usual cause — and save it again. Make sure the key belongs to the provider you selected: an Anthropic key in the Google entry will not work. |
| A model is missing from the picker | The key for that provider is missing or wrong. Re-add it in **Settings → Providers** (Step 10 for DeepSeek, Step 11 for Anthropic, Step 12 for Google), then restart the app. |
| The app offers an OpenCode 2 update | **Decline it.** These config files are built for OpenCode 1.19.5, and OpenCode 2 is not compatible. Keep using the installed app and dismiss the update prompt. |
| Screenshots return nothing, or the Observer stays silent | The Gemini key is not reaching OpenCode. Open **Settings → Providers** and confirm the **Google** key is filled in; if it is missing, re-add it (Step 12). Also check no stale Google/Gemini environment variable is set — an environment variable overrides the saved key, so an old one makes every Gemini call fail with "API key not valid". Check with `Get-ChildItem Env: \| Where-Object Name -match 'GOOGLE\|GEMINI'`. If anything appears, remove it with `[Environment]::SetEnvironmentVariable('NAME', $null, 'User')`, then restart the app. |
| Gemini agents fail or behave oddly in the Budget plan | In Budget the free tier is for pasted screenshots only; add billing to the Google key before using the Gemini researcher or planner. |
| The agent picker does not show `supervisor` | The config did not install. Redo Step 15 (ask OpenCode to install the config), answer any questions it asks (Step 16), and restart the app (Step 17). |
| No supervisor, or the agent team is missing | The config did not load. Redo Step 15 (ask OpenCode to install the config) and restart the app (Step 17). The agent picker should then list `supervisor`. |
| A permission dialog names a folder you do not recognise | Choose **no**. The only folder this setup needs is `%USERPROFILE%\.config\opencode`. |
| Railway errors after you enable it | Use the Railway CLI instead of the MCP (see the Railway section). If OpenCode misbehaves with the Railway MCP on — for example the Observer returns empty screenshot results — set `mcp.railway` back to `"enabled": false` and restart. |

If something is still not right, ask the person who shared this guide — they have the same setup.

---

## Budget path (alternative setup)

Running on a budget? Fund DeepSeek, and use the Gemini free tier for the Observer only. The core of the setup — the supervisor and all the junior agents (V4.1 Flash) — runs entirely on DeepSeek, so you get the full day-to-day workflow for the price of one prepaid balance.

Funding rules on the Budget plan:

- **DeepSeek still needs funds.** It runs everything that matters, so its balance is not optional.
- **The Gemini free tier is acceptable — for the Observer (pasted screenshots) only.** Do not use the Gemini mid-tier researcher/planner agents until you fund the key; they are part of the full setup, not the budget one. Local models via Ollama are free too, but they are a try-out, not a substitute for the paid tiers.
- **Anthropic is skipped, not made free.** The Claude mid-tier and senior agents (Sonnet 5 / Opus 4.8) stay configured but inactive until you add a funded Anthropic key later.
- **xAI is skipped.** It is not part of this setup unless you add it; Grok models remain an unused optional extra.

Steps:

1. Follow Steps 1–3 as written, then skip Steps 4–5 (Anthropic) and do Steps 6–7 (Gemini, and xAI only if you choose to add it). For Gemini, the free tier is fine for the Observer.
2. Follow Steps 8–19 as written. In Steps 10–13, add the DeepSeek and Google keys in **Settings → Providers**; skip Anthropic and xAI. In Step 14, pick `deepseek/deepseek-flash`. Then finish Step 19 by telling OpenCode **"Use the budget instructions."** The supervisor sticks to junior-tier (DeepSeek) agents and won't call the paid tiers.
3. **Upgrade later without redoing anything:** follow Steps 4–5 to create a funded Anthropic key (and Step 7 if you choose to add xAI), add it in **Settings → Providers** (Steps 11–13) and redo Step 14 if needed, then tell OpenCode **"Switch to the full production setup"** — everything is already configured and waiting.