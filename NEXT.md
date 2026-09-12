# NEXT: ジャンプ履歴ビューア

`:jumps` を popup で絞って飛ぶ。`getjumplist()` を表示するだけで状態は持たない。

## やること

- `:Tjv` で `getjumplist()` のリストを候補にする
- 候補表示: `path:line:col`（相対パス）。実測で entry に `filename` / `text` は入らないため
  `bufname(bufnr)` を使う。行テキストは v1 では出さない
- 並びは最新順（`reverse()`）。現在位置も1件として出す（無害）
- 確定: `buffer` + `cursor(lnum, col + 1)`。`edit` は使わない＝新たなジャンプを積まない
  （実測で行頭ジャンプの `col` は 0。+1 の要否は実機で最終確認）
- キー操作・見た目は tff と同じ

## スコープ外

- ウィンドウ/タブ指定（カレントのみ。`getjumplist()` の引数は将来の余地）
- 履歴の削除・編集

## テスト

- tff の filter 直接駆動方式を流用
- `edit`×2 でジャンプを作る → 行数・先頭行（最新が先頭）・Enter後のカーソル位置を検証

## 共通の約束（tpf/tff と同じ）

- Vim9のみ・依存なし・KISS・状態最小。`plugin/tjv.vim` + `autoload/tjv.vim` の2ファイル
- 骨格は tff のコピーでよい（popup + filter、先頭行=入力欄、`> `マーカー、border:[]、padding:[0,1,0,1]、20件）
- 2本目以降で共通コア切り出しを判断（時期尚早ならコピーのまま）
- 罠: def引数名とスクリプト変数の衝突不可(E1168)／テストは `--cmd "set rtp+=$PWD"`／`writefile(/dev/stderr)` 禁止
