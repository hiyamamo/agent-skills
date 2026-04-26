---
description: 調査内容をobsidian-vaultに直接保存する
argument-hint: "<topic>"
allowed-tools:
  - Bash
  - Write
  - AskUserQuestion
---

# 概要

この会話で行った調査内容や作業サマリをまとめ、obsidian-vault に保存する。
Obsidian CLI を優先的に使用する（Obsidian が起動している必要がある）。

# Obsidian CLI

すべてのコマンドに `vault=local` を付与する。

## 主要コマンド

| 操作 | コマンド |
|------|---------|
| ノート作成 | `obsidian vault=local create name="..." content="..." path="folder/"` |
| テンプレートから作成 | `obsidian vault=local create name="..." template="..." path="folder/"` |
| 全文検索 | `obsidian vault=local search query="..." format=json limit=5` |
| テンプレート一覧 | `obsidian vault=local templates` |
| ノート読み取り | `obsidian vault=local read file="Note Name"` |
| ノートに追記 | `obsidian vault=local append file="Note Name" content="..."` |
| ファイル一覧 | `obsidian vault=local files folder="フォルダ名"` |
| フォルダ一覧 | `obsidian vault=local folders` |

## content パラメータの注意

- `\n` で改行、`\t` でタブ
- 空白を含む値はクォートで囲む: `name="My Note"`
- `name` は拡張子なし（自動的に .md が付与される）
- 内容が長い・複雑な場合は Write ツールにフォールバック

# まとめる範囲の判断

1. `$ARGUMENTS` にトピックが指定されている場合: その内容に関連するものをまとめる
2. 空の場合: 直近のタスク（調査、質問、作業とその結果）をまとめる

# ワークフロー

1. 会話のコンテキストから、まとめるべき内容を特定
2. 重複チェック: 関連トピックを検索し、既存ノートがあればユーザーに確認
   ```bash
   obsidian vault=local search query="関連キーワード" format=json limit=5
   ```
3. 内容を Obsidian 形式の Markdown でまとめる
4. 内容に応じて適切なフォルダ名・ファイル名を決定（不明な場合はユーザーに確認）
5. CLI `create` でノートを作成:
   ```bash
   obsidian vault=local create name="ファイル名" content="..." path="フォルダ/"
   ```
6. CLI でエラーが出た場合（Obsidian 未起動など）は Write ツールで `~/obsidian-vault/local/{フォルダ}/{ファイル名}.md` に保存
7. 作成したファイルパスをユーザーに報告

# フォルダ・ファイル命名

内容に応じてその都度適切に決定する。迷う場合はユーザーに確認してよい。

- フォルダ例: `調査メモ/`, `作業ログ/`, `設計メモ/`, `プロジェクト名/` など
- ファイル名: 内容を表す簡潔な名前
- 日付プレフィックス: 必要に応じて `YYYY-MM-DD-` 形式
- ケバブケースまたは日本語OK
- 例: `claude-code-tmux-notification.md`, `2026-01-15-rails8-upgrade.md`

# Obsidian形式

- 見出し: `#`, `##`, `###` を使用
- 内部リンク: `[[ファイル名]]` 形式で既存ページへリンク
- タグ: 必要に応じて `#tag` を使用
- コードブロック: バッククォート3つで囲む
