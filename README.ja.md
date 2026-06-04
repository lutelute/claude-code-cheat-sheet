<p align="center">
  <img src="images/banner.svg" alt="Claude Code チートシート — Opus 4.8 対応" width="100%">
</p>

<h1 align="center">Claude Code チートシート</h1>

<p align="center">
  <b>Claude <code>Opus 4.8</code> / CLI <code>v2.1+</code> 対応の完全ガイド。</b><br>
  最初の <code>claude</code> プロンプトから、バックグラウンドエージェント・worktree・ヘッドレス自動化まで。
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Powered%20by-Opus%204.8-D97757?style=flat-square" alt="Opus 4.8">
  <img src="https://img.shields.io/badge/Claude%20Code-v2.1%2B-1A1A1A?style=flat-square" alt="CLI v2.1+">
  <img src="https://img.shields.io/badge/Commands-70%2B-5A8F7B?style=flat-square" alt="70+ commands">
  <img src="https://img.shields.io/badge/License-MIT-3A7BD5?style=flat-square" alt="MIT License">
  <img src="https://img.shields.io/badge/PRs-welcome-D97757?style=flat-square" alt="PRs welcome">
</p>

<p align="center">
  <a href="README.md">English</a> &nbsp;·&nbsp; <b>日本語</b>
</p>

---

> **このチートシートについて** ネット上のガイドの多くは、存在しないフラグや引退済みモデルを今も載せています。本書のコマンド・フラグ・ショートカットは、すべて実際の `claude --help`（v2.1.x）と公式ドキュメントで検証済みです。実在しないコマンドは載せていません。

## 📄 一枚絵チートシート

<p align="center">
  <img src="images/cheatsheet-poster.svg" alt="Claude Code 一枚絵チートシート — 12カテゴリ" width="100%">
</p>

<p align="center"><sub>CLI 全体を一望。コマンドをコピーしたい場合は、下の各セクションから検索できます。↓</sub></p>

## 📚 目次

| レベル | テーマ | |
|:--|:--|:--|
| 🟢 [**Level 1**](#-level-1-基本) | 基本 — インストール・起動・操作 | ここから |
| 🟡 [**Level 2**](#-level-2-セッションと履歴) | セッションと履歴 — 継続・再開・巻き戻し | |
| 🟠 [**Level 3**](#-level-3-モデルと思考) | モデルと思考 — Opus 4.8・effort・fast mode | |
| 🔴 [**Level 4**](#-level-4-ツールと権限) | ツールと権限 — 許可/拒否・plan mode・sandbox | |
| 🔵 [**Level 5**](#-level-5-メモリとコンテキスト) | メモリとコンテキスト — `CLAUDE.md`・圧縮 | |
| 🟣 [**Level 6**](#-level-6-カスタマイズ) | カスタマイズ — skills・subagents・hooks | |
| 🟤 [**Level 7**](#-level-7-mcp-と連携) | MCP と連携 — サーバー・IDE・Chrome・plugins | |
| ⚫ [**Level 8**](#-level-8-ヘッドレスと自動化) | ヘッドレスと自動化 — `-p`・JSON・CI | |
| ⚪ [**Level 9**](#-level-9-並列とバックグラウンド) | 並列とバックグラウンド — agents・worktrees・`/batch` | |
| 🔘 [**Level 10**](#-level-10-チームと本番) | チームと本番 — settings・token・スケジュール | 上級者 |

**ビジュアル:** [📄 一枚絵チートシート](#-一枚絵チートシート) · [🧭 モード早見表](#-どのモードを使うべき)
**リファレンス:** [CLIコマンド](#cliコマンド) · [CLIフラグ](#cliフラグ) · [スラッシュコマンド](#スラッシュコマンド) · [ショートカット](#キーボードショートカット) · [Vim](#vim-モード)
**深掘り:** [🍳 レシピ](#-レシピ) · [🧠 CLAUDE.md](#-良い-claudemd-の書き方) · [🔧 settings.json](#-settingsjson-リファレンス) · [🪝 Hooks](#-hooks) · [🔌 MCP](#-mcp-セットアップ) · [🌱 環境変数](#-環境変数) · [✨ 隠れ機能](#-隠れ機能) · [❓ FAQ](#-faq)
**その他:** [🤖 Subagents](subagents.md) · [💡 ベストプラクティス](#-ベストプラクティス) · [🧰 トラブルシュート](#-トラブルシュート)

---

## ⚡ クイックスタート

インストール → サインイン → 作業したいプロジェクトのルートで `claude` を起動します。

```bash
# macOS / Linux / WSL — 推奨のネイティブインストーラ
curl -fsSL https://claude.ai/install.sh | bash
```

```powershell
# Windows PowerShell
irm https://claude.ai/install.ps1 | iex
```

```bash
# パッケージマネージャ
brew install --cask claude-code        # macOS (Homebrew)
winget install Anthropic.ClaudeCode    # Windows (WinGet)
```

サインインして起動：

```bash
claude auth login     # Anthropic アカウントにサインイン
claude --version      # インストール確認
claude                # 対話セッション開始
```

> 💡 リポジトリで初めて使うときは、Claude Code 内で `/init` を実行して `CLAUDE.md` のひな形を生成しましょう。

---

## 🟢 Level 1: 基本

*インストール・起動・基本操作。*

### セッションを始める

```bash
claude                          # カレントディレクトリで対話セッション
claude "このプロジェクトを要約して" # 初期プロンプト付きで起動
claude -p "src/auth.ts を説明して" # print モード: 一回実行して終了
cat error.log | claude -p "原因を特定して"   # 標準入力をパイプ
```

### セッション内の操作

```text
/help          全コマンドとショートカットを表示
/status        バージョン・モデル・アカウント・接続状況
/context       コンテキストウィンドウの使用状況を可視化
/clear         新しい会話を開始（前の会話は再開可能）
/compact       会話を要約してコンテキストを節約
/config        設定パネルを開く（テーマ・モデル・エディタモード…）
/doctor        インストール診断。f で Claude に修正させる
/exit          終了（別名: /quit）
```

### 必須ショートカット

```text
Esc            応答/ツール実行を中断（途中までの作業は保持）
Esc Esc        入力をクリア — 空なら巻き戻しメニューを開く
Ctrl+C         中断 → 入力クリア → 2回目で終了
Ctrl+O         トランスクリプトビューア切替（全ツール呼び出しを表示）
Ctrl+R         プロンプト履歴を逆順検索
Ctrl+L         表示が乱れたら画面を再描画
Shift+Tab      権限モードを循環（default → acceptEdits → plan → …）
@              ファイルパスをメンション（補完）
!              シェルモード — コマンド実行して出力をコンテキストに追加
#              CLAUDE.md メモリに事実を1行追記
```

> 全[ショートカット](#キーボードショートカット)と [Vim モード](#vim-モード)は下の表を参照。

---

## 🟡 Level 2: セッションと履歴

*何も失わない — 継続・再開・分岐・巻き戻し。*

### 継続と再開

```bash
claude -c                       # このディレクトリの最新会話を継続
claude --continue               # （同じ・ロングフォーム）
claude -r "auth-refactor" "PRを仕上げて"   # ID/名前で再開 + 新プロンプト
claude --resume                 # セッションピッカーを開く
claude -c -p "型エラーをチェック"           # print モードで継続
```

### 命名・分岐・フォーク

```bash
claude -n "auth-feature"        # 起動時にセッションへ名前を付ける
```

```text
/rename auth-feature            セッション名を変更（プロンプトバーに表示）
/branch try-streaming           会話を分岐。元は /resume で戻れる
/resume                         過去の会話に戻る
/export notes.txt               トランスクリプトをファイルに書き出し
```

### 巻き戻し（タイムトラベル）⏪

```text
Esc Esc          入力が空のとき巻き戻しメニューを開く
/rewind          コードや会話を過去の地点に復元
```

Claude Code は編集前にコードをチェックポイントするので、ファイル・会話・その両方をロールバックして、別のアプローチを取れます。別名: `/checkpoint`, `/undo`。

### プルリクエストから再開

```bash
claude --from-pr 123            # PR #123 を開いたセッションを再開
```

---

## 🟠 Level 3: モデルと思考

*各ターンにどれだけの馬力を割くかを調整。*

### モデルを選ぶ

```bash
claude --model opus             # 最新の Opus（現在は Opus 4.8）
claude --model sonnet           # 最新の Sonnet
claude --model claude-opus-4-8  # フルネームで厳密に固定
```

```text
/model           モデルピッカー（s でこのセッションのみ切替）
Alt+P / Option+P プロンプトを消さずにモデル切替
```

> **Opus 4.8 について** — `opus` は常に最新の Opus を指します。再現性のためには `claude-opus-4-8` を、巨大なコードベースには 1M トークンコンテキストの `claude-opus-4-8[1m]` を指定します。

### effort レベル

effort は1ターンあたりの推論量を決めます。

```bash
claude --effort high            # low · medium · high · xhigh · max
```

```text
/effort high         セッションの effort を設定（対話スライダーもあり）
/effort ultracode    xhigh 推論 + ワークフロー自動オーケストレーション
/effort auto         モデルの既定値に戻す
```

> `max` と `ultracode` はセッション限定。**ultracode** は Claude が自分でバックグラウンドのサブエージェントに作業を展開します — 大規模で網羅的なタスクに最適。

### 拡張思考と fast mode

```text
Alt+T / Option+T   拡張思考（深いステップ推論）を切替
Alt+O / Option+O   fast mode（Opus を高速出力）を切替
/fast on           fast mode を明示的にオン/オフ
```

> **fast mode** は Opus（4.8 / 4.7 / 4.6）のまま出力を高速化します — 小さいモデルにダウングレードするわけではありません。

### フォールバック（ヘッドレス）

```bash
claude -p --fallback-model sonnet "query"   # モデル過負荷時に自動フォールバック
```

---

## 🔴 Level 4: ツールと権限

*Claude が自分で実行してよいこと・確認が必要なことを決める。*

### 権限モード（`Shift+Tab` で循環）

```bash
claude --permission-mode plan          # まず計画、まだ何も触らない
claude --permission-mode acceptEdits   # ファイル編集を自動承認
claude --permission-mode auto          # 安全な操作は自動承認、危険なものは確認
```

| モード | 内容 |
|:--|:--|
| `default` | 書き込み・実行の前に毎回確認 |
| `plan` | 読み取り専用：調査して計画を提案、何も変更しない |
| `acceptEdits` | ファイル編集を自動承認 |
| `auto` | 組み込み分類器が安全な操作を自動承認 |
| `bypassPermissions` | 全チェックをスキップ — サンドボックス専用 |

### ツールの許可 / 拒否

```bash
# 確認なしで実行
claude --allowedTools "Bash(git log:*)" "Bash(git diff:*)" "Read"

# 常に拒否
claude --disallowedTools "Bash(rm:*)" "Bash(sudo:*)"

# ツールセット自体を制限
claude --tools "Bash,Edit,Read"        # これらのみ; "" で全無効
```

```text
/permissions    対話的な許可/確認/拒否ルール管理を開く
```

### plan mode と sandbox

```text
/plan 認証のバグを直して    plan mode に入りタスクを与える
/sandbox                  sandbox モードを切替（対応プラットフォーム）
```

> ⚠️ `--dangerously-skip-permissions` はすべてのチェックを回避します。ネットワーク隔離されたサンドボックスでのみ使用してください。

---

## 🔵 Level 5: メモリとコンテキスト

*プロジェクトを一度教えれば、コンテキストは軽いまま。*

### `CLAUDE.md` メモリファイル

```text
/init       リポジトリ用の CLAUDE.md ひな形を生成
/memory     メモリファイルを編集・auto-memory を管理
#           プロンプト先頭に # を付けて CLAUDE.md に事実を追記
```

メモリは `CLAUDE.md` にあり、毎セッション自動で読み込まれます：

```text
./CLAUDE.md            プロジェクトメモリ（コミットしてチームで共有）
./CLAUDE.local.md      個人用プロジェクトメモリ（git 管理外）
~/.claude/CLAUDE.md    グローバルメモリ（全プロジェクトに適用）
```

### コンテキストウィンドウの管理

```text
/context        何がウィンドウを埋めているか（グリッド表示）
/compact        履歴を要約して空きを作る（会話は続行）
/compact API設計の決定を残して     ← 要約の焦点を指定
/clear          コンテキストを消去して新規開始（前の会話は再開可能）
```

> 🧠 目安：無関係なタスクの間は `/clear`、1つの長いタスクで埋まったら `/compact`。`/context` でゲージを確認。

---

## 🟣 Level 6: カスタマイズ

*skills・subagents・hooks で Claude Code を自分の道具にする。*

### カスタムスラッシュコマンドと skills

`.claude/commands/deploy.md` **または** `.claude/skills/deploy/SKILL.md` のどちらでも `/deploy` を作れます。

```markdown
---
description: テストを実行して失敗を要約
---
`npm test` を実行し、失敗があれば要約して修正案を出して。
引数: $ARGUMENTS
```

```text
/skills          利用可能な skills を一覧（t でトークン量ソート）
/deploy          カスタムコマンドを実行
/reload-skills   再起動せずにディスク上の skills を反映
```

> `$ARGUMENTS` でコマンド以降すべて、`$1`・`$2`… で位置引数。サブフォルダで名前空間化（`.claude/commands/git/sync.md` → `/git:sync`）。

### Subagents

独自のコンテキストウィンドウ・ツール・システムプロンプトを持つ専門エージェント。レビューやデバッグ、調査などの集中作業を委譲するのに最適。

```text
/agents          subagent を対話的に作成・管理
```

```bash
# 1セッション用にインライン定義
claude --agents '{"reviewer":{"description":"コードをレビュー","prompt":"あなたは綿密なコードレビュアーです"}}'
```

📂 **[subagents.md](subagents.md)** に、`.claude/agents/` へそのまま置けるエージェント定義集があります。

### Hooks

ライフサイクルイベント（ツール実行前・編集後・セッション開始時など）で自分のシェルコマンドを `.claude/settings.json` 経由で実行。

```text
/hooks      ツールイベント用の hooks を表示・設定
```

---

## 🟤 Level 7: MCP と連携

*Claude Code をスタックの他の部分につなぐ。*

### Model Context Protocol (MCP)

```bash
claude mcp                              # MCP サーバーの追加/一覧/管理
claude --mcp-config ./servers.json      # ファイルからサーバーを読み込み
claude --strict-mcp-config --mcp-config ./servers.json   # これだけを使う
```

```text
/mcp        セッション内でサーバー接続と OAuth を管理
```

> MCP サーバーは**プロンプト**を公開でき、コマンドとして表示されます：`/mcp__<server>__<prompt>`。

### IDE・Chrome・GitHub

```bash
claude --ide                  # 起動時に IDE へ自動接続
claude --chrome               # Chrome ブラウザ連携を有効化
```

```text
/ide                   IDE 連携を管理（VS Code, JetBrains…）
/install-github-app    リポジトリに Claude GitHub Actions を設定
```

### プラグイン

```bash
claude plugin install code-review@claude-plugins-official
claude --plugin-dir ./my-plugin        # ローカルプラグインを1セッション読み込み
```

```text
/plugin          プラグインを閲覧・管理
/reload-plugins  再起動せずにプラグイン変更を反映
```

---

## ⚫ Level 8: ヘッドレスと自動化

*`-p`（print モード）が Claude Code スクリプト化の入口。*

### print モードと出力形式

```bash
claude -p "この関数を説明して"                     # 一回実行して終了
claude -p "diff を解析" --output-format json       # スクリプト用の構造化結果
claude -p "大きなタスク" --output-format stream-json --verbose   # リアルタイムストリーム
claude -p --max-turns 3 "焦点を絞ったタスク"        # エージェントターン数を制限
claude -p --max-budget-usd 5.00 "タスク"            # コスト上限
```

### 構造化出力

```bash
claude -p --json-schema '{"type":"object","properties":{"bugs":{"type":"array"}}}' \
  "このファイルのバグを列挙"
```

### スクリプトを速く起動

```bash
claude --bare -p "query"        # hooks/MCP/plugins/CLAUDE.md をスキップ — 最小・高速
```

### 実用パイプライン

```bash
git diff HEAD~1 | claude -p "セキュリティ観点でこの変更をレビュー" > review.md
git log --oneline -20 | claude -p "コミットからリリースノートを作成"
SID=$(claude -p "解析を開始" --output-format json | jq -r '.session_id')
claude -r "$SID" "次にリファクタを提案"             # session id で連鎖
```

> 一回限りの実行ではシステムプロンプトを `--append-system-prompt "常に TypeScript を使う"` で追記、`--system-prompt-file ./persona.txt` で置換できます。

---

## ⚪ Level 9: 並列とバックグラウンド

*複数の Claude を同時に — バックグラウンド・隔離 worktree・チームで。*

### バックグラウンドエージェント

```bash
claude --bg "不安定なテストを調査"          # 即デタッチ、session id を返す
claude agents                              # エージェントビューで監視
claude attach 7c5dcf5d                     # バックグラウンドセッションにアタッチ
claude logs 7c5dcf5d                       # 最近の出力を表示
claude stop 7c5dcf5d                       # 停止（トランスクリプトは保持）
```

```text
/background    現在のセッションをデタッチして実行継続（別名: /bg）
/tasks         このセッションのバックグラウンドタスク一覧
Ctrl+B         実行中の bash コマンドをバックグラウンドへ
```

### Git worktree（隔離された並列作業）

```bash
claude -w feature-auth          # 隔離された git worktree で作業
claude -w 123                   # PR #123 を新しい worktree にチェックアウト
claude -w feature-auth --tmux   # さらに tmux セッションも
```

### 分解してファンアウト

```text
/batch src/ の全コンポーネントを Solid から React へ移行
        大きな変更を5〜30ユニットに分解し、worktree ごとに
        バックグラウンドサブエージェントを割り当て、各々が PR を作成。

/workflows   マルチエージェントワークフローを監視・一時停止・再開・保存
```

> **ultracode**（`/effort ultracode`）にすると、Claude が自分でワークフローのオーケストレーションを判断します。

### エージェントチーム

```bash
claude --teammate-mode tmux     # auto · in-process · tmux でチームメイト表示
```

---

## 🔘 Level 10: チームと本番

*設定を共有し、スケジュール実行し、CI で動かす。*

### 設定と共有

```bash
claude --settings ./team-settings.json          # この実行用に設定を上書き
claude --setting-sources user,project           # 読み込む設定ソースを選択
```

設定の優先順位（後勝ち）：`~/.claude/settings.json` → `.claude/settings.json`（共有）→ `.claude/settings.local.json`（個人・git 管理外）。

### CI 用の長期トークン

```bash
claude setup-token              # 長期 OAuth トークンを生成（サブスク必要）
# CI 側で:
export CLAUDE_CODE_OAUTH_TOKEN=...
claude -p --output-format json "レビューを実行" > out.json
```

### スケジュールと繰り返し

```text
/schedule    cron スケジュールで動くクラウドルーティンを作成（別名: /routines）
/loop 5m デプロイが終わったか確認        プロンプトを一定間隔で繰り返す
```

### 診断とヘルス

```bash
claude doctor                   # インストールと自動更新のヘルスチェック
claude update                   # 最新版へ更新
claude install stable           # 特定のネイティブビルドを（再）インストール
```

---

## 📋 リファレンス表

### CLIコマンド

| コマンド | 説明 |
|:--|:--|
| `claude` | 対話セッションを開始 |
| `claude "query"` | 初期プロンプト付きで開始 |
| `claude -p "query"` | print モード — 一回実行して終了 |
| `claude -c` / `--continue` | このディレクトリの最新会話を継続 |
| `claude -r "<id\|name>"` / `--resume` | セッションを再開（またはピッカー） |
| `claude -n "<name>"` | 起動時にセッションへ命名 |
| `claude --from-pr <PR>` | PR にひも付くセッションを再開 |
| `claude -w [name]` / `--worktree` | 隔離 git worktree で開始 |
| `claude --bg "task"` | バックグラウンドエージェントを開始して即復帰 |
| `claude agents` | エージェントビューを開く |
| `claude attach\|logs\|stop\|respawn\|rm <id>` | バックグラウンドセッションを管理 |
| `claude auth login\|logout\|status` | 認証を管理 |
| `claude setup-token` | CI 用の長期トークンを生成 |
| `claude mcp` | MCP サーバーを設定 |
| `claude plugin install <name>` | プラグインを管理 |
| `claude update` | 最新版へ更新 |
| `claude install [version]` | ネイティブバイナリを（再）インストール |
| `claude doctor` | インストール診断 |

### CLIフラグ

| フラグ | 説明 |
|:--|:--|
| `--model <name>` | このセッションのモデル（`opus`/`sonnet`/フルネーム） |
| `--effort <level>` | `low`·`medium`·`high`·`xhigh`·`max` |
| `--fallback-model <name>` | 過負荷時の自動フォールバック（print） |
| `--add-dir <dirs…>` | 追加ディレクトリへファイルアクセス許可 |
| `--permission-mode <mode>` | `default`/`acceptEdits`/`plan`/`auto`/`dontAsk`/`bypassPermissions` |
| `--allowedTools <tools…>` | 確認なしで実行するツール |
| `--disallowedTools <tools…>` | 拒否するツール/パターン |
| `--tools <list>` | 利用可能な組み込みツールを制限 |
| `--dangerously-skip-permissions` | 全権限チェックをスキップ（サンドボックス専用） |
| `-p, --print` | print モード（非対話） |
| `--output-format <fmt>` | `text`·`json`·`stream-json` |
| `--input-format <fmt>` | `text`·`stream-json` |
| `--json-schema <schema>` | 構造化出力を JSON Schema で検証 |
| `--max-turns <n>` | エージェントターン数を制限（print） |
| `--max-budget-usd <amount>` | API 支出の上限（print） |
| `--bare` | 最小モード — hooks/MCP/plugins/CLAUDE.md をスキップ |
| `--append-system-prompt <text>` | システムプロンプトに追記 |
| `--system-prompt[-file] <…>` | システムプロンプトを置換 |
| `--agents <json>` | subagent をインライン定義 |
| `--mcp-config <files…>` | JSON から MCP サーバーを読み込み |
| `--strict-mcp-config` | `--mcp-config` のサーバーだけを使う |
| `--settings <file\|json>` | この実行用に設定を上書き |
| `--ide` / `--chrome` | IDE 接続 / Chrome 連携を有効化 |
| `--fork-session` | 再開時に新しい session id にコピー |
| `--verbose` | 詳細なターンごとの出力 |
| `-v, --version` | バージョンを表示 |

### スラッシュコマンド

| コマンド | 説明 |
|:--|:--|
| `/help` | ヘルプとコマンド一覧 |
| `/clear [name]` | 新しい会話を開始（前の会話は再開可能） |
| `/compact [focus]` | 履歴を要約してコンテキストを節約 |
| `/context [all]` | コンテキスト使用状況を可視化 |
| `/rewind` | コード/会話を過去地点へ復元 |
| `/resume [session]` | 会話を再開（別名: `/continue`） |
| `/rename [name]` · `/branch [name]` | 現在の会話を改名 / 分岐 |
| `/model [name]` · `/effort [level]` | モデル切替 / 推論 effort 設定 |
| `/fast [on\|off]` | fast mode 切替 |
| `/plan [task]` | plan mode に入る |
| `/permissions` | 許可/確認/拒否ルールを管理 |
| `/init` · `/memory` | `CLAUDE.md` メモリの作成 / 編集 |
| `/agents` · `/skills` · `/hooks` | subagents / skills / hooks 管理 |
| `/mcp` · `/ide` · `/plugin` | MCP / IDE / プラグイン管理 |
| `/code-review [level]` · `/security-review` | 現在の diff をレビュー |
| `/batch <instruction>` | 大きな変更を worktree に展開 |
| `/background` · `/tasks` · `/workflows` | バックグラウンド・並列作業 |
| `/schedule` · `/loop [interval]` | クラウドルーティン / プロンプト反復 |
| `/usage` (`/cost`, `/stats`) | セッションのコスト・上限・統計 |
| `/config` (`/settings`) · `/status` · `/doctor` | 設定 / 状態 / 診断 |
| `/export [file]` · `/copy [N]` | トランスクリプト書き出し / 直近返信コピー |
| `/login` · `/logout` · `/exit` | 認証 / 終了 |

### キーボードショートカット

| ショートカット | 動作 |
|:--|:--|
| `Esc` | Claude を中断 |
| `Esc` `Esc` | 入力クリア、空なら巻き戻しメニュー |
| `Ctrl+C` | 中断 → 入力クリア → 終了（段階的） |
| `Ctrl+D` | Claude Code を終了 |
| `Ctrl+O` | トランスクリプトビューア切替 |
| `Ctrl+R` | プロンプト履歴を逆順検索 |
| `Ctrl+L` | 画面を再描画 |
| `Ctrl+T` | タスクリスト切替 |
| `Ctrl+B` | 実行中の bash をバックグラウンドへ（tmux は2回） |
| `Ctrl+G` / `Ctrl+X Ctrl+E` | プロンプトを `$EDITOR` で編集 |
| `Shift+Tab` | 権限モードを循環 |
| `Alt+P` / `Option+P` | モデル切替 |
| `Alt+T` / `Option+T` | 拡張思考の切替 |
| `Alt+O` / `Option+O` | fast mode 切替 |
| `@` · `!` · `#` | ファイルメンション · シェルモード · メモリ追記 |
| `Shift+Enter` / `Ctrl+J` / `\`+`Enter` | 複数行入力の改行 |

### Vim モード

`/config` → Editor mode で有効化。主なもの：

| キー | 動作 |
|:--|:--|
| `Esc` | NORMAL モードへ |
| `i` `a` `I` `A` `o` `O` | 挿入 / 追記 / 行頭挿入 / 開行 |
| `h` `j` `k` `l` | 左 / 下 / 上 / 右 |
| `w` `e` `b` · `0` `$` `^` | 単語 / 行 移動 |
| `gg` `G` | 入力の先頭 / 末尾 |
| `x` `dd` `D` · `dw` `cw` `cc` | 削除 / 変更 |
| `yy` `p` `P` | ヤンク / ペースト |
| `u` · `.` | 取り消し / 直前の変更を繰り返し |
| `v` `V` | ビジュアル / 行ビジュアル選択 |

> 入力の先頭/末尾では、`j`/`k` がカーソル移動の代わりにプロンプト履歴を移動します。

---

## 🍳 レシピ

実証済みの出発点。`claude -p …` の行はシェルから、その他はセッション内で打つプロンプトです。

**レビュー & 出荷**
```bash
git diff | claude -p "この diff のバグとセキュリティ問題をレビュー"   # コミット前チェック
claude -p "/review 123"          # PR #123 をローカルでレビュー
claude -p "/security-review"     # 現在のブランチをセキュリティ精査
```

**テスト & デバッグ**
```text
src/auth.ts のテストを書いて — エラー経路もカバーして実行して
このテストは不安定 — 競合を見つけて決定的にして
issue #42 のバグを再現して、根本原因を直して
```

**コードベースを理解する**
```text
/init                                       ← このリポジトリの CLAUDE.md を生成
リクエストがルーターからDBまでどう流れるか説明して
レート制限はどこで実装されている？呼び出し箇所を見せて
```

**リファクタ & 移行**
```text
この3ファイルの重複したバリデーションを1つのヘルパーに抽出して
このモジュールを JS から TS に移行、挙動は完全に同じに
/batch src/ の全コンポーネントを class から function に移行
```

**Git & 履歴**
```bash
claude -p "ステージ済み変更の conventional-commit メッセージを書いて"
git log --oneline -20 | claude -p "feat/fix 別にリリースノートを作成"
```

---

## 🧠 良い CLAUDE.md の書き方

`CLAUDE.md` は毎セッション読み込まれる、リポジトリで最も効果の高いファイル。短く・事実だけに。

| ✅ 入れる | 🚫 入れない |
|:--|:--|
| 実行・ビルド・テスト方法（"the tests" でなく `npm test`） | コードから自明なこと |
| プロジェクト規約（"X を使う、Y は使わない"） | 長い散文 — 簡潔な箇条書きに |
| アーキテクチャを3〜5行：何がどこにあるか | シークレットやトークン |
| 新人がハマる落とし穴 | 一般論 |

```text
/init        リポジトリからひな形を生成
/memory      編集（任意のメモリファイル）
#            プロンプト先頭に # で事実を1行追記
```

> 階層化：`~/.claude/CLAUDE.md`（自分・全体）→ `./CLAUDE.md`（チーム・コミット）→ `./CLAUDE.local.md`（自分・このリポジトリ・git 管理外）。

---

## 🔧 settings.json リファレンス

JSON で Claude Code を設定。優先順位（後勝ち）：**user** `~/.claude/settings.json` → **project** `.claude/settings.json` → **local** `.claude/settings.local.json` → CLI フラグ → **managed**（エンタープライズ・ロック）。`"$schema"` を入れるとエディタ補完が効きます。

| キー | 内容 |
|:--|:--|
| `model` | 既定モデル（例 `"opus"`） |
| `effortLevel` | effort を永続化：`low`·`medium`·`high`·`xhigh` |
| `permissions` | `allow` / `ask` / `deny` ルール配列（下記参照） |
| `env` | 全セッション・サブプロセスに注入する環境変数 |
| `hooks` | ライフサイクルイベントでコマンド実行（下記参照） |
| `statusLine` | カスタムステータスラインコマンド |
| `outputStyle` | システムプロンプトのスタイル（例 `"Explanatory"`） |
| `editorMode` | `"normal"` または `"vim"` |
| `autoUpdatesChannel` | `"stable"` または `"latest"` |
| `cleanupPeriodDays` | 古いセッションの保持日数（既定 `30`） |
| `attribution` | git commit / PR の co-author 表記をカスタマイズ |
| `availableModels` | `/model` ピッカーを制限 |
| `language` | 応答の優先言語（例 `"japanese"`） |

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "model": "opus",
  "effortLevel": "high",
  "permissions": {
    "allow": ["Bash(npm run test:*)", "Read(./src/**)"],
    "deny": ["Read(./.env)", "Read(./.env.*)", "Bash(curl:*)"]
  },
  "env": { "BASH_DEFAULT_TIMEOUT_MS": "300000" }
}
```

---

## 🔑 権限ルール構文

ルールは `Tool(pattern)`。**deny** が **ask** に勝ち、**ask** が **allow** に勝ちます。ルールはスコープをまたいでマージされます。

```text
Bash(npm run test:*)     "npm run test…" 系をすべて許可
Bash(git:*)              git の全サブコマンドを許可
Read(./src/**)           src/ 以下の読み取りを許可
Read(./.env)             env ファイルの読み取りを拒否
Edit                     ツール名のみは全呼び出しにマッチ
```

`/permissions` で対話的に、または実行ごとに `--allowedTools` / `--disallowedTools` で設定。

---

## 🪝 Hooks

ライフサイクルイベントで自分のシェルコマンド（または HTTP / MCP / prompt）を実行。`settings.json` の `hooks` で設定し、`/hooks` で確認。

| イベント | 発火タイミング |
|:--|:--|
| `SessionStart` | セッション開始・再開時 |
| `UserPromptSubmit` | プロンプト送信時（書き換え/ブロック可） |
| `PreToolUse` | ツール実行前 — **ブロック可** |
| `PostToolUse` | ツール成功後 |
| `PreCompact` / `PostCompact` | コンテキスト圧縮の前後 |
| `SubagentStart` / `SubagentStop` | サブエージェント実行の前後 |
| `Stop` | Claude が応答を終えたとき |
| `Notification` | Claude Code が通知を送るとき |
| `SessionEnd` | セッション終了時 |

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [{ "type": "command", "command": "npm run lint --silent" }]
      }
    ]
  }
}
```

> `PreToolUse` のコマンドが終了コード **`2`** を返すとアクションをブロックし、stderr を Claude にフィードバックします — ガードレールに便利。

---

## 🔌 MCP セットアップ

外部ツール（DB・ブラウザ・課題管理…）を Model Context Protocol でつなぎます。

```bash
# ローカル（stdio）サーバー
claude mcp add github -- npx -y @modelcontextprotocol/server-github

# リモート SSE / HTTP サーバー
claude mcp add --transport sse linear https://mcp.linear.app/sse

# 管理
claude mcp list
claude mcp get github
claude mcp remove github
```

```text
/mcp     セッション内で認証・確認・管理
```

スコープは `--scope local|project|user` で選択。project スコープのサーバーは `.mcp.json` に保存（コミットして共有）。サーバーは**プロンプト**を `/mcp__<server>__<prompt>` として、**リソース**を `@` メンションで公開できます。

---

## 🌱 環境変数

| 変数 | 用途 |
|:--|:--|
| `ANTHROPIC_API_KEY` | API キー（サブスクの代わりに Console 課金） |
| `CLAUDE_CODE_OAUTH_TOKEN` | CI 用長期トークン（`claude setup-token` で生成） |
| `ANTHROPIC_MODEL` | 既定モデル id |
| `CLAUDE_CODE_EFFORT_LEVEL` | `low`·`medium`·`high`·`xhigh`·`max`·`auto` |
| `MAX_THINKING_TOKENS` | 拡張思考のトークン予算 |
| `BASH_DEFAULT_TIMEOUT_MS` | bash の既定タイムアウト（ms） |
| `CLAUDE_CODE_USE_BEDROCK` / `CLAUDE_CODE_USE_VERTEX` | AWS Bedrock / Google Vertex 経由 |
| `DISABLE_TELEMETRY` | テレメトリを無効化 |
| `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS` | バックグラウンド bash を無効化 |

シェルで設定するか、プロジェクトごとに `settings.json` の `env` キーで設定します。

---

## ✨ 隠れ機能

地味だが効く小技：

| これをすると | こうなる |
|:--|:--|
| `/btw <質問>` | 履歴に残さず、文脈から答える脇道の質問 |
| `/rewind`（または `Esc` `Esc`） | コード/会話を任意の過去地点へロールバック |
| `/copy [N]` | Claude の直近（or N番目）の返信をコピー、コードブロック選択も |
| `/goal <条件>` | 条件を満たすまでターンをまたいで作業継続 |
| サジェスト上で `Tab` | グレー表示の次手サジェストを採用 |
| `Ctrl+T` | ライブのタスクリストを切替 |
| `Ctrl+B` | 長い bash コマンドをバックグラウンドへ |
| `! cmd` | シェルコマンド実行＋出力を文脈に追加（`Tab` で補完） |
| `Ctrl+G` | プロンプトを `$EDITOR` で編集 |
| `/context all` | 何がウィンドウを埋めているか項目別に表示 |
| `/insights` · `/recap` | セッション分析 · 「何が起きたか」一行要約 |

---

## ❓ FAQ

**どのモデルを使う？** 難しい推論や大きなリファクタは `opus`、日常の速さは `sonnet`。再現性には `claude-opus-4-8` を固定。

**`-p` と対話、どっち？** 一回限り・スクリプト化・パイプには `-p`。反復作業には対話 REPL。

**何でも許可を聞いてくる。** `/permissions` や `--allowedTools "Bash(git:*)"` で安全なツールを許可、または `Shift+Tab` で `acceptEdits` に。

**コンテキストが一杯になった。** `/compact` で要約して継続、無関係なタスクの間は `/clear`。`/context` で原因を確認。

**ファイルを壊された？** いいえ — 編集前にチェックポイントします。`Esc` `Esc` → 巻き戻し、または `/rewind`。

**CI でどう動かす？** `claude setup-token` → `CLAUDE_CODE_OAUTH_TOKEN` を設定 → `claude -p --output-format json`。

**npm パッケージはまだ使える？** ネイティブインストーラ推奨。npm を使っていたら `npm uninstall -g @anthropic-ai/claude-code` してネイティブで入れ直し。

---

## 💡 ベストプラクティス

**コンテキスト衛生**
- 無関係なタスクの間は `/clear`、1つの長いタスクで埋まったら `/compact`。
- `CLAUDE.md` は簡潔に — 毎セッション読まれるので、事実が常に効きます。
- `/context` でウィンドウを食っているもの（多くは饒舌な MCP サーバー）を特定。

**権限と安全**
- `--dangerously-skip-permissions` より `--allowedTools` / `/permissions` の許可リストを。
- 危険な作業は `plan` モード（`Shift+Tab`）で始め、計画を確認してから実行。
- 破壊的コマンドは明示的に拒否：`--disallowedTools "Bash(rm:*)" "Bash(sudo:*)"`。

**より良い回答を得る**
- 具体的に、`@` でファイルを指す。書く前に読ませる。
- 難問は `--effort high`/`xhigh`（or `ultracode`）、軽い編集は低めに。
- 定型作業には **fast mode**（`Alt+O`）で Opus をキビキビと。

**自動化**
- `-p --output-format json` が全スクリプトの土台。`session_id` を解析してターンを連鎖。
- ヘッドレスの高速・無依存実行には `--bare`。
- CI では `--max-budget-usd` と `--max-turns` でコストとターンを上限設定。

---

## 🧰 トラブルシュート

```bash
# インストールが壊れて見える
claude doctor                         # 診断（/doctor で f を押すと自動修正）
claude install stable                 # ネイティブビルドを再インストール
curl -fsSL https://claude.ai/install.sh | bash   # またはインストーラを再実行

# 非推奨の npm パッケージから移行
npm uninstall -g @anthropic-ai/claude-code

# 認証の問題
claude auth status                    # 本当にサインインしている？
claude auth login

# パフォーマンス / ウィンドウが一杯
/context                              # 何が埋めているか確認
/compact                              # または /clear でリセット

# モデルが過負荷（ヘッドレス）
claude -p --fallback-model sonnet "query"
```

---

## 🤖 Subagents

`.claude/agents/` に専門エージェントを置くと、Claude が適切な作業を自動で委譲します。すぐ使える定義集は **[subagents.md](subagents.md)** に — コードレビュアー、デバッガー、テストエンジニア、セキュリティ監査ほか。

---

## 🤝 コントリビュート

変わったフラグや、ここに載せるべきコマンドを見つけたら PR 歓迎。

- 🐛 不正確な点の報告（Claude Code は高速に進化します）
- ✨ 実在する検証済みコマンド・例の追加
- 📝 説明の改善・誤字修正

新しいコマンドは提出前に `claude --help` と[公式ドキュメント](https://code.claude.com/docs/en/overview)で検証してください。

## 📄 ライセンス

[MIT](LICENSE) — 自由に使用・共有・改変可。クレジットがあると嬉しいです。

## 🔗 リソース

- [Claude Code 公式ドキュメント](https://code.claude.com/docs/en/overview)
- [CLI リファレンス](https://code.claude.com/docs/en/cli-reference) · [コマンド](https://code.claude.com/docs/en/commands) · [対話モード](https://code.claude.com/docs/en/interactive-mode)
- [Subagents](https://code.claude.com/docs/en/sub-agents) · [Skills](https://code.claude.com/docs/en/skills) · [MCP](https://code.claude.com/docs/en/mcp)
- [Settings](https://code.claude.com/docs/en/settings) · [Memory](https://code.claude.com/docs/en/memory) · [Hooks](https://code.claude.com/docs/en/hooks)

<p align="center"><sub>⭐ Claude Code 習得に役立ったら、リポジトリに star を。Claude Code v2.1.x · Opus 4.8 で検証済み。</sub></p>
