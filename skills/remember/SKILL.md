---
description: CLAUDE.mdに記憶を追加・更新する
argument-hint: "[--global|-g] [--project|-p] <覚えてほしいこと>"
allowed-tools:
  - Read
  - Edit
  - AskUserQuestion
---

# 概要

ユーザーから指示された内容を CLAUDE.md に追記または更新する。
これにより、今後のセッションで Claude Code がその内容を参照できるようになる。

# オプション

| オプション | 省略形 | 対象ファイル |
|-----------|--------|-------------|
| --global | -g | グローバル設定（chezmoi: `dot_claude/CLAUDE.md`） |
| --project | -p | プロジェクト設定（カレントディレクトリの `CLAUDE.md`） |

**オプションなしの場合は、最初に AskUserQuestion でどちらに追加するか確認する。**

# 重要: 編集対象ファイル

**chezmoi 管理下のため、グローバル設定は以下のファイルを編集すること:**

- グローバル設定: `~/.local/share/chezmoi/dot_claude/CLAUDE.md`
- プロジェクト設定: 現在のプロジェクトルートの `CLAUDE.md`

**絶対に `~/.claude/CLAUDE.md` を直接編集しないこと**（chezmoi apply で上書きされる）

# ワークフロー

1. `$ARGUMENTS` からオプションと追加・更新したい内容を解析
2. オプションがない場合は AskUserQuestion で確認:
   - 「グローバル設定」: 全プロジェクト共通のルール
   - 「プロジェクト設定」: このプロジェクト固有のルール
3. 対象の CLAUDE.md を Read ツールで読み込む
4. 既存の構造を確認し、適切なセクションを特定
   - 関連するセクションが既にあれば、そこに追記
   - なければ新しいセクションを作成
5. Edit ツールで変更を適用
6. 変更内容をユーザーに報告
7. グローバル設定を編集した場合は `chezmoi apply` の実行を促す

# 記述スタイル

- 見出しは `##` から開始（`#` は使わない）
- 簡潔で具体的な指示として記述
- コード例がある場合はコードブロックで囲む
- 既存のセクションと重複する内容は統合する

# 例

```
/remember -g TypeScriptではany型を使わずunknown型を使う
/remember --project このリポジトリではpnpmを使用する
/remember 新しいルール  # → どちらに追加するか確認
```
