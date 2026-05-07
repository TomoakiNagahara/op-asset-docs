# For TomoakiNagahara

## なぜこの3文書が必要なのか

次の3文書は、ONEPIECE Framework を長期的に AI が使いやすくするうえで特に重要です。

- `Invariants`
- `Responsibility Boundaries`
- `Common Recipes`

なぜ必要かというと、既存の文書群は個別仕様の説明はかなり進んでいる一方で、AI が判断するための一段上の構造も必要だからです。

## 関連文書

- `invariants.md`
- `responsibility-boundaries.md`
- `common-recipes.md`

## 1. なぜ `Invariants` が必要か

AI は、framework が今どう動くかを知るだけでは足りません。

何を不用意に変えてはいけないかも知る必要があります。

不変条件が明文化されていないと、AI は局所的な挙動を正しく理解していても、framework の根本契約を壊す変更を提案してしまうことがあります。

`Invariants` は AI に次の境界線を与えます。

- 安全な変更
- 危険な変更
- framework-level の contract breakage

## 2. なぜ `Responsibility Boundaries` が必要か

framework の変更ミスは、1つの関数の誤読で起きるとは限りません。

むしろ、間違った層を編集してしまうことによって起きることが多いです。

例えば次です。

- 問題が Layout にあるのに Router を変えてしまう
- 問題が Unit にあるのに Core を変えてしまう
- 出力の責務と設定の責務を混ぜてしまう

`Responsibility Boundaries` は AI に次を判断させやすくします。

- その変更はどこに属するか
- どの subsystem がその挙動を所有しているか
- どの layer は触るべきでないか

## 3. なぜ `Common Recipes` が必要か

AI が受ける依頼は、設計論よりも task 指向であることが多いです。

例えば次です。

- ページを追加したい
- local 専用 config を足したい
- CI 対象に repository を追加したい
- unit を差し替えたい
- push failure を調べたい

このような依頼に対しては、AI には手順パターンが必要です。

`Common Recipes` は、framework knowledge と実装作業を接続する実務上の response path を AI に与えます。

## なぜこの3つをセットで持つべきか

この3文書は、セットで使うと最も強くなります。

- `Invariants` は、何を安定させるべきかを示す
- `Responsibility Boundaries` は、どこを触るべきかを示す
- `Common Recipes` は、どう変更を進めるかを示す

この3つが揃うことで、文書は受動的な知識保管ではなく、AI のための能動的な判断フレームワークになります。

## 長期目標との関係

長期目標が「人間が framework 全体を細部まで学習しなくても使えるようにすること」なら、この3文書は基礎になります。

これにより AI は、単に

- framework が何をするか

だけでなく、

- 何を変えるべきか
- どこを変えるべきか
- 何を守るべきか
- どう安全に変更するか

まで答えやすくなります。

だからこの3文書は、任意の補足ではありません。

AI 主導で framework を使っていくための構造文書です。
