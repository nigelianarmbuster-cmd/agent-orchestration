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

Supervisor default agent + ~43 tiered agents: DeepSeek V4 Pro (supervisor, juniors, mules), Claude Sonnet 5 (mid tier), Claude Opus 4.8 (senior tier), Gemini flash/pro (research, multimodal), Ollama (local, free). The `observer-bridge.js` plugin handles pasted screenshots by auto-spawning the @observer agent (needs a Gemini key via `GEMINI_API_KEY`). Railway MCP is enabled; all other MCP servers are present but disabled by default (GitHub MCP is documented as an optional add-on).

## Status

- Guides written Aug 2026. Provider URLs and signup flows verified Aug 2026 against official docs; dollar amounts intentionally not hard-coded ("verify at purchase").
- The `opencode-config` repo is the source of truth for agent files. These guides are prose — update them when the setup changes materially (new providers, renamed agents, changed auth flow).
- The live source config is tracked as a git repo at `~/.config/opencode` (branch `main`); `node_modules` is gitignored, `package.json`/`package-lock.json` tracked so friends can `npm install` the plugin dependency.
