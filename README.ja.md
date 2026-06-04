<p align="center">
  <img src="images/banner.ja.svg" alt="Claude Code チートシート — Opus 4.8 対応" width="100%">
</p>

<h1 align="center">Claude Code チートシート</h1>

<p align="center">
  <b>コマンドの羅列じゃない — 「中の人が本当にやっている使い方」のチートシート。</b><br>
  効く実践ワークフロー・神設定・精度の上げ方・ハマる落とし穴まで。<code>Opus 4.8</code> / CLI <code>v2.1+</code> 対応。
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Powered%20by-Opus%204.8-D97757?style=flat-square" alt="Opus 4.8">
  <img src="https://img.shields.io/badge/Claude%20Code-v2.1%2B-1A1A1A?style=flat-square" alt="CLI v2.1+">
  <img src="https://img.shields.io/badge/Verified-claude%20--help-5A8F7B?style=flat-square" alt="verified">
  <img src="https://img.shields.io/badge/License-MIT-3A7BD5?style=flat-square" alt="MIT License">
  <img src="https://img.shields.io/badge/PRs-welcome-D97757?style=flat-square" alt="PRs welcome">
</p>

<p align="center">
  <a href="README.md">English</a> &nbsp;·&nbsp; <b>日本語</b>
</p>

---

> たいていのチートシートは `--help` で見られるフラグを並べるだけ。これは **Anthropic のエンジニア自身がやっている「効く使い方」** を先頭に置き、全コマンドリファレンスは折りたたんで1クリック先に。すべて `claude --help`（v2.1.x）と公式ドキュメントで検証済み。

## 📚 目次

**🧠 まずここ:** [🏆 神ムーブ](#-神ムーブ) · [⚙️ 神設定](#️-神設定) · [🎯 精度爆上げ](#-精度爆上げ) · [😅 あるある](#-あるある)
**🗺️ 全体像:** [📄 一枚絵](#-一枚絵チートシート) · [🧭 どのモード?](#-どのモードを使う) · [⚡ クイックスタート](#-クイックスタート)
**📦 リファレンス（折りたたみ）:** [📖 コマンドガイド](#-コマンドガイド) · [📋 表](#-リファレンス表) · [🔧 深掘り](#-深掘り) · [🤖 Subagents](subagents.ja.md)

## 📄 一枚絵チートシート

<p align="center">
  <img src="images/cheatsheet-poster.ja.svg" alt="Claude Code 一枚絵チートシート — 12カテゴリ" width="100%">
</p>

## ⚡ クイックスタート

```bash
curl -fsSL https://claude.ai/install.sh | bash   # macOS/Linux/WSL  (Windows: irm https://claude.ai/install.ps1 | iex)
claude auth login                                # サインイン
cd your-project && claude                        # リポジトリで起動
```

そして `/init` で `CLAUDE.md` を生成すれば準備完了。

---

# 🧠 実践知

> Claude Code で最も大事な事実：**コンテキストウィンドウはすぐ埋まり、埋まるほど精度が落ちる。** 以下のコツのほとんどは、要するにこの文脈を守るための話。きれいに保てば Claude は冴えたまま。

## 🏆 神ムーブ

毎日 Claude Code を使い倒している人の操り方。

### 1. 調査 → 計画 → 実装 → コミット

ちょっとしたもの以外は、いきなりコードさせない。自信満々で**間違った問題**を解いてくる。思考と作業を分ける：

```text
1. Shift+Tab → plan mode。 「src/auth を読んでセッションの仕組みを説明して」（まだ編集しない）
2. 「Google OAuth を足したい。何が変わる? 計画を立てて」 → Ctrl+G で計画を直接編集
3. Shift+Tab で戻る。 「計画通り実装して、テストも書いて、実行して、失敗を直して」
4. 「わかりやすいメッセージでコミットして PR を作って」
```

> 一文で説明できる差分（タイポ・ログ行・リネーム）は計画不要。複数ファイルにまたがる・アプローチに迷うときに使う。

### 2. 検証手段を渡す 🔑

これが「**見張る**セッション」と「**任せて離れられる**セッション」の分かれ目。合否が返る仕組みを渡せば、Claude が自分でループを回す。

| ✗ こうではなく | ✓ こうする |
|:--|:--|
| 「メール検証を実装して」 | 「`validateEmail` を書いて。テスト: `a@b.com`→true, `a@.com`→false。**実装後にテストを実行して**」 |
| 「ダッシュボードをいい感じに」 | 「[スクショ] これに合わせて。**スクショを撮って比較し、差分を列挙して直して**」 |
| 「ビルドが失敗する」 | 「[エラー貼付] **根本原因**を直して。握りつぶさず、ビルド成功を確認して」 |

`/goal <条件>`（毎ターン再チェック）や **Stop hook**（スクリプトが通るまでターンを終わらせない）でさらに強制できる。「完了 ✅」ではなく**証拠**（テスト出力）を見せさせること。

### 3. 具体的に。対象を指す。

曖昧に投げれば曖昧に返る。プロンプトが精密なほど、やり直しが減る。

```text
❌ 「ログインのバグを直して」
✅ 「セッションタイムアウト後にログインが失敗すると報告あり。src/auth/ のトークン
    リフレッシュを見て、まず再現する失敗テストを書いてから直して」

❌ 「カレンダーwidgetを足して」
✅ 「@HotDogWidget.php のパターンに倣ってカレンダーwidgetを追加。新規ライブラリ禁止」
```

リッチな文脈を渡す：`@file` でファイルを含める、**スクショを貼る**（Ctrl+V）、ドキュメントの URL を渡す、データをパイプ（`cat error.log | claude -p "原因を特定して"`）。

### 4. 早く軌道修正 — そして `/clear`

タイトなフィードバックループが、一発完璧プロンプトに勝つ。ズレた瞬間に：

- **`Esc`** — 動作の途中で止める（文脈は保持、方向だけ変える）
- **`Esc` `Esc`** / **`/rewind`** — 会話・コード・両方をチェックポイントまで巻き戻す
- **`/clear`** — 無関係なタスクの間で文脈を消す

> 🔑 **2回ルール:** 同じ点を2回直したら、文脈はもう失敗の試行で汚れている。`/clear` して、学んだことを盛り込んだ鋭いプロンプトで再スタート。きれいなセッションは、長く散らかったセッションにほぼ必ず勝つ。

### 5. 調査は subagent に委譲

大きなコードベースの探索は、ファイル読み込みで文脈を埋め尽くす。それを subagent に押し込めば、**別の文脈**で探索して要約だけ返してくる。

```text
トークンリフレッシュの扱いと、再利用できる既存の OAuth ユーティリティが
あるか、subagent を使って調査して
```

### 6. 新しい Claude にレビューさせる

別セッション（fresh context）でレビューさせると、自分で書いたコードへのバイアスがない。**Writer/Reviewer** パターン、または同梱スキルで：

```text
/code-review          現在の diff のバグをレビュー（fresh subagent）
/security-review      同じく脆弱性を
```

### 7. 大きな機能はインタビューさせる

```text
[X] を作りたい。AskUserQuestion ツールで私にインタビューして — 私が見落として
いる難所を掘り下げて、完全な仕様を SPEC.md に書いて
```

そして**新しいセッション**を開いて `SPEC.md` から実装（きれいな文脈＋照合できる仕様書）。

---

## ⚙️ 神設定

毎セッション効いてくる、5分のセットアップ。

- **`/init` → `CLAUDE.md` を育てる。** 毎セッション読まれる永続的なプロジェクト文脈。（良い書き方は[精度爆上げ](#-精度爆上げ)へ）
- **承認連打を消す。** 10回目の「許可?」では誰もレビューしていない、ただ押しているだけ。安全なツールを許可リスト化、または分類器に任せる：
  ```bash
  claude --permission-mode auto          # 分類器が安全なものは承認、危険なものは確認
  # or settings.json / /permissions で許可:  "Bash(npm run test:*)", "Bash(git:*)"
  ```
- **必ず毎回起きてほしいことは hooks。** `CLAUDE.md`（お願いベース）と違い、hooks は決定的。Claude に書かせられる：*「ファイル編集のたびに eslint を走らせる hook を書いて」*
- **`gh` を入れる。** Claude が PR 作成・issue 確認・CI チェックをする最も文脈効率の良い方法。`aws`・`gcloud`・`sentry-cli` も同様。
- **`/terminal-setup`**（Shift+Enter で改行）と、macOS なら [Option を Meta に](https://code.claude.com/docs/en/terminal-config)（`Alt+P/T/O` が効く）。
- **コンテキスト使用量を表示** — [`statusLine`](https://code.claude.com/docs/en/statusline) でウィンドウの埋まり具合を常時可視化。

<details>
<summary><b>そのまま使える <code>settings.json</code> の出発点</b></summary>

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "model": "opus",
  "effortLevel": "high",
  "permissions": {
    "allow": ["Bash(npm run test:*)", "Bash(git:*)", "Read(./src/**)"],
    "deny":  ["Read(./.env)", "Read(./.env.*)", "Bash(curl:*)"]
  },
  "hooks": {
    "PostToolUse": [
      { "matcher": "Edit|Write", "hooks": [{ "type": "command", "command": "npm run lint --silent" }] }
    ]
  },
  "env": { "MAX_THINKING_TOKENS": "32000", "BASH_DEFAULT_TIMEOUT_MS": "300000" }
}
```
優先順位（後勝ち）: user `~/.claude/settings.json` → project `.claude/settings.json` → local `.claude/settings.local.json` → CLI フラグ → managed。
</details>

---

## 🎯 精度爆上げ

出力を「速く」ではなく「良く」する具体策。

- **居場所に値する `CLAUDE.md` を書く。** 毎セッション読まれるので、各行に問う：*「これを消したら Claude は間違える?」* 違うなら削る。**肥大した `CLAUDE.md` は半分無視される。** 何度も外すルールには `IMPORTANT:` / `YOU MUST` を。
- **必ず検証手段を渡す**（テスト・ビルド・スクショ）。**それっぽく見える**未検証コードが、悪い出力の最大の原因。→[神ムーブ #2](#-神ムーブ)
- **推論は要所に。** 難問は `--effort high`/`xhigh`・`/effort ultracode`・拡張思考（`Alt+T`）。定型作業は低めに。
- **具体的に、`@` で指す。** 曖昧プロンプトに対する賢いモデルより、精密な文脈が勝つ。
- **コンテキストを守る。** `/context` で何が食っているか確認、`/compact` で要約、`/clear` でタスク間リセット。埋まるほど精度が落ちる — ここが全て。
- **必要なら大きく。** 大規模コードベースは `claude-opus-4-8[1m]` で 1M トークンウィンドウを。
- **敵対的レビューを足す。** diff だけを見る fresh subagent は、書いた本人セッションが正当化してしまう穴を見つける：*「この diff を PLAN.md と照合して subagent でレビュー。スタイルではなく正しさに関わる漏れを報告して」*

---

## 😅 あるある

誰もがハマる失敗モード。早めに気づけば時間が浮く。

| 😬 罠 | ✅ 対策 |
|:--|:--|
| **何でも1会話** — 1つのチャットが無関係な5タスクを行き来し、文脈がノイズだらけ | 無関係なタスクの間は `/clear` |
| **堂々巡りの修正** — 同じミス、3回目の修正、文脈が失敗で汚染 | 2回失敗したら `/clear` ＋鋭いプロンプト |
| **500行の `CLAUDE.md`** — 長すぎて肝心のルールが埋もれ無視される | 容赦なく刈る。「常にXして」は hook に変換 |
| **検証なしの信用** — それっぽいがエッジケースを黙って飛ばすコード | テスト/スクショを渡す。検証できないなら出荷しない |
| **無限調査** — 範囲を絞らず「調べて」で200ファイル読む | 範囲を絞る、または subagent に委譲 |
| **雰囲気プロンプト** — 答えがあるのに「いい感じに」 | ファイル・制約・倣うべき例を名指しする |
| **難問に fast mode** — 推論が要る場面で速さを選ぶ | `Alt+O` をオフ、`--effort high` ＋思考へ |
| **`--dangerously-skip-permissions` が癖** | 許可リスト＋`auto` モードで速くて安全に |

---

## 🧭 どのモードを使う？

<p align="center">
  <img src="images/decision-flowchart.ja.svg" alt="意思決定フローチャート: どのモード・モデル・effort を使うか" width="100%">
</p>

---

# 📦 リファレンス

全コマンド面 — 実践知を埋もれさせないよう折りたたみ。クリックで展開。

## 📖 コマンドガイド

<details>
<summary><b>🟢 日常使い</b> — 起動・セッション・モデル・操作</summary>

```bash
# 起動
claude                          # 対話セッション
claude "このプロジェクトを要約して"
claude -p "@src/auth.ts を説明して"  # print モード: 一回実行して終了
cat error.log | claude -p "原因を特定して"

# セッションと履歴（何も失わない）
claude -c                       # このディレクトリの最新を継続
claude -r "auth-refactor" "..." # 名前/ID で再開
claude --resume                 # セッションピッカー
claude --from-pr 123            # PR #123 の裏のセッションを再開
/rewind                         # コード/会話を巻き戻し（Esc Esc も）

# モデルと思考
claude --model opus             # 最新 Opus (4.8); 1M 文脈は claude-opus-4-8[1m]
/model · /effort high           # モデル切替 / 推論 effort
Alt+T 思考 · Alt+O fast mode
```

**操作:** `/help` · `/status` · `/context` · `/clear` · `/compact` · `/config` · `/doctor`
**必須キー:** `Esc` 中断 · `Esc Esc` 巻戻し · `Shift+Tab` 権限モード · `Ctrl+O` ログ · `@` ファイル · `!` シェル · `#` メモリ
</details>

<details>
<summary><b>🟠 使いこなす</b> — 権限・メモリ・カスタマイズ</summary>

```bash
# 権限（Shift+Tab で循環: default → acceptEdits → plan → auto → …）
claude --permission-mode plan          # 読み取り専用: まず計画、何も触らない
claude --allowedTools "Bash(git:*)" "Read"
claude --disallowedTools "Bash(rm:*)" "Bash(sudo:*)"
/permissions · /sandbox

# メモリと文脈
/init · /memory                 # CLAUDE.md の作成 / 編集
#                               # プロンプト先頭に # で事実を追記
/context · /compact · /clear

# カスタマイズ
/agents                         # サブエージェント（独自の文脈＋ツール）
/skills · .claude/commands/     # カスタムコマンド（ファイル → /名前）
/hooks                          # ライフサイクルイベントでスクリプト実行
```
</details>

<details>
<summary><b>🔵 スケールさせる</b> — MCP・ヘッドレス・並列・チーム</summary>

```bash
# MCP — 外部ツール接続
claude mcp add github -- npx -y @modelcontextprotocol/server-github
claude mcp add --transport sse linear https://mcp.linear.app/sse
/mcp · claude mcp list

# ヘッドレス / 自動化
claude -p "task" --output-format json     # スクリプト用の構造化出力
claude -p --max-turns 3 --max-budget-usd 5 "task"
claude --bare -p "task"                    # 最小・高速（hooks/MCP/CLAUDE.md スキップ）

# 並列・バックグラウンド
claude --bg "不安定なテストを調査"          # バックグラウンドエージェント
claude -w feature-auth                     # 隔離 git worktree
/batch src/ を class から function コンポーネントへ移行
claude agents · claude attach|logs|stop <id>

# チーム・本番
claude setup-token                         # CI 用の長期トークン
/schedule · /loop 5m "デプロイを確認"      # cron ルーティン / 反復
```
</details>

## 📋 リファレンス表

<details>
<summary><b>CLI コマンド & フラグ</b></summary>

| コマンド | 説明 |
|:--|:--|
| `claude` / `claude "q"` / `claude -p "q"` | 対話 / プロンプト付き / 一回実行 |
| `claude -c` · `-r <id\|name>` · `--resume` | 継続 / 再開 / ピッカー |
| `claude -n <name>` · `--from-pr <PR>` | 命名 / PR から再開 |
| `claude -w [name]` · `--bg "task"` | git worktree / バックグラウンド |
| `claude agents` · `attach\|logs\|stop\|rm <id>` | バックグラウンドセッション管理 |
| `claude auth login\|logout\|status` · `setup-token` | 認証 / CI トークン |
| `claude mcp` · `plugin install <p>` · `update` · `doctor` · `install [ver]` | サーバー / プラグイン / 保守 |

| フラグ | 説明 |
|:--|:--|
| `--model` · `--effort` · `--fallback-model` | モデル / `low…max` / フォールバック |
| `--permission-mode` | `default`/`acceptEdits`/`plan`/`auto`/`dontAsk`/`bypassPermissions` |
| `--allowedTools` · `--disallowedTools` · `--tools` | 許可 / 拒否 / 制限 |
| `-p` · `--output-format` · `--input-format` | print モード / `text\|json\|stream-json` |
| `--json-schema` · `--max-turns` · `--max-budget-usd` | 構造化出力 / 上限 |
| `--bare` · `--add-dir` · `--settings` · `--mcp-config` | 最小 / dir / 設定 / MCP |
| `--append-system-prompt` · `--system-prompt[-file]` | システムプロンプト追記 / 置換 |
| `--ide` · `--chrome` · `--fork-session` · `--verbose` | 連携 / fork / 詳細 |
</details>

<details>
<summary><b>スラッシュコマンド</b></summary>

| コマンド | 説明 |
|:--|:--|
| `/help` `/status` `/doctor` `/config` | ヘルプ / 状態 / 診断 / 設定 |
| `/clear` `/compact [focus]` `/context [all]` `/rewind` | 文脈 & チェックポイント |
| `/resume` `/rename` `/branch` `/export` `/copy [N]` | セッション管理 |
| `/model` `/effort [lvl]` `/fast` | モデル / effort / fast mode |
| `/plan` `/permissions` `/sandbox` | plan モード / 権限 / sandbox |
| `/init` `/memory` | CLAUDE.md |
| `/agents` `/skills` `/hooks` `/mcp` `/ide` `/plugin` | Claude Code 拡張 |
| `/code-review [lvl]` `/security-review` `/review` | diff レビュー |
| `/batch` `/background` `/tasks` `/workflows` | 並列・バックグラウンド |
| `/schedule` `/loop [interval]` | cron ルーティン / 反復 |
| `/usage` (`/cost` `/stats`) `/btw` `/goal` `/insights` | 使用量 / 脇質問 / ゴール / 分析 |
</details>

<details>
<summary><b>キーボードショートカット & Vim</b></summary>

| キー | 動作 |
|:--|:--|
| `Esc` · `Esc Esc` | 中断 · 巻き戻しメニュー |
| `Ctrl+C` · `Ctrl+D` | 中断→クリア→終了 · 終了 |
| `Ctrl+O` · `Ctrl+R` · `Ctrl+L` | ログ · 履歴検索 · 再描画 |
| `Ctrl+T` · `Ctrl+B` | タスクリスト · bash をバックグラウンドへ |
| `Ctrl+G` | プロンプトを `$EDITOR` で編集 |
| `Shift+Tab` | 権限モード循環 |
| `Alt+P` · `Alt+T` · `Alt+O` | モデル切替 · 思考 · fast mode |
| `@` · `!` · `#` | ファイル · シェル · メモリ |
| `Shift+Enter` / `Ctrl+J` / `\`+`Enter` | 改行 |

**Vim**（`/config` → Editor mode で有効化）: `i a o` 挿入 · `h j k l` 移動 · `w b 0 $` モーション · `dd cw yy p` 編集 · `u .` 取消/繰返し · `v V` ビジュアル。
</details>

## 🔧 深掘り

<details>
<summary><b>🍳 レシピ</b> — コピペで使える出発点</summary>

```bash
git diff | claude -p "この diff のバグとセキュリティ問題をレビュー"
claude -p "/review 123"                          # PR をレビュー
git log --oneline -20 | claude -p "feat/fix 別にリリースノートを作成"
```
```text
@src/auth.ts のエラー経路をカバーするテストを書いて、実行して
リクエストがルーターから DB までどう流れるか追って
この3ファイルの重複バリデーションを1つのヘルパーに抽出して
/batch src/ の全コンポーネントを class から function に移行
```
</details>

<details>
<summary><b>🧠 良い CLAUDE.md の書き方</b></summary>

毎セッション読まれる — 短く・事実だけ・刈り込む。

| ✅ 入れる | 🚫 入れない |
|:--|:--|
| 推測できないビルド/テストコマンド | コードから自明なこと |
| 既定と違う規約 | 標準的な言語のお作法 |
| アーキテクチャを3〜5行 | 長いチュートリアル / ファイル別の説明 |
| 落とし穴・必要な環境変数 | シークレット / 頻繁に変わる情報 |

`/init` で生成 · `/memory` で編集 · `#` で事実追記 · `@path/to/file.md` でインポート。何度も外すルールには `IMPORTANT:`/`YOU MUST`。ルールが無視されるなら、たぶんファイルが長すぎる。
</details>

<details>
<summary><b>🪝 Hooks</b> — 決定的な自動化</summary>

| イベント | 発火 |
|:--|:--|
| `PreToolUse` | ツール実行前 — **ブロック可**（終了コード `2`） |
| `PostToolUse` | ツール成功後（自動 lint/format に最適） |
| `UserPromptSubmit` · `Stop` · `SessionStart`/`SessionEnd` | プロンプト / ターン終了 / セッション |
| `PreCompact` · `SubagentStop` · `Notification` | 圧縮 / サブエージェント / 通知 |

```json
{ "hooks": { "PostToolUse": [
  { "matcher": "Edit|Write", "hooks": [{ "type": "command", "command": "npm run lint --silent" }] }
] } }
```
コツ: *「ファイル編集のたびに eslint を走らせる hook を書いて」* — Claude が書いてくれる。
</details>

<details>
<summary><b>🔌 MCP</b> · <b>🌱 環境変数</b> · <b>🔑 権限構文</b></summary>

```bash
# MCP
claude mcp add github -- npx -y @modelcontextprotocol/server-github   # stdio
claude mcp add --transport sse linear https://mcp.linear.app/sse      # リモート
claude mcp list · get · remove   # (--scope local|project|user)
```
サーバーはプロンプトを `/mcp__<server>__<prompt>`、リソースを `@` メンションで公開。

**権限構文** — `Tool(pattern)`、deny > ask > allow:
`Bash(npm run test:*)` · `Bash(git:*)` · `Read(./src/**)` · `Read(./.env)`（拒否） · `Edit`（名前のみ = 全呼び出し）。

**便利な環境変数:** `ANTHROPIC_API_KEY` · `CLAUDE_CODE_OAUTH_TOKEN`（CI） · `ANTHROPIC_MODEL` · `CLAUDE_CODE_EFFORT_LEVEL` · `MAX_THINKING_TOKENS` · `BASH_DEFAULT_TIMEOUT_MS` · `CLAUDE_CODE_USE_BEDROCK`/`_USE_VERTEX` · `DISABLE_TELEMETRY`。
</details>

<details>
<summary><b>❓ FAQ</b></summary>

- **どのモデル?** 難しい推論/リファクタは `opus`、日常の速さは `sonnet`。再現性は `claude-opus-4-8` を固定。
- **`-p` と対話?** 一回限り/スクリプト/パイプは `-p`、反復は REPL。
- **何でも許可を聞く?** `/permissions` で許可リスト、または `Shift+Tab` で `acceptEdits`/`auto`。
- **文脈が一杯?** `/compact` で継続、タスク間は `/clear`、`/context` で診断。
- **ファイルを壊された?** いいえ — 編集前にチェックポイント。`Esc Esc` / `/rewind`。（git の代替ではない）
- **CI?** `claude setup-token` → `CLAUDE_CODE_OAUTH_TOKEN` → `claude -p --output-format json`。
</details>

---

## 🤖 Subagents

`.claude/agents/` に専門エージェントを置くと、Claude が自動で委譲。すぐ使える定義（レビュアー・デバッガー・テストエンジニア・セキュリティ監査…）は **[subagents.ja.md](subagents.ja.md)** に。

## 🤝 貢献 · ライセンス · リソース

PR 歓迎 — 新コマンドは `claude --help` と[公式ドキュメント](https://code.claude.com/docs/en/overview)で検証してから。ライセンスは [MIT](LICENSE)。

📚 [ベストプラクティス](https://code.claude.com/docs/en/best-practices) · [一般的ワークフロー](https://code.claude.com/docs/en/common-workflows) · [CLI リファレンス](https://code.claude.com/docs/en/cli-reference) · [設定](https://code.claude.com/docs/en/settings) · [Hooks](https://code.claude.com/docs/en/hooks) · [Subagents](https://code.claude.com/docs/en/sub-agents)

<p align="center"><sub>⭐ 役に立ったら star＆シェアを。Claude Code v2.1.x · Opus 4.8 で検証済み。</sub></p>
