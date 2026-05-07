# Hooks と統合された CI/CD

## 概要

ONEPIECE Framework の大きな特徴のひとつは、CI/CD が framework の workflow に統合されていることです。

Git hook は optional な飾りではなく、framework の運用モデルの一部として扱われます。

ただし、これは CI/CD が framework にとって必須の強制機能であり、この実装しか使ってはいけない、という意味ではありません。

開発者やエンドユーザーは、この統合 workflow を任意で利用できますが、この実装だけに縛られるわけではありません。

## 関連 framework 文書

- `../op/invariants.ja.md`
- `../op/responsibility-boundaries.ja.md`
- `../op/common-recipes.ja.md`

主目的は単純です。

- 必要な CI check を通過していない code は `push` できないようにする
- repository ルールは remote 側で CI job が始まってからではなく、`push` 前に強制する

そのため、`asset/init/submodules.php` は main repository と submodule の両方に `core.hooksPath` を設定します。

## なぜ hook を強制するのか

framework は、CI/CD check を developer workflow に近づけるために hook を使います。

目的は次です。

- 未検証 code の push を防ぐ
- 関連 repository 間の状態を揃える
- main repository と submodule に同じ workflow を適用する
- remote 側で遅れて失敗するより、developer machine 上で早く失敗させる

つまり、hook 設定は偶然ではなく、framework design の一部です。

## hook と CI の関係

`pre-push` hook は、push を継続させる前に CI entry script を実行します。

典型的な挙動は次です。

1. `git push` が始まる
2. 設定済みの `pre-push` hook が実行される
3. hook が `.ci.sh` または `ci.sh` を読み込む
4. current branch と commit が CI result marker と照合される
5. 期待される CI 状態を満たしていなければ push はブロックされる

これは、framework が remote CI だけに依存せず、push 時点で local にも CI の期待状態を強制していることを意味します。

## `asset/config/init.php` について

`asset/config/init.php` は developer-owned configuration です。

これは、全環境で同一であるべき framework constant ではありません。
developer が local 初期化方針を定義する場所です。例えば次です。

- GitHub account 名
- local repository remote を使うか
- SSH remote を使うか
- repository access 用の host 名
- local repository path

そのため、`asset/config/init.php` の値は local operational setting として理解すべきです。

## branch policy

### branch `2030`

branch 名 `2030` は、この repository set の main branch です。

- 標準の working branch として扱われる
- `origin` へ push される前提の branch である
- CI marker file や関連 workflow の前提はこの branch model に基づく

### branch `local`

branch 名 `local` は local-only work のために予約されています。

- `origin` へ push することは意図していない
- CI/CD workflow の中で特別扱いされる
- CD flow の一部は current branch が `local` の場合に処理をスキップする

この branch は、通常の delivery branch として扱わずに、local 開発や local 実験を支えるために存在します。

## `ci.sh` と `.ci.sh` の歴史的背景

最初期の CI entry script 名は次でした。

- `ci.sh`

その後、実運用では次へ寄っていきました。

- `.ci.sh`

理由は、単なる技術的事情だけではありません。

各 directory で `ls` したときに、目に見える `ci.sh` があると、framework 開発者ではなく、開発者の成果物を利用する側のユーザーを混乱させる可能性があると考えたためです。

この経緯により、現行の hook 挙動は次です。

1. まず `ci.sh` を探す
2. `ci.sh` が無ければ `.ci.sh` を探す

これにより、古い repository との互換性を維持しつつ、新しい repository では hidden-dot convention を使えるようにしています。

## [DOC-GAP] 現行実装の副作用

hook が `ci.sh` を先に優先するため、現行実装には次の副作用があります。

- 空の `ci.sh` file を置くと、その file が先に source される
- 実際の CI check は走らない
- CI enforcement をせずに、その後の check へ進めてしまう

これは現行の As-Is 挙動の一部です。

ただし、ここで述べているのは stable な intended specification ではありません。

## [DOC-FUTURE] 将来変更の可能性

現行の互換性優先の挙動は、永続的な contract とは限りません。

空の `ci.sh` による bypass は、現行実装の結果であり、長期的に保証された feature ではありません。

そのため、この挙動は将来的に変更される可能性があります。

## `cd.sh` についての歴史的補足

古い世代では次も存在していました。

- `cd.sh`

これは現行ワークフローでは使われませんが、古い repository には残っている場合があります。

## 運用上の意図

この設計の意図は、Git hook を厳しくすること自体ではありません。

維持したい contract は次です。

- 開発は素早く進められる
- local と関連 repository の状態を揃えやすい
- CI/CD の挙動を日常の操作に組み込む
- unsafe または未検証 code は `origin` に到達する前に止める

だからこそ、ONEPIECE Framework は hook を optional な個人設定ではなく、framework workflow の一部として扱います。

## 任意性と差し替え可能性

統合された CI/CD workflow は、framework の標準 operating model ですが、必須の universal lock-in ではありません。

つまり次が可能です。

- 開発者は built-in の CI/CD workflow を使ってよい
- エンドユーザーも運用に合うなら利用してよい
- third-party 製の CI/CD system を使ってもよい
- 独自開発の CI/CD system を使ってもよい

言い換えると、`op-unit-ci` は標準の統合選択肢ですが、唯一の許容手段ではありません。

## [DOC-FUTURE] 理想的な責務配置

長期的な理想形は、CI 関連の責務を `op-unit-ci` に、CD 関連の責務を `op-unit-cd` に集約することです。

この理想モデルでは次のように分かれます。

- CI 関連の check と orchestration は `op-unit-ci`
- CD 関連の公開制御と push policy は `op-unit-cd`

current 実装には歴史的経緯による散在部分が残っていますが、To-Be の方向は、責務ごとの集中をより明確にすることです。
