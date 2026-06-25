# ContextGuard

**The linter for your AI configuration.**

Each agent has config it may read on every request — Claude Code has CLAUDE.md, Cursor has `.cursorrules`, Copilot has its instruction files, MCP servers add tool schemas — and nothing reviews that layer. When an agent ignores a rule, you blame the model, not the 6,000-token CLAUDE.md where that rule is buried under contradictions and a duplicated style guide.

ContextGuard is ESLint for that layer. Deterministic rules catch oversized instruction files, unmeasured MCP overhead, secrets in AI-visible files, and token bloat — each with the monthly cost it quietly adds. It also exports clean, paste-ready context bundles for any web AI. Local-first: your code never leaves your machine during scans.

**30 seconds to value:** open a project. If it has any AI instruction or MCP config, the status bar shows the auto-loaded maximum across tools, plus the monthly cost estimate. Click it to open the inspector.

<p align="center">
  <img src="https://raw.githubusercontent.com/BearInBlue/contextguard/main/assets/workplace_scan.gif" alt="ContextGuard workspace scan showing lint, bundle, and Free vs Pro panels" width="780">
</p>

_Screenshots use real projects where it matters: Cline examples are from the actual open-source Cline repo; secret-finding examples use a local test repo._

---

## Quick Start

1. Install and open a project. The status bar (bottom right) shows your auto-injected token baseline and monthly cost.
2. Click the ContextGuard icon in the Activity Bar.
3. Click **Workspace** to lint your AI config, or **Tabs** to see the current session's context.
4. Fix the warnings, or select files and export a clean bundle.

---

## What drifts, and why it matters

Every AI tool brings its own project config, and that config rots:

- Instruction files grow until they tax every request, unnoticed
- CLAUDE.md and `.cursorrules` start contradicting each other once you use more than one tool
- MCP servers add schema overhead nobody has measured
- Secrets land in files agents happily read — even gitignored ones
- Handing context to a web AI means hand-picking files and stripping noise, again and again

ContextGuard handles all of it locally, with deterministic rules. No AI required to lint your AI.

---

## Lint: ESLint for AI context

Scan your active file, open tabs, or workspace (Pro adds PR Review and Branch Diff). Every warning is a rule, not a guess:

| Rule | What it catches |
|---|---|
| Large / Huge Instruction | AI instruction files over 1.5k–6k tokens that tax every request |
| Duplicate Context / Rule *(Pro)* | Repeated Markdown context or the same rule across CLAUDE.md, `.cursorrules`, AGENTS.md, … |
| Conflicting Rule *(Pro)* | "Use tabs" in one file, "use spaces" in another, with Jump To navigation |
| Sensitive | `.env`, `*.pem`, `*.key`, keystores, Terraform state, and secret-like content (API keys, JWTs, DB URLs) in source files |
| MCP Unknown | MCP configs whose runtime schema overhead isn't counted anywhere |
| Large / Huge File | Files over 3k / 8k tokens that would dominate a request |
| Generated / Noisy | `dist/`, lockfiles, logs, snapshots: high-token, low-signal context |
| No Test Changes *(Pro review scans)* | Git diff touches source files but no tests |

**One registry, every convention.** ContextGuard detects 21+ conventions — Claude Code, AGENTS.md, Cursor, Copilot, Cline, Windsurf, Roo Code, Gemini, Codex CLI, Aider, Zed, Continue, JetBrains Junie, Amazon Q, Kilo Code, Goose, OpenHands, Amp, Augment Code, Firebase Studio, Trae — each mapped to its real paths (`CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`, `.mcp.json`, …). If a repo has Claude, Cursor, and Copilot config, ContextGuard shows all three so teams can see total exposure. Each tool still loads only its own files. Missing one is a bug — file it.

Instruction-file warnings also show up in the Problems panel on save, no manual scan needed.

### Know what it costs

The status bar shows your **auto-injected baseline**: the maximum tokens ContextGuard found across detected instruction files and MCP config, plus roughly what they cost per month. A 4k-token CLAUDE.md isn't free.

<p align="center">
  <img src="https://raw.githubusercontent.com/BearInBlue/contextguard/main/assets/status_bar_cline.png" alt="ContextGuard status bar showing auto-injected tokens and estimated monthly cost" width="519">
</p>

Defaults are conservative (50 requests/day, Sonnet-class pricing). Point `costModel.requestsPerDay` and `costModel.usdPerMillionInputTokens` at your real usage and every "≈ $X/month" becomes your number. And the dollars are only half of it — those tokens take up context-window room and compete with your real instructions on every request, so trimming the baseline buys you a sharper agent *and* a cheaper one. The token count is true no matter how you're billed.

For each convention, the baseline counts **what that tool loads automatically**: your global `~/` config, parent-directory config, even an instruction file that happens to contain a secret (still counted, since the tool loads it anyway — and flagged separately). When multiple tool configs exist, the combined number is the repo maximum, not a claim that one tool reads every file.

Instruction files also get an inline CodeLens, so the cost is visible at the top of the file and at the section that is carrying the weight.

<p align="center">
  <img src="https://raw.githubusercontent.com/BearInBlue/contextguard/main/assets/code_lens_cline.png" alt="ContextGuard CodeLens showing instruction-file token count, monthly cost, and section size" width="440">
</p>

**On-demand rules are counted only when they fire.** Path/glob-scoped rules (Cursor `globs`, Claude `paths:`, Windsurf `trigger: glob`) and subdirectory `CLAUDE.md`/`AGENTS.md` files aren't in the every-request baseline. But scan a file one of them targets and it's counted and badged "✓ Applies here," because the agent really would inject it there. Rules that load on the agent's judgment or an `@`-mention can't be predicted, so they're listed but left uncounted.

### See exactly what's injected

**Preview Injected Context** opens one document with every detected auto-loaded file — instruction files (`@imports` expanded), global/parent config, and MCP configs — in load order, split into "Always injected" vs "Loaded on demand," with per-source token counts. **Copy Injected Context** puts the always-injected maximum on your clipboard. Both **redact detected secrets** to `[REDACTED]` first, since these views embed full file content; token totals still reflect the real size.

<p align="center">
  <img src="https://raw.githubusercontent.com/BearInBlue/contextguard/main/assets/preview_cline.png" alt="ContextGuard injected context preview showing always-injected sources, loaded-on-demand rules, and redacted full content" width="780">
</p>

Reading config outside your project (global `~/`, and parent directories on Pro) happens only after a one-time, machine-wide consent prompt — the first time a scan reaches outside the workspace. Turn it off anytime under **Settings → Scan global & home config**.

---

## Fix: don't just flag *(Pro)*

**Optimize Instruction File** turns warnings into edits. Pick a file and ContextGuard proposes the cleanup: rules duplicated within it, rules that already live in another instruction file (the other copy stays authoritative), and a pick-the-winner prompt for each contradiction. You approve every removal, and the summary shows the payoff: *"4.1k → 1.8k tokens, ≈ −$2.10/month."*

## Guard: catch secrets before the agent does

Secrets in your **instruction files** (CLAUDE.md, .cursorrules, AGENTS.md, …) are flagged in the Problems panel on every save, **free** — those files are auto-injected into every session, so a leaked key there is core safety, not a paid extra.

**Live Secret Guard *(Pro)*** extends that same on-save check to *every other* AI-visible file (`.ts`, `.py`, `.json`, …). The moment a key, token, JWT, or database URL lands in one, you get a status-bar alert and a Problems entry with the exact line. Detection covers real shapes: OpenAI/Anthropic, GitHub, GitLab, Slack, Stripe, AWS, Google, and npm, plus JWTs, private-key blocks, and credentialed DB URLs.

**Workspace Security Audit** sweeps the whole project on demand and shows the file and line of every finding — **free**, because paywalling *where* a secret is would defeat the point. Pro's secret value is the *continuous* every-save guard, not the locations.

<p align="center">
  <img src="https://raw.githubusercontent.com/BearInBlue/contextguard/main/assets/secrets_test_repo.png" alt="ContextGuard workspace scan showing exact secret findings with line numbers and ignore actions" width="750">
</p>

**Secret redaction *(free)*** replaces detected secrets with `[REDACTED]` in every exported bundle and in the Preview/Copy views (bundles say so in the preamble). Leaking a credential is irreversible, so it's never gated — paste into any web AI without wondering what rode along. Env-var references and placeholders are left alone; only real secret material is scrubbed. (PR review packs are Pro, so their redaction is Pro.)

## Measure: real MCP overhead *(Pro)*

MCP configs only tell you a server exists. The real cost is the tool schemas it injects at runtime, and nothing shows that number. **Measure MCP Overhead** launches your configured stdio servers (with your confirmation, since these are commands from workspace config), performs the handshake, calls `tools/list`, and reports the actual schema tokens per server and their monthly cost.

Scope: ContextGuard measures repo-local MCP configs and stdio servers it can safely launch with your approval. Global, remote, client-managed, or built-in agent context may not be visible from the workspace.

## Bundle: clean context for any AI

Pick what matters, cut what doesn't, and export a paste-ready Markdown bundle for Claude, ChatGPT, Copilot Chat, or any web AI:

- Token counts per file, with a budget bar
- Per-file allocation breakdown (Pro)
- **Remove Noise** deselects lockfiles, generated output, logs, and snapshots in one click
- A compact **file map** at the top so the AI sees your project structure
- Live selection tracking: highlight code, bundle just the selection
- Export to clipboard or to a Markdown file for CLI agents that read context from disk

<p align="center">
  <img src="https://raw.githubusercontent.com/BearInBlue/contextguard/main/assets/bundle_cline.png" alt="ContextGuard exported bundle showing a file map and paste-ready context" width="760">
</p>

### PR Review *(Pro)*

**PR Review** scans your uncommitted changes, including untracked files that `git diff` silently misses. Each file shows diff and full-file token counts, toggleable per file. The export is a paste-ready review prompt in unified-diff format, with related test files included so the reviewer sees current coverage. **Branch Diff Review** packages all commits ahead of your base branch without touching git state — ideal for reviewing someone else's branch. Oversized packs split into labeled "Part X of N" pastes.

---

## Free vs Pro

<p align="center">
  <img src="https://raw.githubusercontent.com/BearInBlue/contextguard/main/assets/starting_ui_free_pro_table.png" alt="ContextGuard panel showing Free and Pro feature lists" width="752">
</p>

| Feature | Free | Pro |
|---|---|---|
| Core lint rules (size, sensitive, generated, noisy, MCP) | ✓ | ✓ |
| AI instruction & MCP config detection | ✓ | ✓ |
| Auto-injected token baseline + monthly cost in status bar | ✓ | ✓ |
| Budget tracking, Remove Noise, bundle export, file map | ✓ | ✓ |
| `.contextguardignore` + `.gitignore`-aware scans | ✓ | ✓ |
| Workspace scan | 50 files, with secrets prioritized | 1,500 files |
| **Secret redaction**: bundles auto-scrub keys, tokens & JWTs | ✓ | ✓ |
| On-save secret scan of **instruction files** (CLAUDE.md, .cursorrules, AGENTS.md, …) → Problems panel | ✓ | ✓ |
| **Live Secret Guard**: on-save secret scan of *all other* AI-visible files (source, config) + status-bar alert | — | ✓ |
| Secret redaction in PR review packs | — | ✓ |
| **Optimize Instruction File**: apply dedupe & conflict fixes | — | ✓ |
| **Measure MCP Overhead**: real tool-schema tokens per server | — | ✓ |
| **Duplicate Context & Instruction Conflict Detection** | — | ✓ |
| **Workspace Security Audit**: file & line of every finding | ✓ | ✓ |
| **PR Review**: git diff (incl. untracked files) → paste-ready review prompt | — | ✓ |
| **Branch Diff Review**: review committed changes without touching git state | — | ✓ |
| **Context History & Trends**: 30-day growth tracking | — | ✓ |
| **Quality metrics**: focus, noise, secrets, instructions, MCP | — | ✓ |
| **Copy Compressed**: mechanical excerpts of logs, JSON, markdown to your clipboard | — | ✓ |

When a scan hits the file cap, sensitive-looking files (`.env`, keys, keystores, Terraform state) and files with detected secret content are prioritized first.

Pro checkout isn't open yet. Planned early-access pricing is **$5/month or $39/year**; once it opens, activate with `ContextGuard: Activate Pro License` from the Command Palette.

---

## .contextguardignore

Create a `.contextguardignore` at the project root to exclude files from scans:

```
# Logs and generated files
*.log
logs/
*.min.js
archive/
```

Workspace scans also respect your `.gitignore`, except for sensitive-looking files — a gitignored `.env` is still AI-visible, so it's still flagged.

---

## Settings

| Setting | Default | What it does |
|---|---|---|
| `contextguard.costModel.requestsPerDay` | `50` | Assumed AI requests/day behind every "≈ $X/month" estimate |
| `contextguard.costModel.usdPerMillionInputTokens` | `3` | Input-token price (USD/M) behind the cost estimates |
| `contextguard.statusBar.enabled` | `true` | Show the token baseline in the status bar |
| `contextguard.scanGlobalConfig.enabled` | `true` | Count your home/global AI config (`~/.claude/CLAUDE.md`, `~/AGENTS.md`, …) in the baseline (asks permission once before reading outside the workspace) |
| `contextguard.alwaysInjectedBudgetTokens` | `0` | Soft baseline budget; over it, the status bar and panel nudge you. `0` disables it. Never blocks anything |
| `contextguard.codeLens.enabled` | `true` | Show the inline token/cost CodeLens at the top of instruction files |
| `contextguard.liveSecretGuard.enabled` | `true` | Run the on-save secret check (Pro) |
| `contextguard.secretScan.highEntropy` | `false` | Warn on random-looking values that do not match a known token pattern or secret key name. More recall, more false positives — silence a line with a `contextguard:allow` comment |
| `contextguard.scanParentDirectories.enabled` | `false` | On a Workspace scan, also check parent directories up to the filesystem root for instruction/MCP files (Pro; asks permission first) |

---

## Support

ContextGuard's free tier stands on its own — token baseline, lint rules, secret detection with exact locations, bundle redaction, and clean export, all local. If it's saved you tokens or caught a secret, **Pro funds the work** (and unlocks PR review, Live Secret Guard, Optimize, and more). Checkout isn't open yet; early-access pricing is planned at **$5/month or $39/year**.

---

## Privacy

Local-first. Scans, lints, and bundles run on your machine — no telemetry, no data collection. Reading AI config outside your project (global `~/`, and parent directories on Pro) happens only after a one-time, machine-wide consent prompt; until you allow it, ContextGuard stays inside the workspace. The only outbound call is Pro license activation or validation — to the ContextGuard license server, which forwards to Lemon Squeezy — and only when you activate a key. Measure MCP Overhead launches workspace-configured MCP commands after your confirmation; those servers do whatever they normally do. Your code stays on your machine.

---

## License

Proprietary. See [LICENSE](LICENSE).
