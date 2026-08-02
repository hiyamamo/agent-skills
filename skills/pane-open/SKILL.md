---
description: herdr の pane を分割してファイルを nvim で開く。「paneで開いて」で発動
argument-hint: "<file-path>"
allowed-tools:
  - Bash
---

# 概要

herdr（terminal workspace manager）の socket API を使い、現在の pane の隣に
新しい pane を作ってファイルを nvim（read-only）で表示する。
「〜を pane で開いて」「分割して見せて」と言われたら引数のファイルに対して実行する。

herdr が動いていない環境（`herdr status` が失敗する）ではこのスキルは使えない。
その場合はファイルパスを提示して手動で開いてもらう。

# 手順

1. **pane を分割する**（右 40%、フォーカスは移さない）:

   ```bash
   herdr pane split --current --direction right --ratio 0.4 --no-focus
   ```

   出力は JSON。`result.pane.pane_id`（例: `w1:p1D`）を控える。

2. **nvim を read-only で起動する**:

   ```bash
   herdr pane run <pane_id> "nvim -R <file-path>"
   ```

3. **表示を確認する**（nvim がプラグイン通知等で "Press ENTER" のまま
   止まっていることがある）:

   ```bash
   herdr pane read <pane_id> --lines 8
   ```

   "Press ENTER" が見えたら Enter を送って消す:

   ```bash
   herdr pane send-keys <pane_id> Enter
   ```

# バリエーション

- **複数ファイル**: 1つの pane で `nvim -R file1 file2` として開き、
  「`:bn` / `:bp` で切り替えられます」と伝える。ファイルごとに pane を
  増やさない（狭くなる）
- **tab で開きたいと言われたら**: `herdr tab create --cwd <dir> --label <名前>`
  → 返ってきた tab の pane に `pane run`
- **「pane 閉じて」と言われたら**: `herdr pane close <pane_id>`
- **編集もしたいと言われたら**: `-R` を外して開く

# 注意

- pane_id はセッション内で使い回すため、開いたら覚えておく
- 既にこのスキルで開いた pane が残っている場合は、新規分割せずその pane で
  `herdr pane run <pane_id> "nvim -R <新ファイル>"` を実行して差し替える
  （nvim が起動中なら先に `herdr pane send-keys <pane_id> Escape` →
  `send-text` で `:q` するか、`pane close` してから分割し直す）
