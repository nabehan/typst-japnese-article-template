# jlreq-typst

[W3C 日本語組版処理の要件（JLReq）](https://www.w3.org/TR/jlreq/?lang=ja) に準拠した、和文文書向けの [Typst](https://typst.app/) テンプレートです。

pLaTeX の [`jlreq` クラス](https://github.com/abenori/jlreq) が持つ組版思想（版面設計・行送り・字下げ・脚注体裁など）を参考に、Typst の `set`/`show` ルールで再現しています。

- 参考: [JLReq (W3C)](https://www.w3.org/TR/jlreq/?lang=ja)
- 参考: [jlreq for LaTeX (TeX Wiki 解説)](https://tug.org/docs/latex/jlreq/jlreq-ja.html)
- 参考: [jlreq README-ja.md (abenori/jlreq)](https://github.com/abenori/jlreq/blob/master/README-ja.md)

## 特徴

- **版面の自動計算** — 用紙サイズから JLReq の原則（版面は紙面の 75%、余白は外周から等距離）に基づいて余白を自動算出。`margin-*` や `line-length` / `number-of-lines` で明示指定も可能
- **用紙の縦横** — `flipped` で縦向き・横向きを切り替え。A系・B系・JIS B系・US Letter/Legal に対応
- **段落の統一されたリズム** — 段落先頭の全角字下げ（見出し直後含む）、行間と段落間隔を同一の行送りに統一
- **見出しの階層番号** — `heading-numbering` で `1.1.` 形式や番号なしを切り替え
- **数式番号の章連動** — 章が変わるたびに `(1.1)`, `(2.1)` のようにリセット。`@eq:xxx` の相互参照は「式 N.N」の形式で自動整形
- **著者・所属の表示** — 複数著者・複数所属（beamer の `\inst[]` に相当）、著者ごとの脚注（メールアドレス等）に対応
- **段組** — `num-columns` でタイトルはそのまま・本文のみを段組化
- **脚注・引用・コードブロック・箇条書き・字取り（`jidori`）** など和文組版でよく使う要素をひととおりサポート

## 必要環境

- [Typst](https://typst.app/) 0.15 以降
- 日本語フォント（既定は以下。環境になければ `main.typ` 側で変更してください）
  - 明朝体: `Harano Aji Mincho`
  - ゴシック体: `Harano Aji Gothic`
  - 等幅（コードブロック用）: `HackGen`

フォントが見つからない場合、以下のような代替候補に差し替えてください。

| 用途     | macOS                     | Windows   | Linux             |
| -------- | ------------------------- | --------- | ----------------- |
| 明朝     | Hiragino Mincho ProN      | Yu Mincho | Noto Serif CJK JP |
| ゴシック | Hiragino Kaku Gothic ProN | Yu Gothic | Noto Sans CJK JP  |

## ファイル構成

```
.
├── jlreq.typ       # テンプレート本体（jlreq関数とユーティリティ関数）
├── main.typ        # 最小構成のサンプル
├── main-test.typ   # 全機能を一通り試せる使用例つきサンプル
└── README.md
```

## 使い方

1. `jlreq.typ` をプロジェクトのディレクトリに配置します。
2. 本文ファイルの先頭で `jlreq` 関数を import します。

```typst
#import "jlreq.typ": jlreq, jidori

#show: jlreq.with(
  title: "文書のタイトル",
  author: "著者名",
)

= はじめに

本文をここに書きます。
```

3. `typst compile main.typ` または Typst の VS Code 拡張（tinymist）などでコンパイルします。

## `jlreq()` の主なパラメータ

### 書誌情報

| パラメータ          | 既定値                 | 説明                                                                                  |
| ------------------- | ---------------------- | ------------------------------------------------------------------------------------- |
| `title`             | `none`                 | タイトル。`none` ならタイトルページ自体を出力しない                                   |
| `subtitle`          | `none`                 | サブタイトル                                                                          |
| `author`            | `none`                 | 著者名。文字列 or 複数著者の配列                                                      |
| `institute`         | `none`                 | 所属一覧の配列（beamer の `\institute` に相当）                                       |
| `author-institute`  | `none`                 | 各著者の所属番号（`institute` 配列への 1 始まりのインデックス）。複数所属は配列で指定 |
| `institute-symbols` | `("*", "†", "‡", ...)` | 所属番号を表示する記号列。脚注番号と見分けやすくするため既定は記号                    |
| `author-note`       | `none`                 | 著者ごとの脚注（メールアドレス等）。文字列 or 配列                                    |
| `date`              | `none`                 | 日付                                                                                  |

### 用紙・版面

| パラメータ                                                      | 既定値  | 説明                                                                              |
| --------------------------------------------------------------- | ------- | --------------------------------------------------------------------------------- |
| `paper`                                                         | `"a4"`  | 用紙サイズ（`a0`〜`a7`, `b3`〜`b7`, `jis-b4`〜`jis-b6`, `us-letter`, `us-legal`） |
| `flipped`                                                       | `false` | `true` で横向き                                                                   |
| `font-size`                                                     | `10pt`  | 本文和文フォントサイズ（版面計算の基準単位）                                      |
| `leading`                                                       | `auto`  | 行送り。`font-size` への倍率で指定（`auto` は 1.7 倍）                            |
| `line-length`                                                   | `auto`  | 版面の横幅を明示指定する場合に使用                                                |
| `number-of-lines`                                               | `auto`  | 版面の行数を明示指定する場合に使用                                                |
| `margin-top` / `margin-bottom` / `margin-left` / `margin-right` | `auto`  | 余白を個別指定。`auto` の場合は外周から等距離になるよう自動算出                   |

### フォント

| パラメータ  | 既定値                |
| ----------- | --------------------- |
| `ja-serif`  | `"Harano Aji Mincho"` |
| `en-serif`  | `"Nimbus Roman"`      |
| `ja-sans`   | `"Harano Aji Gothic"` |
| `en-sans`   | `"Nimbus Sans"`       |
| `code-font` | `"HackGen"`           |

### 見出し・段組・ページ

| パラメータ          | 既定値   | 説明                                                                |
| ------------------- | -------- | ------------------------------------------------------------------- |
| `heading-numbering` | `"1.1."` | 見出し番号の書式。`none` で番号なし                                 |
| `num-columns`       | `1`      | 本文の段組数。`2` 以上でタイトル部分は 1 段のまま本文だけ段組になる |
| `gap-columns`       | `2em`    | 段間の空き                                                          |
| `page-numbering`    | `"1"`    | ページ番号の書式                                                    |
| `running-head`      | `none`   | 柱（ページ上部の見出し）                                            |

## 提供するユーティリティ関数

### `jidori(width, body)` — 字取り（均等割付）

指定した幅に文字を均等配置します。

```typst
#jidori(9em)[均等割付]
```

## 数式番号・相互参照

数式番号は章番号と連動し、章が変わるとリセットされます。

```typst
$ E = m c^2 $ <eq:einstein>

本文中で @eq:einstein のように参照すると「式 1.1」と表示されます。
```

## 著者・所属の指定例

```typst
#show: jlreq.with(
  author: ("著者氏名１", "著者氏名２", "著者氏名３"),
  institute: (
    "○○大学 △△学部",
    "○○研究所",
    "○○株式会社 リサーチセンター",
  ),
  // 著者１は所属1、著者２は所属2、著者３は所属1と3の両方
  author-institute: (1, 2, (1, 3)),
  author-note: (
    "author1@example.com",
    "author2@example.com",
    "author3@example.com",
  ),
)
```

所属番号は `*`, `†`, `‡` … の記号で著者名の右肩に表示され、脚注の番号（1, 2, 3…）とは見分けやすいようになっています。

## サンプルファイル

- `main.typ` — 最小構成の例
- `main-test.typ` — 版面・見出し・箇条書き・引用・脚注・著者所属・数式・コードブロックなど全機能を一通り確認できるサンプル

```sh
typst compile main-test.typ
```

## ライセンス

このテンプレートは自由にコピー・改変・再配布して構いません。
ベースにした jlreq (LaTeX) は [BSD 2-Clause License](https://github.com/abenori/jlreq/blob/master/LICENSE) の下で公開されています。

## 参考文献

- [日本語組版処理の要件 (日本語版) — W3C](https://www.w3.org/TR/jlreq/?lang=ja)
- [jlreq: 日本語文書用のLaTeXクラス — TeX Wiki](https://tug.org/docs/latex/jlreq/jlreq-ja.html)
- [abenori/jlreq README-ja.md](https://github.com/abenori/jlreq/blob/master/README-ja.md)
- [Typst Documentation](https://typst.app/docs/)
