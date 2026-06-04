<p align="center">
  <img src="images/banner.svg" alt="Claude Code Cheat Sheet — powered by Opus 4.8" width="100%">
</p>

<h1 align="center">Claude Code Cheat Sheet</h1>

<p align="center">
  <b>Not a flag dump — the way the pros actually use Claude Code.</b><br>
  Real workflows, how to prompt, god-tier setup, and the traps to avoid. Tuned for <code>Opus 4.8</code> / CLI <code>v2.1+</code>.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Powered%20by-Opus%204.8-D97757?style=flat-square" alt="Opus 4.8">
  <img src="https://img.shields.io/badge/Claude%20Code-v2.1%2B-1A1A1A?style=flat-square" alt="CLI v2.1+">
  <img src="https://img.shields.io/badge/License-MIT-3A7BD5?style=flat-square" alt="MIT">
</p>

<p align="center">
  <b>English</b> &nbsp;·&nbsp; <a href="README.md">日本語</a>
</p>

---

> Most cheat sheets just list flags you could get from `--help`. This one centers on **how Anthropic's own engineers actually work**, and folds the full command reference below. Everything verified against `claude --help` (v2.1.x) and the official docs.

<p align="center">
  <img src="images/cheatsheet-poster.svg" alt="Claude Code one-page cheat sheet" width="100%">
</p>

## Contents

1. [The Good Stuff](#the-good-stuff) — how the pros work
2. [Prompting](#prompting) — anatomy & technique
3. [God-Tier Setup](#god-tier-setup) — statusLine, quality gates, cost design
4. [Accuracy](#accuracy)
5. [Anti-Patterns](#anti-patterns)
6. [Which mode?](#which-mode-should-i-use)
7. [Reference](#reference) (collapsed) — [Commands](#command-guide) / [Tables](#reference-tables) / [Deep Dives](#deep-dives) / [Subagents](subagents.en.md)

## Quick Start

```bash
curl -fsSL https://claude.ai/install.sh | bash   # macOS/Linux/WSL  (Windows: irm https://claude.ai/install.ps1 | iex)
claude auth login                                # sign in
cd your-project && claude                        # then /init to generate CLAUDE.md
```

---

# The Good Stuff

> The one fact that drives everything: **the context window fills up fast, and quality drops as it fills.** Almost every tip here is really about protecting that context — which is why advanced users obsess over what *not* to put in it.

### Explore → Plan → Code → Commit

Don't let Claude jump straight to code on anything non-trivial — it'll confidently solve the *wrong* problem. Separate thinking from doing.

```text
1. Shift+Tab → plan mode.  "read src/auth and explain how sessions work"  (no edits)
2. "add Google OAuth — what changes? write a plan."   → Ctrl+G to edit the plan
3. Shift+Tab back.  "implement the plan, write tests, run them, fix failures"
4. "commit with a clear message and open a PR"
```

Skip the plan for one-sentence diffs. Use it when the change **spans files, you're unsure of the approach, or you don't know the code** being touched.

### Give Claude a way to verify (most important)

This is the line between *babysitting* a session and *walking away* from one. Hand Claude a check that returns pass/fail and it closes the loop itself.

| Instead of… | Do this |
|:--|:--|
| "implement email validation" | "write `validateEmail`. tests: `a@b.com`→true, `a@.com`→false. **run them after**" |
| "make the dashboard look better" | "[screenshot] match this. **screenshot the result, compare, fix the diffs**" |
| "the build is failing" | "[error] fix the **root cause**, don't suppress it, then verify the build passes" |

How hard to gate the stop:

- **In the prompt** — "run it and iterate until it passes" (works today)
- **Across a session** — `/goal <condition>`, re-checked every turn until it holds
- **Deterministic gate** — a [Stop hook](#hooks-quality-gates) blocks the turn until your script passes
- **Second opinion** — a fresh subagent / workflow tries to *refute* the result

Ask for evidence (the test output), not "done ✅".

### Protect context — subagents & `/clear`

Context is the fundamental constraint, so **subagents are your most powerful tool**. Exploring a big codebase floods context with file reads; a subagent explores in its *own* context and reports back a summary.

```text
use a subagent to investigate how we handle token refresh, and whether we
already have OAuth utilities I should reuse
```

Isolate verbose output (test runs, log parsing, doc fetches) in a subagent too. `/clear` when switching to unrelated work.

### Verify with multiple agents

A second session (fresh context) reviewing the first's work isn't biased toward code it just wrote.

```text
Writer:   implement a rate limiter for our API
Reviewer: review @src/middleware/rateLimiter.ts — edge cases, races, consistency
          with existing middleware  (← separate session, clean context)
```

Same idea for tests: one Claude writes tests, another writes code to pass them. The bundled `/code-review` and `/security-review` run in a fresh subagent. To keep this loop going on autonomous runs, use [agent teams](#scale-up).

### Course-correct early

Tight feedback loops beat one perfect prompt.

- **`Esc`** — stop mid-action (context preserved, just redirect)
- **`Esc` `Esc`** / **`/rewind`** — roll back conversation, code, or both
- **`/clear`** — wipe context between unrelated tasks

> **Two-correction rule:** corrected Claude twice on the same thing? The context is now polluted with failed attempts. `/clear` and restart with a sharper prompt. A clean session almost always wins.

---

# Prompting

> Output quality is mostly decided here. Vague in, vague out — and it wastes context too.

### Anatomy of a prompt

Good prompts tend to have four parts:

```
[Context]   where to look  (@file, an example pattern, a screenshot)
[Task]      what to do      (one goal per prompt)
[Constraints] what to honor (state them all up front)
[Verify]    what "done" means (tests, expected output)
```

```text
look at the token refresh in @src/auth/ (context), fix the bug where login fails
after session timeout (task). no new libraries (constraint). write a failing test
that reproduces it first, then fix it and run the tests (verify)
```

### Specificity is everything

| ✗ Vague | ✓ Specific |
|:--|:--|
| "fix the login bug" | "login fails after timeout. check token refresh in `src/auth/`, write a repro test, then fix" |
| "add a calendar widget" | "follow the pattern in `@HotDogWidget.php` to add a calendar widget. no new libraries" |
| "why is this API so weird?" | "look through `ExecutionFactory`'s git history and summarize how its API came to be" |
| "improve this codebase" | "add input validation to the login function in `auth.ts`" (avoids broad scanning, saves tokens) |

### More techniques

- **Give a role** — "as a senior security engineer, review for injection, authz, and secrets"
- **Show an example** (few-shot) — point at the implementation or test to follow with `@`
- **Positive over negative** — "do Y" beats "don't do X"; describe the shape you want
- **Rich input** — `@file`, paste screenshots (Ctrl+V), give doc URLs, `cat error.log | claude -p "..."`
- **Let it interview you** for big features — "interview me with AskUserQuestion, then write the spec to `SPEC.md`" → implement in a fresh session

### Prompting anti-patterns

| ✗ Common | ✓ Better |
|:--|:--|
| Pile multiple asks into one prompt | One goal per prompt; build up stepwise |
| "why is it broken?" — no detail | Symptom + repro steps + expected result |
| Add constraints later, piecemeal | State all constraints **up front** |
| "make it nice" | Name the example / shape to follow |
| Run Claude's plan without reading it | Confirm the plan in plan mode first |
| Jargon with zero context | Point at files and use domain language |

---

# God-Tier Setup

Five minutes that pays off every session. Only the real stuff, copy-paste ready.

### statusLine: a context meter

When the fill level is **visible**, you learn when to `/clear` or `/compact` by feel. Highest-leverage setting. Create `~/.claude/statusline.sh`:

```bash
#!/bin/bash
input=$(cat)
MODEL=$(echo "$input" | jq -r '.model.display_name')
PCT=$(echo "$input" | jq -r '.context_window.used_percentage // 0')
COST=$(echo "$input" | jq -r '.cost.total_cost_usd // 0')
EFFORT=$(echo "$input" | jq -r '.effort.level // "-"')
echo "[$MODEL·$EFFORT] ${PCT}% ctx · \$$(printf '%.2f' "$COST")"
```

```jsonc
// ~/.claude/settings.json
{ "statusLine": { "type": "command", "command": "~/.claude/statusline.sh" } }
```

Too lazy to script it? `/statusline show model and context % as a bar` and Claude writes it. The stdin JSON includes `context_window.used_percentage`, `cost.total_cost_usd`, `effort.level`, `model`, `rate_limits`, and more.

### Hooks as quality gates

Unlike `CLAUDE.md` (advisory), hooks are **deterministic** — for things that must happen every time.

**Auto-lint/format on every edit** (`PostToolUse`):

```json
{ "hooks": { "PostToolUse": [
  { "matcher": "Edit|Write", "hooks": [{ "type": "command", "command": "npm run lint --silent" }] }
] } }
```

**Don't end the turn until tests pass** (`Stop` hook — a quality gate for autonomous runs):

```json
{ "hooks": { "Stop": [
  { "hooks": [{ "type": "command", "command": "npm test --silent || { echo 'tests failing' >&2; exit 2; }" }] }
] } }
```

Exit code `2` blocks the turn and feeds stderr back to Claude (auto-released after 8 consecutive blocks).

**Block dangerous ops** (`PreToolUse`, filtered by `if`):

```json
{ "hooks": { "PreToolUse": [
  { "matcher": "Bash", "hooks": [{ "type": "command", "if": "Bash(rm -rf *)", "command": "echo blocked >&2; exit 2" }] }
] } }
```

**Preprocess logs to save tokens** — instead of feeding Claude a 10,000-line log, a hook can return only the `ERROR` lines, cutting context from tens of thousands of tokens to hundreds.

> Tip: *"write a hook that runs eslint after every file edit"* — Claude will author it.

### Permission craft

Kill the approval prompts while still stopping the dangerous stuff. Via `/permissions` or settings:

```json
{ "permissions": {
  "allow": ["Bash(npm run:*)", "Bash(git:*)", "Bash(gh:*)", "Read(./src/**)", "Edit(./src/**)"],
  "ask":   ["Bash(git push:*)"],
  "deny":  ["Read(./.env)", "Read(./.env.*)", "Read(./secrets/**)", "Bash(rm -rf:*)", "Bash(curl:*)"]
} }
```

`deny` > `ask` > `allow`. Or `claude --permission-mode auto` to let a classifier handle it (asks only on risky calls).

### Cost design

Tokens scale with context size. **Prompt caching** (reusing a stable prefix) is automatic — the trick is not to break it.

| Do this | Why |
|:--|:--|
| Keep `CLAUDE.md` **stable** (don't churn it) | prompt cache keeps hitting |
| `/clear` between unrelated tasks | stop carrying stale context every message |
| `sonnet` by default, `opus` for hard parts, `haiku` for subagents | optimize per-model price |
| Lower effort / `MAX_THINKING_TOKENS=8000` on easy tasks | thinking is billed as output |
| Prefer CLI (`gh`/`aws`) over MCP; disable unused MCP | save tool-definition context |
| Preprocess logs in a hook; move heavy knowledge to skills | keep context small |
| `--bare` to skip hooks/MCP/CLAUDE.md | lean, fast scripted runs |

`/usage` for spend, `/context` for the breakdown.

### Full settings.json

<details>
<summary>Everything above — copy, then grow it</summary>

```jsonc
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "model": "opus",
  "effortLevel": "high",
  "permissions": {
    "allow": ["Bash(npm run:*)", "Bash(git:*)", "Bash(gh:*)", "Read(./src/**)", "Edit(./src/**)"],
    "ask":   ["Bash(git push:*)"],
    "deny":  ["Read(./.env)", "Read(./.env.*)", "Read(./secrets/**)", "Bash(rm -rf:*)", "Bash(curl:*)"]
  },
  "hooks": {
    "PostToolUse": [
      { "matcher": "Edit|Write", "hooks": [{ "type": "command", "command": "npm run lint --silent" }] }
    ]
  },
  "statusLine": { "type": "command", "command": "~/.claude/statusline.sh" },
  "env": { "MAX_THINKING_TOKENS": "32000", "BASH_DEFAULT_TIMEOUT_MS": "300000" }
}
```
Precedence (later wins): user → project `.claude/settings.json` → local `.claude/settings.local.json` → CLI flags → managed.
</details>

### Environment

- **`/terminal-setup`** for Shift+Enter newlines; on macOS [enable Option as Meta](https://code.claude.com/docs/en/terminal-config) so `Alt+P/T/O` work
- **Install `gh`** — the most context-efficient way for Claude to open PRs, read issues, check CI
- **`.mcp.json`** committed to share MCP servers with your team

---

# Accuracy

- **A `CLAUDE.md` that earns its place** — for each line ask *"would removing this make Claude mess up?"* If not, cut it. **Bloat gets half-ignored**, so keep it under ~200 lines and push heavy procedures into skills. `IMPORTANT:` / `YOU MUST` for rules it keeps missing.
- **Always give it a check** (→ [The Good Stuff](#give-claude-a-way-to-verify-most-important)). Unverified plausible code is the #1 source of bad output.
- **Spend reasoning where it counts** — `--effort high`/`xhigh`, `/effort ultracode`, thinking (`Alt+T`) on hard problems; low for routine.
- **Guard context** — `/context` for the breakdown, `/compact` to summarize, `/clear` to reset.
- **Go big** — pin `claude-opus-4-8[1m]` for the 1M-token window.
- **Adversarial review** — a fresh subagent that only sees the diff catches what the author-session rationalizes away: *"review this diff against `PLAN.md` — report gaps that affect correctness, not style."*

---

# Anti-Patterns

### Prompting

| ✗ Common | ✓ Better |
|:--|:--|
| Multiple asks in one prompt | One goal per prompt, stepwise |
| "why is it broken?" | Symptom + repro + expected |
| Constraints added late | State them all up front |
| "make it nice" | Name the example to follow |

### Sessions

| ✗ Common | ✓ Better |
|:--|:--|
| Kitchen-sink chat (noisy context) | `/clear` between unrelated tasks |
| Correcting in circles | After 2 tries, `/clear` + sharper prompt |
| "investigate" with no scope → 200 files | Scope it / delegate to a subagent |
| Trust without verify | Tests/screenshots; don't ship what you can't verify |

### Config

| ✗ Common | ✓ Better |
|:--|:--|
| 500-line `CLAUDE.md` | Prune hard; move procedures to hooks/skills |
| Fast mode on a hard problem | `Alt+O` off, `--effort high` + thinking |
| `--dangerously-skip-permissions` as a habit | Allowlist + `auto` mode |

---

# Which Mode Should I Use?

<p align="center">
  <img src="images/decision-flowchart.svg" alt="Which Claude Code mode, model, and effort to use" width="100%">
</p>

---

# Reference

The complete command surface — folded so it doesn't bury the good stuff.

## Command Guide

<details>
<summary><b>Daily driver</b> — start, sessions, models, navigation</summary>

```bash
claude · claude "task"          # interactive / with prompt
claude -p "explain @src/auth.ts"# print: run once, exit
cat error.log | claude -p "find the root cause"
claude -c · -r "name" "..." · --resume   # continue / resume / picker
claude --from-pr 123 · /rewind  # resume from PR / roll back
claude --model opus             # latest Opus; 1M: claude-opus-4-8[1m]
/model · /effort high · Alt+T thinking · Alt+O fast
```
Navigate: `/help` `/status` `/context` `/clear` `/compact` `/config` `/doctor`
Keys: `Esc` · `Esc Esc` · `Shift+Tab` · `Ctrl+O` · `@` `!` `#`
</details>

<details>
<summary><b>Take control</b> — permissions, memory, customization</summary>

```bash
claude --permission-mode plan
claude --allowedTools "Bash(git:*)" "Read"
claude --disallowedTools "Bash(rm:*)" "Bash(sudo:*)"
/permissions · /sandbox
/init · /memory · # (append a fact) · /context · /compact · /clear
/agents · /skills · .claude/commands/ · /hooks
```
</details>

<details>
<summary><b>Scale up</b> — MCP, headless, parallel, teams</summary>

```bash
claude mcp add github -- npx -y @modelcontextprotocol/server-github
claude mcp add --transport sse linear https://mcp.linear.app/sse
claude -p "task" --output-format json
claude -p --max-turns 3 --max-budget-usd 5 "task"
claude --bare -p "task"          # minimal & fast
claude --bg "investigate" · claude -w feature-auth   # background / worktree
/batch ... · /workflows · claude setup-token · /schedule
```
Agent teams: enable with `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` (use `sonnet`, keep teams small, clean up — each teammate is its own context window).
</details>

## Reference Tables

<details>
<summary><b>CLI commands & flags</b></summary>

| Command | Description |
|:--|:--|
| `claude` / `"q"` / `-p "q"` | interactive / prompt / print-once |
| `-c` · `-r <id\|name>` · `--resume` | continue / resume / picker |
| `-n <name>` · `--from-pr <PR>` · `-w [name]` · `--bg` | name / PR / worktree / background |
| `agents` · `attach\|logs\|stop\|rm <id>` | background sessions |
| `auth …` · `setup-token` · `mcp` · `update` · `doctor` | auth / CI / servers / maintenance |

| Flag | Description |
|:--|:--|
| `--model` · `--effort` · `--fallback-model` | model / `low…max` / fallback |
| `--permission-mode` | `default`/`acceptEdits`/`plan`/`auto`/`dontAsk`/`bypassPermissions` |
| `--allowedTools` · `--disallowedTools` · `--tools` | allow / deny / restrict |
| `-p` · `--output-format` · `--json-schema` · `--max-turns` · `--max-budget-usd` | headless |
| `--bare` · `--add-dir` · `--settings` · `--mcp-config` · `--append-system-prompt` | misc |
</details>

<details>
<summary><b>Slash commands</b></summary>

| Command | Description |
|:--|:--|
| `/help` `/status` `/doctor` `/config` | help / status / diagnose / settings |
| `/clear` `/compact` `/context` `/rewind` | context & checkpoints |
| `/resume` `/rename` `/branch` `/export` `/copy` | sessions |
| `/model` `/effort` `/fast` `/plan` `/permissions` `/sandbox` | model / mode |
| `/init` `/memory` `/agents` `/skills` `/hooks` `/mcp` `/plugin` | extend |
| `/code-review` `/security-review` `/batch` `/workflows` | review / parallel |
| `/usage` `/btw` `/goal` `/schedule` `/loop` | usage / side-q / goal / schedule |
</details>

<details>
<summary><b>Keyboard & Vim</b></summary>

| Key | Action |
|:--|:--|
| `Esc` · `Esc Esc` | interrupt · rewind |
| `Ctrl+O` · `Ctrl+R` · `Ctrl+L` | transcript · history · redraw |
| `Ctrl+T` · `Ctrl+B` · `Ctrl+G` | tasks · background · `$EDITOR` |
| `Shift+Tab` · `Alt+P/T/O` | permission cycle · model/thinking/fast |
| `@` · `!` · `#` | file · shell · memory |

Vim (`/config` → Editor mode): `i a o` insert · `hjkl` move · `dd cw yy p` edit · `u .` undo/repeat.
</details>

## Deep Dives

<details>
<summary><b>Recipes</b></summary>

```bash
git diff | claude -p "review this diff for bugs and security issues"
git log --oneline -20 | claude -p "draft release notes grouped by feat/fix"
```
```text
write tests for @src/auth.ts covering the error paths, then run them
trace how a request flows from the router to the database
/batch migrate every component in src/ from class to function components
```
</details>

<details>
<summary><b>Hook events</b></summary>

| Event | Fires… |
|:--|:--|
| `PreToolUse` | before a tool — **can block** (exit `2`) |
| `PostToolUse` | after success (auto-lint/format) |
| `Stop` | turn end (test quality gate) |
| `UserPromptSubmit` · `SessionStart`/`End` | prompt / session |
| `PreCompact` · `SubagentStop` · `Notification` | compaction / subagent / notify |
</details>

<details>
<summary><b>Env vars</b> · <b>Permission syntax</b></summary>

**Env vars:** `ANTHROPIC_API_KEY` · `CLAUDE_CODE_OAUTH_TOKEN` (CI) · `ANTHROPIC_MODEL` · `CLAUDE_CODE_EFFORT_LEVEL` · `MAX_THINKING_TOKENS` · `BASH_DEFAULT_TIMEOUT_MS` · `CLAUDE_CODE_USE_BEDROCK`/`_USE_VERTEX` · `DISABLE_TELEMETRY`

**Permission syntax** — `Tool(pattern)`, deny > ask > allow:
`Bash(npm run:*)` · `Bash(git:*)` · `Read(./src/**)` · `Read(./.env)` (deny) · `Edit` (bare = all calls)
</details>

<details>
<summary><b>FAQ</b></summary>

- **Which model?** `opus` for hard reasoning, `sonnet` for everyday, pin `claude-opus-4-8` for reproducibility.
- **`-p` vs interactive?** `-p` for one-shot/scriptable; the REPL for iterative.
- **Context full?** `/compact` to keep going, `/clear` between tasks, `/context` to diagnose.
- **Broke my files?** No — it checkpoints before edits. `Esc Esc` / `/rewind` (not a git replacement).
- **CI?** `claude setup-token` → `CLAUDE_CODE_OAUTH_TOKEN` → `claude -p --output-format json`.
</details>

---

## Subagents

Drop specialized agents into `.claude/agents/` and Claude delegates automatically. Ready-made definitions in **[subagents.en.md](subagents.en.md)**.

## Contributing · License

PRs welcome — verify new commands against `claude --help` and the [docs](https://code.claude.com/docs/en/overview) first. [MIT](LICENSE).

📚 [Best Practices](https://code.claude.com/docs/en/best-practices) · [Workflows](https://code.claude.com/docs/en/common-workflows) · [CLI Reference](https://code.claude.com/docs/en/cli-reference) · [Settings](https://code.claude.com/docs/en/settings) · [Hooks](https://code.claude.com/docs/en/hooks) · [Costs](https://code.claude.com/docs/en/costs)

<p align="center"><sub>⭐ Useful? Star the repo. Verified against Claude Code v2.1.x · Opus 4.8.</sub></p>
