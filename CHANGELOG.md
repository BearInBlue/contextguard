# Changelog

All notable changes to ContextGuard are documented here.

## [0.1.0] - 2026-06-14

First public release. ContextGuard is a linter for your AI configuration. It shows what your AI assistant may be reading, flags problems in your instruction and MCP files, helps catch secrets before they reach a model, and packages clean context for any external agent.

### What it does

- **Context inspector.** Scan the active file, your open tabs, or the whole workspace and see how many tokens each file adds to an AI session, with a running budget and a status-bar total. Workspace scans respect `.gitignore` and `.contextguardignore`.
- **Instruction linter.** Deterministic rules check your AI instruction files (CLAUDE.md, AGENTS.md, .cursorrules, and 18 other conventions) for oversized files, duplicate rules, and rules that contradict each other across tools. Findings name the tool they belong to and jump straight to the line.
- **Auto-injected baseline.** Before you scan anything, the status bar shows how many tokens of instruction files and MCP config already get injected into every session, with an estimated monthly cost in the tooltip. Point the estimate at your own model price and request volume in the settings.
- **Secret detection.** High-confidence patterns catch GitHub, GitLab, Stripe, npm, and Google credentials, plus private key blocks, JWTs, and database URLs with passwords in them. Sensitive filenames like `.env`, keystores, Terraform state, and SSH or cloud credential paths stay flagged even when they fall past a scan limit.
- **Context bundles.** Pick the files you want and copy them as a single Markdown paste for any external agent, with a directory tree at the top. Bundles too big for your token budget are split into labeled parts you copy one at a time.
- **CLI.** The same deterministic checks run headless for CI. Use `contextguard check` with `--json` and `--fail-on error|warning|never`; it reads your `.gitignore` like the extension does.

### Pro

- **Live Secret Guard** checks every save of an AI-visible file and reports detected secrets in the Problems panel.
- **PR Review and Branch Diff Review** turn a working diff or an already-committed branch into a review bundle and surface the existing tests for the changed files, without touching your staging area.
- **Secret redaction** strips detected keys, tokens, and credentials out of exported bundles automatically.
- **Quality metrics** score a selection on focus, noise, secrets, instruction weight, and MCP overhead.
- **Optimize Instruction File** applies fixes instead of just listing them. It removes duplicate rules, walks you through contradictions, and reports the estimated tokens and cost saved.
- **Measure MCP Overhead** launches your configured stdio MCP servers, with your consent, and reports the real tool-schema token cost of each one.
- **Context history and trends** keep local snapshots of how your context grows, with a 30-day trend strip in the panel.
- **Larger scans.** Free covers 50 files per workspace scan. Pro raises that to 1,500.
