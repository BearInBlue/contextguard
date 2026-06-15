# ContextGuard

**The linter for your AI configuration.**

Your agents read a layer of configuration on every single request (CLAUDE.md, AGENTS.md, `.cursorrules`, Copilot instructions, MCP server configs), and nothing reviews it. When an agent ignores an instruction, you blame the model, not the 6,000-token CLAUDE.md where your one important rule is buried under contradictions and a duplicated style guide.

ContextGuard is ESLint for that layer: deterministic rules that catch oversized instruction files, unmeasured MCP overhead, secrets in AI-visible files, and token bloat, each with the monthly cost it's silently adding. Pro adds duplicate Markdown context and contradicting-rule checks. Local-first; your code never leaves your machine during scans.

It also packages clean, paste-ready context bundles for any web AI, including PR review packs built from your git diff (Pro).

**30 seconds to value:** install and open a project. If it has any AI instruction or MCP config, the status bar shows how many tokens they add to *every* AI request and the estimated monthly cost. Click it to open the inspector.

---

## Quick Start

1. Install ContextGuard and open a project. If it has AI instruction or MCP files, the status bar (bottom right) shows their auto-injected token baseline and estimated monthly cost.
2. Click the ContextGuard icon in the Activity Bar.
3. Click **Workspace** to lint your AI configuration, or **Tabs** to see your current session's context.
4. Fix the warnings, or select files and export a clean bundle.

---

## Why

Every AI coding tool silently injects project configuration into every session, and that configuration drifts:

- Instruction files grow until they tax every single request, without anyone noticing
- CLAUDE.md and `.cursorrules` start contradicting each other once a team uses more than one tool
- MCP servers add schema overhead nobody has measured
- Secrets end up in files agents happily read, even files that are safely gitignored
- Handing context to a web AI means hand-picking files and stripping out noise, over and over

ContextGuard answers all of this locally, with deterministic rules. No AI required to lint your AI.

---

## Lint: ESLint for AI context

Scan your active file, open tabs, or workspace. Pro adds PR Review and Branch Diff Review scans. Every warning is a rule, not a guess:

| Rule | What it catches |
|---|---|
| Large / Huge Instruction | AI instruction files over 1.5k–6k tokens that tax every request |
| Duplicate Context / Rule *(Pro)* | Repeated Markdown context or the same rule repeated across CLAUDE.md, `.cursorrules`, AGENTS.md, … |
| Conflicting Rule *(Pro)* | "Use tabs" in one file, "use spaces" in another, with Jump To navigation |
| Sensitive | `.env`, `*.pem`, `*.key`, keystores, Terraform state, and secret-like content (API keys, JWTs, DB URLs) in source files |
| MCP Unknown | MCP configs whose runtime schema overhead isn't counted anywhere |
| Large / Huge File | Files over 3k / 8k tokens that would dominate a request |
| Generated / Noisy | `dist/`, lockfiles, logs, snapshots: high-token, low-signal context |
| No Test Changes *(Pro review scans)* | Git diff touches source files but no tests |

### Every convention, one registry

ContextGuard detects 21+ AI configuration conventions, including Claude Code, the AGENTS.md standard, Cursor, GitHub Copilot, Cline, Windsurf, Roo Code, Gemini, Codex CLI, Aider, Zed, Continue, JetBrains Junie, Amazon Q, Kilo Code, Goose, OpenHands, Amp, Augment Code, Firebase Studio, and Trae, each mapped to its real config paths (`CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`, `.mcp.json`, and the rest). When a tool ships a new convention it becomes a registry entry, and every surface (panel, save-lint, CLI) picks it up. Missing one? That's a bug; file it.

Instruction-file warnings also appear in the Problems panel automatically on save, no manual scan needed.

### Know what it costs

The status bar shows your **auto-injected baseline**: how many tokens of instruction files and MCP config ride along with every AI request, and roughly what that costs per month. A 4k-token CLAUDE.md isn't free; ContextGuard puts a number on it.

The defaults are deliberately conservative (50 requests/day, Sonnet-class input pricing). Set `contextguard.costModel.requestsPerDay` and `contextguard.costModel.usdPerMillionInputTokens` to your real usage and every "≈ $X/month" in the extension becomes *your* number, not an estimate you have to discount.

---

## Fix: don't just flag *(Pro)*

**Optimize Instruction File** (`ContextGuard: Optimize Instruction File`) turns warnings into edits. Pick a file and ContextGuard proposes the cleanup: rules duplicated within the file, rules that already live in another instruction file (the other copy stays authoritative), and a pick-the-winner prompt for each contradiction. You review every removal before it's applied, and the summary shows the payoff: *"4.1k → 1.8k tokens, ≈ −$2.10/month."*

## Guard: continuous, not on-demand *(Pro)*

**Live Secret Guard** checks every save of an AI-visible file for secret-like content. The moment an API key, token, JWT, or database URL lands in a source or config file, you get a status bar warning and a Problems entry with the exact line, before any agent reads it. Detection covers real token shapes: OpenAI/Anthropic, GitHub, GitLab, Slack, Stripe, AWS, Google, and npm, plus JWTs, private key blocks, and credentialed database URLs.

**Workspace Security Audit** (`ContextGuard: Workspace Security Audit`) sweeps the whole project on demand. Free shows how many issues exist; Pro shows exactly which file and line each one is on.

**Secret redaction in bundles.** Every bundle you export with Pro automatically replaces detected API keys, tokens, JWTs, private key blocks, and credentialed database URLs with `[REDACTED]` placeholders, and says so in the bundle preamble. Paste into any web AI without wondering what rode along. Env-var references and placeholders are left alone; only real secret material is scrubbed.

## Measure: real MCP overhead *(Pro)*

MCP configs only tell you a server exists. The real cost is the tool schemas it injects at runtime, and no tool shows that number. **Measure MCP Overhead** (`ContextGuard: Measure MCP Overhead`) launches your configured stdio servers (with your explicit confirmation, since these are commands from workspace config), performs the MCP handshake, calls `tools/list`, and reports the actual schema tokens per server and what they cost per month.

Scope note: ContextGuard measures repo-local MCP configs and stdio servers it can safely launch with your approval. Global, client-managed, remote, or built-in agent context may not be visible from the workspace.

## Bundle: clean context for any AI

Select what matters, cut what doesn't, and export a paste-ready Markdown bundle for Claude, ChatGPT, Copilot Chat, or any web AI:

- Token counts per file, with a budget bar
- Per-file allocation breakdown (Pro)
- **Remove Noise** deselects lockfiles, generated output, logs, and snapshots in one click
- A compact **file map** at the top of every bundle so the AI sees your project structure
- Live selection tracking: highlight code and bundle just the selection
- Export to clipboard or to a Markdown file for CLI agents that read context from disk

### PR Review *(Pro)*

Click **PR Review** to scan your uncommitted git changes, including brand-new untracked files, which `git diff` alone silently misses. Each file shows both diff and full-file token counts; toggle per file. The export is a paste-ready code review prompt in standard unified diff format, with related test files included so the reviewer sees current coverage.

**Branch Diff Review** packages all commits ahead of your base branch without resetting, staging, or unstaging anything, ideal for reviewing someone else's PR branch.

Oversized review bundles split automatically into labeled "Part X of N" pastes that fit your budget.

---

## Free vs Pro

| Feature | Free | Pro |
|---|---|---|
| Core lint rules (size, sensitive, generated, noisy, MCP) | ✓ | ✓ |
| AI instruction & MCP config detection | ✓ | ✓ |
| Auto-injected token baseline + monthly cost in status bar | ✓ | ✓ |
| Budget tracking, Remove Noise, bundle export, file map | ✓ | ✓ |
| `.contextguardignore` + `.gitignore`-aware scans | ✓ | ✓ |
| Workspace scan | 50 files | 1,500 files |
| **Live Secret Guard**: secret check on every save | — | ✓ |
| **Secret redaction**: bundles auto-scrub keys, tokens & JWTs | — | ✓ |
| **Optimize Instruction File**: apply dedupe & conflict fixes | — | ✓ |
| **Measure MCP Overhead**: real tool-schema tokens per server | — | ✓ |
| **Duplicate Context & Instruction Conflict Detection**: duplicate Markdown context and contradicting AI rules | — | ✓ |
| **Workspace Security Audit** | Count only | File & line detail |
| **PR Review**: git diff (incl. untracked files) → paste-ready review prompt | — | ✓ |
| **Branch Diff Review**: review committed changes without touching git state | — | ✓ |
| **Context History & Trends**: 30-day growth tracking | — | ✓ |
| **Quality metrics**: focus, noise, secrets, instructions, MCP | — | ✓ |
| **Compress**: mechanical excerpts of logs, JSON, markdown | — | ✓ |

The file cap never hides a secret: sensitive-looking files (`.env`, keys, keystores, Terraform state) are always scanned and flagged, even past the Free limit.

Pro checkout isn't open yet. Planned early-access pricing is **$5/month or $39/year**.

Once checkout opens, activate Pro with `ContextGuard: Activate Pro License` from the Command Palette.

---

## .contextguardignore

Create a `.contextguardignore` at the project root to exclude files from ContextGuard scans:

```
# Logs and generated files
*.log
logs/
*.min.js
archive/
```

Workspace scans also respect your `.gitignore` automatically, except for sensitive-looking files (a gitignored `.env` is still AI-visible, so it is still flagged).

---

## Settings

| Setting | Default | What it does |
|---|---|---|
| `contextguard.costModel.requestsPerDay` | `50` | Assumed AI requests/day behind every "≈ $X/month" estimate |
| `contextguard.costModel.usdPerMillionInputTokens` | `3` | Input-token price (USD/M) behind the cost estimates |
| `contextguard.statusBar.enabled` | `true` | Show the token baseline in the status bar |
| `contextguard.liveSecretGuard.enabled` | `true` | Run the on-save secret check (Pro) |

---

## Privacy

ContextGuard is local-first. Scans, lints, and bundles run on your machine, with no telemetry or data collection. The only planned ContextGuard service call is Pro license activation or validation against `api.contextguard.dev`. Measure MCP Overhead launches workspace-configured MCP commands only after your confirmation; those servers may make whatever network calls they normally make. Your code stays on your machine.

---

## License

Proprietary. See [LICENSE](LICENSE).
