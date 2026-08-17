# Agent Orchestration

Beginner-friendly setup guides for mirroring an OpenCode agent-orchestration setup (a supervisor that delegates to ~43 tiered AI agents) in your own OpenCode instance. Written for first-time AI users.

**The configuration files themselves live in a separate public repo:**
https://github.com/nigelianarmbuster-cmd/opencode-config
(Clone it into `~/.config/opencode` / `%USERPROFILE%\.config\opencode`, then run `npm install`.)

## Guides

| Guide | Audience | OS specifics |
|---|---|---|
| `OPENCODE-SETUP-MACOS.md` | First-time AI users on macOS | Terminal / bash, `~/.config/opencode` |
| `OPENCODE-SETUP-WINDOWS.md` | First-time AI users on Windows | PowerShell, `%USERPROFILE%\.config\opencode` |

Both guides cover: install OpenCode → download the config (ZIP or `git clone`) → create provider accounts (DeepSeek, Anthropic, Google Gemini) → add prepaid funds → connect API keys (`/connect` + `.env`) → first-run test → optional extras (Ollama local models, Railway MCP, GitHub MCP) → costs and safety → troubleshooting.

## The setup, in one paragraph

Supervisor default agent + ~43 tiered agents: DeepSeek V4 Pro (supervisor, juniors, mules), Claude Sonnet 5 (mid tier), Claude Opus 4.8 (senior tier), Gemini flash/pro (research, multimodal), Ollama (local, free — model chosen per machine, see below). The `observer-bridge.js` plugin handles pasted screenshots by auto-spawning the @observer agent (needs a Gemini key via `GEMINI_API_KEY`). Railway MCP is enabled; all other MCP servers are present but disabled by default (GitHub MCP is documented as an optional add-on).

## Local model selection (supervisor-driven)

The setup does not impose a fixed set of local models. The original machine's models are not pushed on anyone — most of them are too big for typical machines. Instead, the **supervisor agent evaluates each machine** (RAM, GPU/VRAM, free disk) and pulls the best-fitting Ollama model: small models on modest hardware, larger ones only when the machine can run them comfortably. The sizing table and the OS-specific measurement commands live in the supervisor agent's "Local Model Selection" section (in the `opencode-config` repo, `agents/supervisor.md`).

Users trigger this by asking the supervisor in OpenCode: **"Set up local models that fit my machine."** The baseline local agent is `local-coder` (qwen2.5-coder:7b, ~4.7 GB) — the supervisor drops to 1.5b/3b models on weak machines and may go up to 14b/30b only on capable ones.

## Status

- Guides written Aug 2026. Provider URLs and signup flows verified Aug 2026 against official docs; dollar amounts intentionally not hard-coded ("verify at purchase").
- Aug 2026: local models are now chosen per machine by the supervisor agent (machine-capability evaluation), replacing the original machine's fixed model recommendations. Guides, README, and `agents/supervisor.md` all updated.
- The `opencode-config` repo is the source of truth for agent files. These guides are prose — update them when the setup changes materially (new providers, renamed agents, changed auth flow).
- The live source config is tracked as a git repo at `~/.config/opencode` (branch `main`); `node_modules` is gitignored, `package.json`/`package-lock.json` tracked so friends can `npm install` the plugin dependency.
