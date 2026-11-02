# OpenCode Setup Guide (Windows)

Welcome. This guide walks you through setting up OpenCode on Windows, step by step. OpenCode is a free, open-source program that runs in PowerShell and gives you a team of AI assistants that can read your files, write code, and run commands on your computer. This guide assumes you have never used an AI coding tool before. Follow the steps in order, and copy-paste commands exactly. Use PowerShell throughout — not the older Command Prompt (cmd), which uses different syntax.

## A few words you will see

- **PowerShell** — a built-in Windows app where you type text commands instead of clicking buttons. To open it: press the Windows key, type "PowerShell", press Enter. (The newer "Terminal" app also works — it runs PowerShell inside.)
- **API key** — a secret password that lets a program use your account (and spend your balance). Treat it like a password.
- **Prepaid balance** — money you add to a provider's console in advance. Usage is deducted from it. Every provider in this setup works this way: you cannot use a provider until you have funded it.
- **Model** — the actual AI "brain" that does the thinking. Different companies make different models; each has a name like `deepseek/deepseek-flash`.
- **API vs subscription** — a subscription (like ChatGPT Plus or Claude Pro) lets you chat inside a company's own app. API access is a separate pay-as-you-go balance that other programs, like OpenCode, can use. A subscription does not include API access.
- **Prompt marker (`PS>`)** — commands sometimes start with `PS>`, which just marks where to paste: open PowerShell, paste the command after `PS>`, press Enter. You never type the `PS>` itself — only the command after it. The setup page's copy buttons leave the marker off automatically.

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
- **A web browser** — Steps 1–7 happen entirely in the browser. No programming tools, no Node.js, nothing to install yet.
- **Accounts and keys for four providers** — DeepSeek, Anthropic, Google Gemini, and optionally xAI. You create these in Steps 1–7.
- **Funded prepaid balances**: DeepSeek, Anthropic, and Google (Gemini) all require you to add money or buy credits before they work. xAI does too, if you use it. Amounts change — verify in each console when you purchase.
- **A way to store secrets** — a password manager, or a private note. You will keep four API keys in it.
- Optional: a GitHub account (free — it is where you keep your own projects later, and some optional add-ons use it), Ollama (free, no account), a Railway account (only if you deploy to Railway).
- On a budget? See the [Budget path](#budget-path) section at the end of this guide. You still have to fund DeepSeek; the only free allowance is the Gemini free tier, for the Observer.

**Funding is never optional for a provider you set up.** If you create a DeepSeek, Anthropic, Gemini (full setup) or xAI account, you must add funds or credits before you use it. Finishing the sign-up is not the same as being able to use the model. The single exception is the Budget plan, below.

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

On the Budget plan, skip this whole part. Anthropic only powers the mid/senior agents, and you can add the key later with `/connect` — nothing else needs redoing.

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

Only if you want Grok models — nothing in this setup requires them.

1. Console at https://console.x.ai -> add prepaid credits -> API Keys page -> create a key.
2. As with every other provider, the credits must be funded before any Grok model will answer. Minimums are shown at purchase.
3. Copy the key into your password manager. You will connect it in Step 13.

Everything up to here happens in the browser. From here on, you type into PowerShell once for the install, once to start OpenCode, and after that into OpenCode's chat box.

## 10. Step 8 — Install OpenCode

**Two terminal pastes, then plain typing.** You only paste into PowerShell **twice** — once to install OpenCode (Step 8) and once to start it (Step 9). Everything after that is typed into OpenCode's chat box, including the commands that start with `/`.

This setup is built for **OpenCode 1.19.5**, so use exactly this command. Open PowerShell (press the Windows key, type "PowerShell", press Enter) and run:

```text
PS> winget install --id SST.opencode -e
```

**Node.js is not required.** The installer is a self-contained program — that one paste is all it takes. OpenCode installs its own plugin dependencies later, so you never run `npm` or install Node.js at any point.

Then check it worked. **Close PowerShell and open a new window first** — Windows only refreshes its list of programs in a new window, so the `opencode` command will not be found in the old one:

```text
PS> opencode --version
```

You should see `1.19.5`.

**If Windows says `winget` is not recognised**, install **App Installer** from the Microsoft Store, then run the install command again.

**If the version is not 1.19.5**, you have an OpenCode 2 install. Do **not** use `scoop install opencode2`, and do not use the download button on opencode.ai — both install OpenCode 2, which these config files are not built for. OpenCode 1's installer refuses version 2 on purpose. Uninstall OpenCode 2, then repeat Step 8. The exact release this guide targets: https://github.com/anomalyco/opencode/releases/tag/v1.19.5

## 11. Step 9 — Start OpenCode

In the same PowerShell window:

```text
PS> opencode
```

OpenCode opens its own chat screen. **That was the last terminal paste in this guide.** From now on you type in OpenCode's chat box — including commands that start with `/`.

**Verify:** OpenCode's chat screen appears.

## 12. Step 10 — Connect DeepSeek

Type `/connect` in OpenCode's chat box, choose **DeepSeek**, choose **API key**, paste the key from Step 3, press Enter.

OpenCode remembers keys itself, in its own credential store. You will never edit a file to add or change a key.

**Verify:** OpenCode lists DeepSeek as connected. (You re-check them all in Step 18.)

## 13. Step 11 — Connect Anthropic

Type `/connect` again, choose **Anthropic**, choose **"Manually enter API Key"**, and paste the key from Step 5. Do not pick the Pro/Max option (see the warning in Step 4). **Budget plan: skip this.**

**Verify:** OpenCode lists Anthropic as connected.

## 14. Step 12 — Connect Google (Gemini)

Type `/connect` again, choose **Google**, choose **API key**, and paste the key from Step 6. This replaces the old way of setting an environment variable: the key goes in the same way as the others, and there is no file to create or edit.

**Verify:** OpenCode lists Google as connected.

## 15. Step 13 — Connect xAI (optional)

Type `/connect` again, choose **xAI**, and paste your Grok key. Skip if you skipped the Grok key.

**Verify:** OpenCode lists xAI as connected.

## 16. Step 14 — Pick the default model

Type `/models`, search for `deepseek`, and choose `deepseek/deepseek-flash` — the default brain for the **supervisor**, the agent that plans and delegates.

Do this now: it is what makes your *first* messages work, because the config that sets the default has not been installed yet. If `deepseek/deepseek-flash` is not listed, DeepSeek is not connected — redo Step 10.

## 17. Step 15 — Ask OpenCode to install the config

You do not copy files, extract ZIPs, or run setup commands. You paste **one message** into OpenCode's chat box and let the agent do the work.

Paste this into OpenCode's chat box and press Enter:

> Install your own configuration. Clone https://github.com/nigelianarmbuster-cmd/opencode-config.git into your global OpenCode config folder — ~/.config/opencode on macOS, %USERPROFILE%\.config\opencode on Windows — and set up its dependencies. If that folder already exists, keep the files that are already there and add the missing ones. Tell me when it is done.

How it behaves:

- OpenCode runs a few commands on your computer itself, and does not ask before each one. It writes to `%USERPROFILE%\.config\opencode`, which is `C:\Users\<you>\.config\opencode` — the same folder written as `%USERPROFILE%\.config\opencode`.
- It may ask **once** for permission to work outside your project folder. **Approving:** if the prompt names that config folder, choose **allow**; if it names any other folder, choose **no** and stop.
- It may need `git`. If it asks to install something, read the prompt first and allow it only if it names `git`.
- **Windows Defender Firewall or SmartScreen** may pop up; Windows does this for any program it has not seen before.
- **Ctrl+C stops it** if anything looks wrong.
- If the folder already exists and is not empty, the agent adds the missing files instead of failing. If it says "destination path already exists" and stops, run the manual fallback below.

**Manual fallback (only if you would rather do it yourself).** In PowerShell:

```text
PS> git clone https://github.com/nigelianarmbuster-cmd/opencode-config.git "$env:USERPROFILE\.config\opencode"
```

`$env:USERPROFILE` is PowerShell's way of writing your user folder (for example `C:\Users\nigel`). If `git` is "not recognized", let OpenCode do it as above instead — you do not need git installed yourself.

## 18. Step 16 — Restart OpenCode

Type `/exit` in the chat box, then run `opencode` again in PowerShell. (Or close PowerShell, open a new window, and run `opencode`.)

**Verify:** the agent name shown at the bottom of the OpenCode window reads `supervisor`, and pressing **Tab** cycles through the primary agents. There is no `/agents` command in OpenCode 1 — Tab is how you look at the team.

## 19. Step 17 — Confirm the team is loaded

Ask the supervisor, in the chat box: **"List your subagents by tier."**

**Verify:** the junior, mid and senior groups are listed, plus the mule agents.

## 20. Step 18 — Check your connections

Ask the supervisor — it runs the check for you:

> Run opencode auth list and tell me which providers are connected.

**Verify:** `deepseek` is listed, plus `anthropic`, `google` or `xai` for whichever ones you connected. (Budget plan: `deepseek` plus `google`.) If one is missing, connect it again with `/connect` (Step 10 for DeepSeek, Step 11 for Anthropic, Step 12 for Google).

Prefer to check yourself? Run this in PowerShell:

```text
PS> opencode auth list
```

`opencode auth list` shows which providers are connected; `opencode auth logout` removes one.

## 21. Step 19 — Run a first smoke test

Ask something simple in the chat box:

> What is 2+2?

**Verify:** a normal answer comes back, which means everything is wired up.

An error here means the model is not connected — redo Step 10 (connect DeepSeek), then start OpenCode again (Step 16). To leave OpenCode, press Ctrl+C (twice if it is busy).

If you are on the Budget plan, tell the supervisor: **"Use the budget instructions."** This switches it into budget mode: it sticks to the junior-tier (DeepSeek) agents and never calls the paid mid/senior tiers. The full production setup stays installed and untouched.

## 22. Optional extras

### Ollama — free local models (no account, no key)

Ollama runs AI models on your own PC — free, private, and offline. It is a good way to try local models without spending anything, and it is the only part of this setup where "free" replaces a paid provider. Which model fits your machine depends on its memory and graphics card, so this setup does not assume everyone has the same hardware.

1. Download the Windows installer from https://ollama.com/download and run it.
2. Let the setup pick the right model for your machine. In the OpenCode chat, ask: **"Set up local models that fit my machine."** The supervisor agent will check your PC's memory, graphics card, and free disk space, then download a model that fits — a small one on modest hardware, a larger one only if your machine can run it comfortably. It downloads **one model by default** — plenty to try things out. If you later want a bigger model, just ask for it in the chat.
3. The config already includes the Ollama provider (it points at `http://localhost:11434/v1`), so pulled models appear in `/models` automatically. Local models are slower and less capable than the cloud ones — that is the trade-off. They are a try-out, not a replacement for the funded cloud providers.

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

To enable one: open `opencode.json` in your config folder (`%USERPROFILE%\.config\opencode`), find the tool's name under `"mcp"`, change `"enabled": false` to `"enabled": true`, save, and restart OpenCode. Check it is live with:

```text
PS> opencode mcp list
```

## 24. Costs and safety

- **Everything here is prepaid**: you add money, and usage is deducted from your balance. All three providers' consoles show your usage — check occasionally.
- **Funding is required, not optional.** DeepSeek, Anthropic and Google (Gemini, full setup) each need a funded balance before they work; xAI does too if you set it up. Finishing sign-up without funds leaves that agent tier unusable.
- **API keys are stored by OpenCode itself** in its own credential store — not in this repo, and not in a file you edit. Never paste a key into a chat message, never share screenshots of them, and never put them into files you share, upload, or commit to git. If a key leaks, revoke it and make a new one in the console.
- **The agent can run commands on your computer** when you paste an instruction — Step 15 is the main one here. The commands do not prompt individually. You may be asked once to allow work in your home config folder (that is the only folder it needs here); if any other folder is named, choose **no**. Ctrl+C stops the agent.
- **Amounts change — verify in each console when you purchase.** Anthropic credits expire after one year and are non-refundable.

## 25. Troubleshooting

| Symptom | Fix |
|---|---|
| "model not found" or a model error | Usually a key or funding problem. Run `opencode auth list` (or ask the supervisor) to see which providers are connected, then re-paste the key with `/connect`. Check the provider's console that your prepaid balance is funded. |
| `npm: command not found` or missing-module errors during setup | Not expected in this setup — start again from Step 8 with the winget command. Do not try to install Node.js to fix it: this setup does not use Node.js. |
| `winget` is "not recognized" | Install **App Installer** from the Microsoft Store, then run `winget install --id SST.opencode -e` again. |
| `opencode` is "not recognized" after installing | Close PowerShell completely and open a new window (Windows refreshes its program list only in new windows). Restart the computer if it still fails. |
| `opencode --version` shows 2.x | You have OpenCode 2, which this config is not built for. Do not install from `scoop install opencode2` or opencode.ai/download. Uninstall it and repeat Step 8. |
| Syntax errors or "unexpected token" | Make sure you are in PowerShell, not Command Prompt, and copy the commands from this guide exactly — including `$` and any quotes. |
| Pasted screenshots are ignored, or observer errors | The Gemini key is missing, or `google` is not listed in `opencode auth list`. Re-paste the key with `/connect` -> Google -> API key (Step 12). There is no file to check. |
| The agent name at the bottom is not `supervisor` | The config did not install. Repeat Step 15, then `/exit` and run `opencode` again. |
| The installer prompt asks about a folder you do not recognise | Choose **no** and stop. The only folder Step 15 needs is `%USERPROFILE%\.config\opencode`. |
| Railway errors after you enable it | Use the Railway CLI instead of the MCP (see the Railway section). If OpenCode misbehaves with the Railway MCP on — for example the Observer returns empty screenshot results — set `mcp.railway` back to `"enabled": false` and restart. |

If something is still not right, ask the person who shared this guide — they have the same setup.

---

## Budget path (alternative setup)

Running on a budget? Fund DeepSeek, and use the Gemini free tier for the Observer only. The core of the setup — the supervisor and all the junior agents (V4.1 Flash) — runs entirely on DeepSeek, so you get the full day-to-day workflow for the price of one prepaid balance.

Funding rules on the Budget plan:

- **DeepSeek still needs funds.** It runs everything that matters, so its balance is not optional.
- **The Gemini free tier is acceptable — for the Observer (pasted screenshots) only.** Do not use the Gemini mid-tier researcher/planner agents until you fund the key; they are part of the full setup, not the budget one. Local models via Ollama are free too, but they are a try-out, not a substitute for the paid tiers.
- **Anthropic is skipped, not made free.** The Claude mid-tier and senior agents (Sonnet 5 / Opus 4.8) stay configured but inactive until you add a funded Anthropic key later.
- **xAI is skipped.** Grok models remain an unused optional extra.

Steps:

1. Follow Steps 1–3 as written, then skip Steps 4–5 (Anthropic) and do Steps 6–7 (Gemini, and xAI only if you want it). For Gemini, the free tier is fine for the Observer.
2. Follow Steps 8–19 as written. In Steps 10–13, connect DeepSeek and Google; skip Anthropic and xAI. In Step 14, pick `deepseek/deepseek-flash`. Then finish Step 19 by telling OpenCode **"Use the budget instructions."** The supervisor sticks to junior-tier (DeepSeek) agents and won't call the paid tiers.
3. **Upgrade later without redoing anything:** follow Steps 4–5 to create a funded Anthropic key (and Step 7 if you want xAI), connect it with `/connect` (Steps 11–13) and redo Step 14 if needed, then tell OpenCode **"Switch to the full production setup"** — everything is already configured and waiting.
