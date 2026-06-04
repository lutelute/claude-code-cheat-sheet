<p align="center">
  <img src="images/banner.svg" alt="Claude Code Cheat Sheet — powered by Opus 4.8" width="100%">
</p>

<h1 align="center">Claude Code Cheat Sheet</h1>

<p align="center">
  <b>Your complete, up-to-date guide to mastering Claude Code — tuned for Claude <code>Opus 4.8</code> and CLI <code>v2.1+</code>.</b><br>
  From your first <code>claude</code> prompt to background agents, worktrees, and headless automation.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Powered%20by-Opus%204.8-D97757?style=flat-square" alt="Opus 4.8">
  <img src="https://img.shields.io/badge/Claude%20Code-v2.1%2B-1A1A1A?style=flat-square" alt="CLI v2.1+">
  <img src="https://img.shields.io/badge/Commands-70%2B-5A8F7B?style=flat-square" alt="70+ commands">
  <img src="https://img.shields.io/badge/License-MIT-3A7BD5?style=flat-square" alt="MIT License">
  <img src="https://img.shields.io/badge/PRs-welcome-D97757?style=flat-square" alt="PRs welcome">
</p>

---

> **Why this cheat sheet?** Claude Code moves fast, and a lot of guides online still reference flags that never existed or models that have since been retired. Every command, flag, and shortcut here was verified against `claude --help` (v2.1.x) and the official docs. If a command isn't real, it isn't in here.

## 📚 Table of Contents

| Level | Theme | |
|:--|:--|:--|
| 🟢 [**Level 1**](#-level-1-the-basics) | The basics — install, launch, navigate | start here |
| 🟡 [**Level 2**](#-level-2-sessions--history) | Sessions & history — continue, resume, rewind | |
| 🟠 [**Level 3**](#-level-3-models--thinking) | Models & thinking — Opus 4.8, effort, fast mode | |
| 🔴 [**Level 4**](#-level-4-tools--permissions) | Tools & permissions — allow/deny, plan mode, sandbox | |
| 🔵 [**Level 5**](#-level-5-memory--context) | Memory & context — `CLAUDE.md`, compaction | |
| 🟣 [**Level 6**](#-level-6-customize-claude) | Customize — skills, subagents, hooks | |
| 🟤 [**Level 7**](#-level-7-mcp--integrations) | MCP & integrations — servers, IDE, Chrome, plugins | |
| ⚫ [**Level 8**](#-level-8-headless--automation) | Headless & automation — `-p`, JSON, CI | |
| ⚪ [**Level 9**](#-level-9-parallel--background) | Parallel & background — agents, worktrees, `/batch` | |
| 🔘 [**Level 10**](#-level-10-teams--production) | Teams & production — settings, tokens, schedules | power user |

**Reference:** [CLI Commands](#cli-commands) · [CLI Flags](#cli-flags) · [Slash Commands](#slash-commands) · [Keyboard Shortcuts](#keyboard-shortcuts) · [Vim Mode](#vim-mode)
**More:** [🤖 Subagents](subagents.md) · [💡 Best Practices](#-best-practices) · [🧰 Troubleshooting](#-troubleshooting)

---

## ⚡ Quick Start

Install, sign in, and start `claude` from the root of the project you want to work on.

```bash
# macOS, Linux, or WSL — recommended native installer
curl -fsSL https://claude.ai/install.sh | bash
```

```powershell
# Windows PowerShell
irm https://claude.ai/install.ps1 | iex
```

```bash
# Package managers
brew install --cask claude-code        # macOS (Homebrew)
winget install Anthropic.ClaudeCode    # Windows (WinGet)
```

Then sign in and launch:

```bash
claude auth login     # sign in to your Anthropic account
claude --version      # verify the install
claude                # start an interactive session
```

> 💡 First time in a repo? Run `/init` inside Claude Code to generate a starter `CLAUDE.md`, then start asking it to build things.

---

## 🟢 Level 1: The Basics

*Install, launch, and find your way around.*

### Start a session

```bash
claude                          # interactive session in the current directory
claude "summarize this project" # start with an initial prompt
claude -p "explain src/auth.ts" # print mode: run once, print the answer, exit
cat error.log | claude -p "find the root cause"   # pipe content in
```

### Navigate inside a session

```text
/help          Show all commands and shortcuts
/status        Version, model, account, and connectivity
/context       Visualize where your context window is going
/clear         Start a fresh conversation (the old one stays resumable)
/compact       Summarize the conversation to free up context
/config        Open the settings panel (theme, model, editor mode…)
/doctor        Diagnose your installation; press f to let Claude fix issues
/exit          Quit (alias: /quit)
```

### Essential keyboard shortcuts

```text
Esc            Interrupt Claude mid-turn (keeps work done so far)
Esc Esc        Clear the input draft — or, when empty, open the rewind menu
Ctrl+C         Interrupt; if idle, clear input, then exit on a second press
Ctrl+O         Toggle the transcript viewer (see full tool calls)
Ctrl+R         Reverse-search your prompt history
Ctrl+L         Redraw the screen if the display gets garbled
Shift+Tab      Cycle permission modes (default → acceptEdits → plan → …)
@              Mention a file path (autocomplete)
!              Shell mode — run a command and add its output to context
#              Quick-add a fact to CLAUDE.md memory
```

> See the full [keyboard shortcut](#keyboard-shortcuts) and [Vim mode](#vim-mode) tables below.

---

## 🟡 Level 2: Sessions & History

*Nothing is lost — continue, resume, branch, or rewind any conversation.*

### Continue & resume

```bash
claude -c                       # continue the most recent conversation here
claude --continue               # (same thing, long form)
claude -r "auth-refactor" "finish the PR"   # resume by ID or name + new prompt
claude --resume                 # open an interactive session picker
claude -c -p "check for type errors"        # continue in print mode
```

### Name, branch & fork

```bash
claude -n "auth-feature"        # name a session at startup
```

```text
/rename auth-feature            Rename the current session (shows on the prompt bar)
/branch try-streaming           Branch the conversation; original stays under /resume
/resume                         Jump back to any past conversation
/export notes.txt               Export the transcript to a file
```

### Rewind (time travel) ⏪

```text
Esc Esc          Open the rewind menu when the input is empty
/rewind          Restore code and/or conversation to an earlier point
```

Claude Code checkpoints your code before each edit, so you can roll back files, the conversation, or both — then take a different path. Aliases: `/checkpoint`, `/undo`.

### Resume from a pull request

```bash
claude --from-pr 123            # resume the session that opened PR #123
```

---

## 🟠 Level 3: Models & Thinking

*Dial in how much horsepower each turn gets.*

### Pick a model

```bash
claude --model opus             # latest Opus (currently Opus 4.8)
claude --model sonnet           # latest Sonnet
claude --model claude-opus-4-8  # pin an exact model by full name
```

```text
/model           Open the model picker (press s to switch for this session only)
Alt+P / Option+P Switch model without clearing your prompt
```

> **About Opus 4.8** — `opus` always resolves to the newest Opus model. Pin `claude-opus-4-8` for reproducibility, or `claude-opus-4-8[1m]` to request the 1M-token context window for very large codebases.

### Effort level

Effort controls how much reasoning Claude spends per turn.

```bash
claude --effort high            # low · medium · high · xhigh · max
```

```text
/effort high         Set effort for the session (also: an interactive slider)
/effort ultracode    xhigh reasoning + automatic workflow orchestration
/effort auto         Reset to the model's default
```

> `max` and `ultracode` are session-only. **ultracode** lets Claude fan work out across background subagents on its own — great for big, exhaustive tasks.

### Extended thinking & fast mode

```text
Alt+T / Option+T   Toggle extended thinking (deeper step-by-step reasoning)
Alt+O / Option+O   Toggle fast mode (faster Opus output, same model)
/fast on           Turn fast mode on/off explicitly
```

> **Fast mode** keeps you on Opus (4.8 / 4.7 / 4.6) but streams faster — it does *not* downgrade to a smaller model.

### Fallbacks (headless)

```bash
claude -p --fallback-model sonnet "query"   # auto-fall back if the model is overloaded
```

---

## 🔴 Level 4: Tools & Permissions

*Decide what Claude can run on its own — and what it must ask about.*

### Permission modes (`Shift+Tab` to cycle)

```bash
claude --permission-mode plan          # plan first, don't touch anything yet
claude --permission-mode acceptEdits   # auto-accept file edits
claude --permission-mode auto          # auto-approve safe calls, ask on risky ones
```

| Mode | What it does |
|:--|:--|
| `default` | Ask before anything that writes or runs |
| `plan` | Read-only: research and propose a plan, change nothing |
| `acceptEdits` | Auto-accept edits to files |
| `auto` | Built-in classifier auto-approves safe actions |
| `bypassPermissions` | Skip all checks — sandboxes only |

### Allow / deny specific tools

```bash
# Run these without prompting
claude --allowedTools "Bash(git log:*)" "Bash(git diff:*)" "Read"

# Always deny these
claude --disallowedTools "Bash(rm:*)" "Bash(sudo:*)"

# Restrict the toolset entirely
claude --tools "Bash,Edit,Read"        # only these; "" disables all
```

```text
/permissions    Open the interactive allow / ask / deny rule manager
```

### Plan mode & sandbox

```text
/plan fix the auth bug    Enter plan mode and seed it with a task
/sandbox                  Toggle sandbox mode (supported platforms)
```

> ⚠️ `--dangerously-skip-permissions` bypasses every check. Use it only in an isolated sandbox with no network access.

---

## 🔵 Level 5: Memory & Context

*Teach Claude your project once; keep the context window lean.*

### `CLAUDE.md` memory files

```text
/init       Generate a starter CLAUDE.md for the repo
/memory     Edit memory files and manage auto-memory
#           Type # at the start of a prompt to append a fact to CLAUDE.md
```

Memory lives in `CLAUDE.md` and loads automatically every session:

```text
./CLAUDE.md            Project memory (commit this — shared with your team)
./CLAUDE.local.md      Personal project memory (git-ignored)
~/.claude/CLAUDE.md    Global memory (applies to every project)
```

### Manage the context window

```text
/context        See exactly what's filling the window (grid view)
/compact        Summarize history to reclaim space (keeps the thread going)
/compact keep the API design decisions     ← focus the summary
/clear          Wipe context and start fresh (old session stays resumable)
```

> 🧠 Rule of thumb: `/clear` between unrelated tasks, `/compact` when one long task fills up. Watch the gauge with `/context`.

---

## 🟣 Level 6: Customize Claude

*Skills, subagents, and hooks turn Claude Code into your tool.*

### Custom slash commands & skills

A file at `.claude/commands/deploy.md` **or** `.claude/skills/deploy/SKILL.md` both create `/deploy`.

```markdown
---
description: Run the test suite and summarize failures
---
Run `npm test`, then summarize any failures and suggest fixes.
Arguments: $ARGUMENTS
```

```text
/skills          List available skills (press t to sort by token cost)
/deploy          Invoke your custom command
/reload-skills   Pick up skills added on disk without restarting
```

> Use `$ARGUMENTS` for everything after the command, or `$1`, `$2`… for positional args. Subfolders namespace commands (`.claude/commands/git/sync.md` → `/git:sync`).

### Subagents

Specialized agents with their own context window, tools, and system prompt — perfect for delegating focused work (review, debugging, research).

```text
/agents          Create and manage subagents interactively
```

```bash
# Define one inline for a single session
claude --agents '{"reviewer":{"description":"Reviews code","prompt":"You are a meticulous code reviewer"}}'
```

📂 **See [subagents.md](subagents.md)** for ready-to-use agent definitions you can drop into `.claude/agents/`.

### Hooks

Run your own shell commands on lifecycle events (before a tool runs, after an edit, on session start) via `.claude/settings.json`.

```text
/hooks      View and configure hooks for tool events
```

---

## 🟤 Level 7: MCP & Integrations

*Connect Claude Code to the rest of your stack.*

### Model Context Protocol (MCP)

```bash
claude mcp                              # add / list / manage MCP servers
claude --mcp-config ./servers.json      # load servers from a file for this run
claude --strict-mcp-config --mcp-config ./servers.json   # use only these
```

```text
/mcp        Manage server connections and OAuth from inside a session
```

> MCP servers can expose **prompts** that show up as commands: `/mcp__<server>__<prompt>`.

### IDE, Chrome & GitHub

```bash
claude --ide                  # auto-connect to your IDE on startup
claude --chrome               # enable the Chrome browser integration
```

```text
/ide                   Manage IDE integrations (VS Code, JetBrains…)
/install-github-app    Set up Claude GitHub Actions on a repo
```

### Plugins

```bash
claude plugin install code-review@claude-plugins-official
claude --plugin-dir ./my-plugin        # load a local plugin for one session
```

```text
/plugin          Browse and manage plugins
/reload-plugins  Apply plugin changes without restarting
```

---

## ⚫ Level 8: Headless & Automation

*`-p` (print mode) is the gateway to scripting Claude Code.*

### Print mode & output formats

```bash
claude -p "explain this function"                 # run once, print, exit
claude -p "analyze the diff" --output-format json # structured result for scripts
claude -p "big task" --output-format stream-json --verbose   # realtime stream
claude -p --max-turns 3 "focused task"            # cap agentic turns
claude -p --max-budget-usd 5.00 "task"            # cap spend
```

### Structured output

```bash
claude -p --json-schema '{"type":"object","properties":{"bugs":{"type":"array"}}}' \
  "list bugs in this file"
```

### Faster scripted starts

```bash
claude --bare -p "query"        # skip hooks, MCP, plugins, CLAUDE.md — minimal & fast
```

### Real pipelines

```bash
git diff HEAD~1 | claude -p "review this change for security issues" > review.md
git log --oneline -20 | claude -p "write release notes from these commits"
SID=$(claude -p "start analysis" --output-format json | jq -r '.session_id')
claude -r "$SID" "now suggest refactors"          # chain by session id
```

> Extend or replace the system prompt for one-off runs with `--append-system-prompt "Always use TypeScript"` or `--system-prompt-file ./persona.txt`.

---

## ⚪ Level 9: Parallel & Background

*Run many Claudes at once — in the background, in isolated worktrees, or as a team.*

### Background agents

```bash
claude --bg "investigate the flaky test"   # detach immediately, returns a session id
claude agents                              # open the agent view to monitor them
claude attach 7c5dcf5d                     # attach to a background session
claude logs 7c5dcf5d                       # print its recent output
claude stop 7c5dcf5d                       # stop it (transcript is kept)
```

```text
/background    Detach the current session to keep running (alias: /bg)
/tasks         List background tasks in this session
Ctrl+B         Move a running bash command to the background
```

### Git worktrees (isolated parallel work)

```bash
claude -w feature-auth          # work in an isolated git worktree
claude -w 123                   # check out PR #123 into a fresh worktree
claude -w feature-auth --tmux   # plus a tmux session for it
```

### Decompose & fan out

```text
/batch migrate src/ from Solid to React
        Decompose a big change into 5–30 units, one background subagent per
        worktree, each opening its own PR.

/workflows   Watch, pause, resume, or save multi-agent workflows
```

> Turn on **ultracode** (`/effort ultracode`) to let Claude decide when to orchestrate workflows for you.

### Agent teams

```bash
claude --teammate-mode tmux     # auto · in-process · tmux display for teammates
```

---

## 🔘 Level 10: Teams & Production

*Share configuration, automate on a schedule, and run in CI.*

### Settings & sharing

```bash
claude --settings ./team-settings.json          # override settings for this run
claude --setting-sources user,project           # choose which settings load
```

Settings precedence (later wins): `~/.claude/settings.json` → `.claude/settings.json` (shared) → `.claude/settings.local.json` (personal, git-ignored).

### Long-lived auth for CI

```bash
claude setup-token              # generate a long-lived OAuth token (subscription)
# then in CI:
export CLAUDE_CODE_OAUTH_TOKEN=...
claude -p --output-format json "run the review" > out.json
```

### Schedule & repeat

```text
/schedule    Create cloud routines that run on a cron schedule (alias: /routines)
/loop 5m check if the deploy finished        Repeat a prompt on an interval
```

### Diagnostics & health

```bash
claude doctor                   # check installation & auto-updater health
claude update                   # update to the latest version
claude install stable           # (re)install a specific native build
```

---

## 📋 Reference Tables

### CLI Commands

| Command | Description |
|:--|:--|
| `claude` | Start an interactive session |
| `claude "query"` | Start with an initial prompt |
| `claude -p "query"` | Print mode — run once and exit |
| `claude -c` / `--continue` | Continue the most recent conversation here |
| `claude -r "<id\|name>"` / `--resume` | Resume a session (or open the picker) |
| `claude -n "<name>"` | Name the session at startup |
| `claude --from-pr <PR>` | Resume the session linked to a pull request |
| `claude -w [name]` / `--worktree` | Start in an isolated git worktree |
| `claude --bg "task"` | Start a background agent and return immediately |
| `claude agents` | Open the agent view (monitor background sessions) |
| `claude attach\|logs\|stop\|respawn\|rm <id>` | Manage background sessions from the shell |
| `claude auth login\|logout\|status` | Manage authentication |
| `claude setup-token` | Generate a long-lived token for CI |
| `claude mcp` | Configure MCP servers |
| `claude plugin install <name>` | Manage plugins |
| `claude update` | Update to the latest version |
| `claude install [version]` | Install/reinstall the native binary |
| `claude doctor` | Check installation health |

### CLI Flags

| Flag | Description |
|:--|:--|
| `--model <name>` | Model for this session (`opus`, `sonnet`, or a full name) |
| `--effort <level>` | `low` · `medium` · `high` · `xhigh` · `max` |
| `--fallback-model <name>` | Auto-fallback when the model is overloaded (print mode) |
| `--add-dir <dirs…>` | Grant file access to extra directories |
| `--permission-mode <mode>` | `default`/`acceptEdits`/`plan`/`auto`/`dontAsk`/`bypassPermissions` |
| `--allowedTools <tools…>` | Tools that run without prompting |
| `--disallowedTools <tools…>` | Tools/patterns to deny |
| `--tools <list>` | Restrict the available built-in toolset |
| `--dangerously-skip-permissions` | Skip all permission checks (sandboxes only) |
| `-p, --print` | Print mode (non-interactive) |
| `--output-format <fmt>` | `text` · `json` · `stream-json` |
| `--input-format <fmt>` | `text` · `stream-json` |
| `--json-schema <schema>` | Validate structured output against a JSON Schema |
| `--max-turns <n>` | Cap agentic turns (print mode) |
| `--max-budget-usd <amount>` | Cap API spend (print mode) |
| `--bare` | Minimal mode — skip hooks/MCP/plugins/CLAUDE.md for fast scripted starts |
| `--append-system-prompt <text>` | Append to the default system prompt |
| `--system-prompt[-file] <…>` | Replace the system prompt |
| `--agents <json>` | Define subagents inline |
| `--mcp-config <files…>` | Load MCP servers from JSON |
| `--strict-mcp-config` | Use only `--mcp-config` servers |
| `--settings <file\|json>` | Override settings for this session |
| `--ide` / `--chrome` | Connect IDE / enable Chrome integration |
| `--fork-session` | Resume into a new session id (copy, don't reuse) |
| `--verbose` | Verbose, turn-by-turn output |
| `-v, --version` | Print the version |

### Slash Commands

| Command | Description |
|:--|:--|
| `/help` | Show help and available commands |
| `/clear [name]` | Start a new conversation (old one stays resumable) |
| `/compact [focus]` | Summarize history to free context |
| `/context [all]` | Visualize context-window usage |
| `/rewind` | Restore code/conversation to an earlier point |
| `/resume [session]` | Resume a conversation (alias: `/continue`) |
| `/rename [name]` · `/branch [name]` | Rename / fork the current conversation |
| `/model [name]` · `/effort [level]` | Switch model / set reasoning effort |
| `/fast [on\|off]` | Toggle fast mode |
| `/plan [task]` | Enter plan mode |
| `/permissions` | Manage allow/ask/deny rules |
| `/init` · `/memory` | Create / edit `CLAUDE.md` memory |
| `/agents` · `/skills` · `/hooks` | Manage subagents / skills / hooks |
| `/mcp` · `/ide` · `/plugin` | Manage MCP / IDE / plugins |
| `/code-review [level]` · `/security-review` | Review the current diff |
| `/batch <instruction>` | Fan a large change out across worktrees |
| `/background` · `/tasks` · `/workflows` | Background & parallel work |
| `/schedule` · `/loop [interval]` | Cloud routines / repeat a prompt |
| `/usage` (`/cost`, `/stats`) | Session cost, limits, and activity |
| `/config` (`/settings`) · `/status` · `/doctor` | Settings / status / diagnostics |
| `/export [file]` · `/copy [N]` | Export transcript / copy last reply |
| `/login` · `/logout` · `/exit` | Auth / quit |

### Keyboard Shortcuts

| Shortcut | Action |
|:--|:--|
| `Esc` | Interrupt Claude mid-turn |
| `Esc` `Esc` | Clear input draft, or open the rewind menu when empty |
| `Ctrl+C` | Interrupt → clear input → exit (progressive) |
| `Ctrl+D` | Exit Claude Code |
| `Ctrl+O` | Toggle transcript viewer |
| `Ctrl+R` | Reverse-search prompt history |
| `Ctrl+L` | Redraw the screen |
| `Ctrl+T` | Toggle the task list |
| `Ctrl+B` | Background a running bash command (twice in tmux) |
| `Ctrl+G` / `Ctrl+X Ctrl+E` | Edit your prompt in `$EDITOR` |
| `Shift+Tab` | Cycle permission modes |
| `Alt+P` / `Option+P` | Switch model |
| `Alt+T` / `Option+T` | Toggle extended thinking |
| `Alt+O` / `Option+O` | Toggle fast mode |
| `@` · `!` · `#` | File mention · shell mode · add to memory |
| `Shift+Enter` / `Ctrl+J` / `\`+`Enter` | Newline in multiline input |

### Vim Mode

Enable via `/config` → Editor mode. Highlights:

| Key | Action |
|:--|:--|
| `Esc` | Enter NORMAL mode |
| `i` `a` `I` `A` `o` `O` | Insert / append / open line |
| `h` `j` `k` `l` | Move left / down / up / right |
| `w` `e` `b` · `0` `$` `^` | Word / line motions |
| `gg` `G` | Top / bottom of input |
| `x` `dd` `D` · `dw` `cw` `cc` | Delete / change |
| `yy` `p` `P` | Yank / paste |
| `u` · `.` | Undo / repeat last change |
| `v` `V` | Visual / line-visual selection |

> At the top/bottom of the input, `j`/`k` navigate prompt history instead of moving the cursor.

---

## 💡 Best Practices

**Context hygiene**
- `/clear` between unrelated tasks; `/compact` when a single long task fills up.
- Keep a tight `CLAUDE.md` — it loads every session, so facts there pay off constantly.
- Use `/context` to find what's eating the window (often a chatty MCP server).

**Permissions & safety**
- Prefer `--allowedTools` / `/permissions` allowlists over `--dangerously-skip-permissions`.
- Start risky work in `plan` mode (`Shift+Tab`), review the plan, then let it run.
- Deny destructive commands explicitly: `--disallowedTools "Bash(rm:*)" "Bash(sudo:*)"`.

**Get better answers**
- Be specific and point at files with `@`. Let Claude read before it writes.
- Reach for `--effort high`/`xhigh` (or `ultracode`) on hard problems; keep it low for quick edits.
- Turn on **fast mode** (`Alt+O`) when you want snappy Opus output on routine work.

**Automation**
- `-p --output-format json` is the backbone of every script; parse `session_id` to chain turns.
- Use `--bare` for fast, dependency-free headless runs.
- Cap cost and turns in CI with `--max-budget-usd` and `--max-turns`.

---

## 🧰 Troubleshooting

```bash
# Installation looks broken
claude doctor                         # diagnose; press f in /doctor to auto-fix
claude install stable                 # reinstall the native build
curl -fsSL https://claude.ai/install.sh | bash   # or re-run the installer

# Migrating off the deprecated npm package
npm uninstall -g @anthropic-ai/claude-code

# Auth issues
claude auth status                    # are you actually signed in?
claude auth login

# Performance / the window feels full
/context                              # see what's filling it
/compact                              # or /clear to reset

# A model is overloaded (headless)
claude -p --fallback-model sonnet "query"
```

---

## 🤖 Subagents

Drop specialized agents into `.claude/agents/` and Claude delegates the right work to them automatically. Ready-made definitions live in **[subagents.md](subagents.md)** — code reviewer, debugger, test engineer, security auditor, and more.

---

## 🤝 Contributing

Found a flag that changed, or a command that belongs here? PRs welcome.

- 🐛 Report anything inaccurate (Claude Code ships fast — things move)
- ✨ Add real, verified commands and examples
- 📝 Improve explanations and fix typos

Please verify new commands against `claude --help` and the [official docs](https://code.claude.com/docs/en/overview) before submitting.

## 📄 License

[MIT](LICENSE) — free to use, share, and adapt. Attribution appreciated.

## 🔗 Resources

- [Official Claude Code Docs](https://code.claude.com/docs/en/overview)
- [CLI Reference](https://code.claude.com/docs/en/cli-reference) · [Commands](https://code.claude.com/docs/en/commands) · [Interactive Mode](https://code.claude.com/docs/en/interactive-mode)
- [Subagents](https://code.claude.com/docs/en/sub-agents) · [Skills](https://code.claude.com/docs/en/skills) · [MCP](https://code.claude.com/docs/en/mcp)
- [Settings](https://code.claude.com/docs/en/settings) · [Memory](https://code.claude.com/docs/en/memory) · [Hooks](https://code.claude.com/docs/en/hooks)

<p align="center"><sub>⭐ If this helped you master Claude Code, star the repo and share it. Verified against Claude Code v2.1.x · Opus 4.8.</sub></p>
