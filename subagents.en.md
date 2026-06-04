<p align="center">
  <img src="images/banner.svg" alt="Claude Code Cheat Sheet — Subagents" width="100%">
</p>

<h1 align="center">🤖 Subagents</h1>

<p align="center">
  <b>Ready-to-use subagent definitions for Claude Code.</b><br>
  Specialized agents with their own context window, tools, and system prompt — Claude delegates the right work to the right agent automatically.
</p>

<p align="center"><a href="README.en.md">← Back to the Cheat Sheet</a> &nbsp;·&nbsp; <a href="subagents.md">日本語</a></p>

---

## What is a subagent?

A **subagent** is a separate AI assistant Claude Code can hand a focused task to. Each one runs in its **own context window** (so it never clutters your main conversation), can be limited to **specific tools**, and follows its **own system prompt**.

Use them to delegate: code review, debugging, test writing, security audits, and more. Claude picks a subagent automatically when a task matches its `description`, or you can invoke one explicitly.

```text
/agents      Create, edit, and manage subagents interactively
```

## Where they live

| Location | Scope |
|:--|:--|
| `.claude/agents/` | **Project** subagents — commit them, shared with your team |
| `~/.claude/agents/` | **Personal** subagents — available in every project |

A subagent is just a Markdown file with YAML frontmatter:

```markdown
---
name: code-reviewer
description: Use right after writing or changing code, to review for bugs and quality.
tools: Read, Grep, Glob, Bash   # optional — omit to inherit all tools
model: sonnet                   # optional — sonnet | opus | haiku | inherit
---

You are a senior code reviewer. When invoked:
1. Run `git diff` to see what changed.
2. Review for correctness, security, readability, and tests.
3. Report issues grouped by severity, each with a concrete fix.
```

### Frontmatter fields

| Field | Required | Notes |
|:--|:--|:--|
| `name` | ✅ | Lowercase, hyphenated identifier |
| `description` | ✅ | **When** to use it — Claude reads this to auto-delegate. Be specific. |
| `tools` | — | Comma-separated allowlist. Omit to inherit every tool. |
| `model` | — | `sonnet`, `opus`, `haiku`, or `inherit` (default: configured subagent model) |

> 💡 Write the `description` as a trigger ("Use when…", "Use right after…", "MUST be used for…"). That's what makes auto-delegation reliable.

## Install these agents

```bash
# From the root of your project
mkdir -p .claude/agents
cp path/to/subagents/*.md .claude/agents/
```

Then restart Claude Code (or run `/reload-skills`) and they're ready.

### Or define one inline (single session)

```bash
claude --agents '{
  "reviewer": {
    "description": "Reviews code for bugs and quality",
    "prompt": "You are a meticulous senior code reviewer."
  }
}'
```

## Included agents

| Agent | Use it for | Default model |
|:--|:--|:--|
| [`code-reviewer`](subagents/code-reviewer.md) | Reviewing diffs for bugs, security, and quality | sonnet |
| [`debugger`](subagents/debugger.md) | Root-causing errors, test failures, and crashes | sonnet |
| [`test-engineer`](subagents/test-engineer.md) | Writing and hardening test suites | sonnet |
| [`security-auditor`](subagents/security-auditor.md) | Auditing for vulnerabilities before shipping | opus |
| [`refactor-specialist`](subagents/refactor-specialist.md) | Restructuring code without changing behavior | sonnet |
| [`docs-writer`](subagents/docs-writer.md) | Writing READMEs, API docs, and docstrings | sonnet |

---

## Tips for writing your own

- **One job per agent.** A focused agent beats a do-everything one.
- **Least privilege.** Only grant the `tools` the job needs (a reviewer rarely needs `Write`).
- **Match the model to the task.** Heavy reasoning (security, architecture) → `opus`; routine work → `sonnet`; cheap/fast → `haiku`.
- **End with an output contract.** Tell the agent exactly how to format its findings so its handback is easy to act on.

<p align="center"><a href="README.en.md">← Back to the Cheat Sheet</a> &nbsp;·&nbsp; <a href="subagents.md">日本語</a></p>
