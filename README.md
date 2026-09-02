# Agent Orchestration

Beginner-friendly setup guides for mirroring an OpenCode agent-orchestration setup (a supervisor that delegates to ~44 tiered AI agents) in your own OpenCode instance. Written for first-time AI users.

**Two entry points, in order:**

1. **`index.html` — the "why"** (a beginner's first stop): what the Supervisor agent-orchestration setup is, how the agents work together, and best practices. Live at https://nigelianarmbuster-cmd.github.io/agent-orchestration/ (the site root)
2. **`setup.html` — the "how"**: the interactive step-by-step checklist of every user action, with links to every account/key page, a macOS/Windows toggle, and a Full production / Budget plan toggle. Live at https://nigelianarmbuster-cmd.github.io/agent-orchestration/setup.html

The guides below are the detailed source of truth.

**The configuration files themselves live in a separate public repo:**
https://github.com/nigelianarmbuster-cmd/opencode-config
(Clone it into `~/.config/opencode` / `%USERPROFILE%\.config\opencode`, then run `npm install`.)

## Guides

| Guide | Audience | OS specifics |
|---|---|---|
| `index.html` | Everyone — the "why": what the setup is and how it works | Both (no OS-specific content) |
| `setup.html` | Everyone — the "how": interactive step-by-step setup checklist | Both (built-in macOS/Windows toggle) |
| `OPENCODE-SETUP-MACOS.md` | First-time AI users on macOS | Terminal / bash, `~/.config/opencode` |
| `OPENCODE-SETUP-WINDOWS.md` | First-time AI users on Windows | PowerShell, `%USERPROFILE%\.config\opencode` |

Both guides cover: install OpenCode → download the config (ZIP or `git clone`) → create provider accounts (DeepSeek, Anthropic, Google Gemini) → add prepaid funds → connect API keys (`/connect` + `.env`) → first-run test → optional extras (Ollama local models, Railway via CLI, GitHub MCP) → costs and safety → troubleshooting.

## The setup, in one paragraph

Supervisor default agent + ~44 tiered agents across three providers: DeepSeek (supervisor on V4 Pro, juniors + role-mules on V4 Flash — the workhorse), Claude (mid-tier + senior agents on Sonnet 5 / Opus 4.8 — quality gates), and Gemini 3.7 Flash (mid researcher/planner, observer, gemini-worker, gemini-mule — research, planning, and vision), plus Ollama (local, free — model chosen per machine, see below). Effort is tiered by policy: juniors low, mid/senior high, mules thinking-off. The `observer-bridge.js` plugin handles pasted screenshots by auto-spawning the @observer agent (needs a Gemini key via `GEMINI_API_KEY`). All MCP servers (playwright, chrome-devtools, elevenlabs, yt-dlp, vercel, gemini-api-docs, context7, github, macos-use, railway) are present but **disabled by default** — dormant by design, enabled per-user. GitHub is documented as an optional add-on in the guides; Railway deployments use the Railway CLI directly — the Railway MCP server stays off because enabling it has been found to break the Gemini Observer; the explainer's "Learn how to customize your OpenCode config" section catalogs the rest, explains activation, and links a term dictionary.

## Local model selection (supervisor-driven)

The setup does not impose a fixed set of local models. The original machine's models are not pushed on anyone — most of them are too big for typical machines. Instead, the **supervisor agent evaluates each machine** (RAM, GPU/VRAM, free disk) and pulls the best-fitting Ollama model: small models on modest hardware, larger ones only when the machine can run them comfortably. The sizing table and the OS-specific measurement commands live in the supervisor agent's "Local Model Selection" section (in the `opencode-config` repo, `agents/supervisor.md`).

**One model by default.** The supervisor downloads a single model — the smallest that fits the task — and never "everything that fits": each model is 1–20 GB, and local models are an optional test tier, not the main setup. A second, larger model is pulled only when the user explicitly asks (max two without asking).

Users trigger this by asking the supervisor in OpenCode: **"Set up local models that fit my machine."** The baseline local agent is `local-coder` (qwen2.5-coder:7b, ~4.7 GB) — the supervisor drops to 1.5b/3b models on weak machines and may go up to 14b/30b only on capable ones.

## Status

- Guides written Aug 2026. Provider URLs and signup flows verified Aug 2026 against official docs; dollar amounts intentionally not hard-coded ("verify at purchase").
- Aug 2026: local models are now chosen per machine by the supervisor agent (machine-capability evaluation), replacing the original machine's fixed model recommendations. Guides, README, and `agents/supervisor.md` all updated.
- Aug 2026: GitHub Pages enabled on this repo — the explainer is live at https://nigelianarmbuster-cmd.github.io/agent-orchestration/ and auto-updates on every push to `main`.
- Aug 2026: **Budget mode** added — DeepSeek-only path (fund DeepSeek; optional free Gemini key keeps the screenshot feature). The explainer has a Full production / Budget toggle; both guides document the budget path; the supervisor honors the "Use the budget instructions" prompt and the full production setup stays configured for later upgrades (no re-setup).
- Aug 2026: **Railway MCP now dormant by default** like all other MCPs; the setup checklist gains a collapsed-by-default "Learn how to customize your OpenCode config" section (dormant MCP catalog with one-line explanations, activation steps + rationale, AI Coding Dictionary link, where to find more MCPs); supervisor.md tells the models the MCPs are dormant and how to activate them.
- Aug 2026: the maintainer's live setup briefly re-enabled Railway via the `OPENCODE_CONFIG` env var pointing at a personal override file (`~/.config/opencode-personal.json`, outside the repo) — this was later reverted (see Railway retirement below); the repo itself stays at the beginner default.
- Aug 2026: entry points finalized — the "why" primer is now `index.html` at the site root; the "how" checklist moved to `setup.html`; its two CTA links retargeted to the setup page.
- The `opencode-config` repo is the source of truth for agent files. These guides are prose — update them when the setup changes materially (new providers, renamed agents, changed auth flow).
- The live source config is tracked as a git repo at `~/.config/opencode` (branch `main`); `node_modules` is gitignored, `package.json`/`package-lock.json` tracked so friends can `npm install` the plugin dependency.
- Aug 2026: **Railway MCP retired from the live setup** — enabling it was found to break the Gemini Observer (pasted screenshots returned empty results). Railway now uses the Railway CLI (`railway login`, `railway up`) with no MCP server; the guides document Railway as CLI-first.
