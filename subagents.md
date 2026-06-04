<p align="center">
  <img src="images/banner.svg" alt="Claude Code チートシート — Subagents" width="100%">
</p>

<h1 align="center">🤖 Subagents</h1>

<p align="center">
  <b>Claude Code 用のすぐ使える subagent 定義集。</b><br>
  独自のコンテキストウィンドウ・ツール・システムプロンプトを持つ専門エージェント。Claude が適切な作業を適切なエージェントへ自動で委譲します。
</p>

<p align="center"><a href="README.md">← チートシートに戻る</a> &nbsp;·&nbsp; <a href="subagents.en.md">English</a></p>

---

## subagent とは？

**subagent** は、Claude Code が集中的なタスクを任せられる独立した AI アシスタントです。それぞれが**自分のコンテキストウィンドウ**で動き（メインの会話を汚しません）、**特定のツール**に限定でき、**自分のシステムプロンプト**に従います。

委譲に使いましょう：コードレビュー、デバッグ、テスト作成、セキュリティ監査など。タスクが `description` に合致すると Claude が自動で選び、明示的に呼ぶこともできます。

```text
/agents      subagent を対話的に作成・編集・管理
```

## 置き場所

| 場所 | スコープ |
|:--|:--|
| `.claude/agents/` | **プロジェクト** subagent — コミットしてチームで共有 |
| `~/.claude/agents/` | **個人** subagent — 全プロジェクトで利用可 |

subagent は YAML フロントマター付きの Markdown ファイルです：

```markdown
---
name: code-reviewer
description: コードを書いた/変えた直後に、バグと品質をレビューするときに使う。
tools: Read, Grep, Glob, Bash   # 任意 — 省略すると全ツール継承
model: sonnet                   # 任意 — sonnet | opus | haiku | inherit
---

あなたはシニアコードレビュアーです。呼ばれたら：
1. `git diff` を実行して変更点を確認。
2. 正確性・セキュリティ・可読性・テストをレビュー。
3. 深刻度別に問題を、それぞれ具体的な修正案とともに報告。
```

### フロントマターの項目

| 項目 | 必須 | 備考 |
|:--|:--|:--|
| `name` | ✅ | 小文字・ハイフン区切りの識別子 |
| `description` | ✅ | **いつ**使うか — Claude が自動委譲の判断に読む。具体的に。 |
| `tools` | — | カンマ区切りの許可リスト。省略で全ツール継承。 |
| `model` | — | `sonnet`・`opus`・`haiku`・`inherit`（既定：設定済みの subagent モデル） |

> 💡 `description` はトリガーとして書く（「〜のときに使う」「〜の直後に使う」「〜には必ず使う」）。これが自動委譲の精度を左右します。

## これらのエージェントを導入する

```bash
# プロジェクトのルートで
mkdir -p .claude/agents
cp path/to/subagents/*.md .claude/agents/
```

そして Claude Code を再起動（または `/reload-skills`）すれば使えます。

### または1セッション用にインライン定義

```bash
claude --agents '{
  "reviewer": {
    "description": "バグと品質をレビュー",
    "prompt": "あなたは綿密なシニアコードレビュアーです。"
  }
}'
```

## 収録エージェント

| エージェント | 用途 | 既定モデル |
|:--|:--|:--|
| [`code-reviewer`](subagents/code-reviewer.md) | diff のバグ・セキュリティ・品質レビュー | sonnet |
| [`debugger`](subagents/debugger.md) | エラー・テスト失敗・クラッシュの根本原因究明 | sonnet |
| [`test-engineer`](subagents/test-engineer.md) | テストスイートの作成・強化 | sonnet |
| [`security-auditor`](subagents/security-auditor.md) | 出荷前の脆弱性監査 | opus |
| [`refactor-specialist`](subagents/refactor-specialist.md) | 挙動を変えずにコードを再構成 | sonnet |
| [`docs-writer`](subagents/docs-writer.md) | README・API ドキュメント・docstring 作成 | sonnet |

> 個別の定義ファイル（`subagents/*.md`）は、そのままコピーして使えるよう英語のシステムプロンプトで書かれています。

---

## 自分で書くときのコツ

- **1エージェント1仕事。** 何でも屋より、的を絞ったエージェントが勝ちます。
- **最小権限。** 仕事に必要な `tools` だけ与える（レビュアーに `Write` はほぼ不要）。
- **モデルをタスクに合わせる。** 重い推論（セキュリティ・設計）は `opus`、定型は `sonnet`、安く速くは `haiku`。
- **出力契約で締める。** 報告の書式を指定すると、戻ってきた結果を使いやすくなります。

<p align="center"><a href="README.md">← チートシートに戻る</a> &nbsp;·&nbsp; <a href="subagents.en.md">English</a></p>
