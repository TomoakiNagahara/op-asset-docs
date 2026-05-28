# Design Philosophy

## 概要

ONEPIECE Framework は、hidden で過度に magical な挙動よりも、明示的な挙動を好みます。

`ONEPIECE` という名称自体にも、framework の重要な意図が含まれています。

由来は、すべての機能を一つに繋ぐ、という考え方です。

これは単なる coding style の好みではありません。

framework 全体の思想に直結しています。

- simple
- intuitive
- concise
- easy to understand

## なぜ明示性が重要なのか

framework は、挙動が visible で direct であるほど、保守しやすいと考えます。

実務上、明示的な設計は次を理解しやすくします。

- 何が起きているか
- どこで判断しているか
- どの layer が責任を持つか
- 何を変えるべきで、何を守るべきか

これは人間にも AI にも重要です。

## 設計上の好み

framework は一般に次を好みます。

- hidden convention より explicit contract
- implicit side effect より visible participation
- heavy indirection より direct structure
- clever だが opaque な shortcut より理解しやすい operational behavior

また、システム設計の自由さと柔軟性にも強い価値を置いています。

実務上は、あるサブシステムが必要な contract を満たしているなら、そのサブシステムを差し替え可能なままにしておくことを好み、単一の正規実装だけを永続的に強制することを好みません。

この好みは framework の多くの部分に現れています。

例:

- 明確な entry point としての `app.php`
- endpoint 実行と最終描画を分離する NEW WORLD
- 明示的な CI participation contract としての `OP_CI`
- interface ベース contract による unit replacement
- 予測可能な読込順を持つ local override config
- repository URL や mapping による差し替え可能な repository / subsystem

## エラーハンドリングに関する好み

framework は、application level の制御フローとして `throw` に頼るよりも、失敗を丁寧に呼び出し元へ返す設計を好みます。

例外を全面的に `throw` した方が、実装自体は速く書けることがあります。

しかし framework の設計上の好みは、可能な限り、呼び出し元が次に何をすべきか判断しやすい形で、失敗を丁寧に伝えることです。

これは実装上かなり大変です。

framework は PHP runtime level では例外の捕捉をサポートしています。

ただし application 設計の好みとしては、`throw first` を理想的な default とは考えていません。

理由は実務的です。

丁寧なエラー処理の方が、コードを使う側にとって助けになることが多いからです。

この好みは抽象的な理論だけではなく、苦い経験にも根ざしています。

## メモリー使用量に関する好み

framework は、memory usage を低く抑えることを、実装後の optimization task ではなく development guideline として扱います。

通常の成功 request path では、rare condition でだけ必要になる code、data、helper を読み込まないようにします。

例:

- recovery guidance
- diagnostics
- maintenance helper
- 大きな optional helper class
- error-only processing
- environment investigation logic

そのような logic が必要な場合は、focused file または class に分離し、それが本当に必要になった条件が発生した後にだけ lazy-load します。

これにより、common path を小さく保ち、小さな server、shared hosting、long-running process、high-traffic application でも practical な framework にできます。

目標は、memory saving のために code を分かりにくくすることではありません。

目標は、現在の request が本当に必要とする処理に比例した memory use に保つことです。

## 意味

この思想は、framework が大きくなっても practical でいられる理由のひとつです。

目標は、すべての abstraction をなくすことではありません。

目標は、abstraction を理解可能なまま保つことです。

そのため、ONEPIECE Framework では、明示的な処理は設計上の肯定的価値として扱われます。

これは同時に、純正実装だけを使わなければならない、という前提を置かない理由でもあります。

たとえば `op-core` であっても、常に純正実装でなければならないわけではありません。必要な役割と contract を満たしていれば、third-party 製の実装や fork した独自実装を利用しても構いません。
