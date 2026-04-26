---
description: daily-notesに直接保存する
argument-hint: "[--done|-d] [--thoughts|-t] [--todo] <topic>"
allowed-tools:
  - Bash
  - Read
  - Write
---

# 概要

この会話で行った内容をまとめ、obsidian-vault の daily-notes に保存する。
Obsidian CLI を優先的に使用する（Obsidian が起動している必要がある）。

# Obsidian CLI

すべてのコマンドに `vault=local` を付与する。

## Daily Notes コマンド

| 操作 | コマンド |
|------|---------|
| 今日のノートを読む | `obsidian vault=local daily:read` |
| 末尾に追記 | `obsidian vault=local daily:append content="..."` |
| 先頭に追記 | `obsidian vault=local daily:prepend content="..."` |
| ファイルパス取得 | `obsidian vault=local daily:path` |

## content パラメータの注意

- `\n` で改行、`\t` でタブ
- 空白を含む値はクォートで囲む: `content="text with spaces"`

# オプション

オプションを指定すると、該当セクションに追記される。

| オプション | 省略形 | セクション |
|-----------|--------|-----------|
| --done | -d | Done |
| --thoughts | -t | Thoughts |
| --todo | なし | TODO |

オプションなしの場合は、内容に応じて適切なセクションに振り分ける。

# まとめる範囲の判断

1. `$ARGUMENTS` にトピックが指定されている場合: その内容に関連する調査をまとめる
2. 空の場合: 直近の調査タスク（ユーザーが最後に依頼した調査や質問とその結果）をまとめる

# ワークフロー

1. 会話のコンテキストから、まとめるべき内容を特定
2. オプションを解析し、保存先セクションを決定
3. CLI で今日のノートを読み取る:
   ```bash
   obsidian vault=local daily:read
   ```
4. 既存内容を解析し、該当セクションの位置を特定
5. 保存方法を決定:
   - **末尾への単純追記**: `daily:append` を使用
   - **セクション内への挿入**: `daily:path` でパス取得 → Read → 編集 → Write
6. 保存したファイルパスをユーザーに報告

## ファイルが存在しない場合

`daily:append` を実行すると、Obsidian が daily note テンプレートを適用して自動作成する。

## CLI でエラーが出た場合（フォールバック）

Obsidian が起動していない場合は、従来の方法で保存する:
1. `daily:path` が使えないため、`~/obsidian-vault/local/daily-notes/` 配下に保存
2. ファイルパス形式はユーザーに確認する

# Daily Notes フォーマット

h1見出しは不要（ファイル名がObsidian上でタイトルとして表示されるため）

```markdown
## TODO

- [ ] foo
- [ ] bar

## Done

- foo
	- 詳細が必要なものは別途ドキュメント化して[[link]]する
- bar

## Thoughts

自由記述
```

# Wikilink形式

Obsidianでは `[[wikilink]]` 形式で内部リンクを作成する：

- `[[ページ名]]` - 別ページへのリンク
- `[[ページ名#見出し]]` - 特定の見出しへのリンク
- `[[ページ名|表示テキスト]]` - エイリアス付きリンク

既存のページがある場合は積極的にリンクを張る。
