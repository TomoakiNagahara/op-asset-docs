# CI/CD の概念

## 対象範囲

この文書は、ONEPIECE Framework における CI/CD の理想的な概念を記録します。

ここで説明するのは、framework 固有の用語と運用モデルです。世間一般の用語法と異なる場合は、ONEPIECE Framework における用語・概念の境界として扱ってください。

## ONEPIECE の CI/CD モデル

ONEPIECE Framework では、CI と CD は別の概念です。

行動としても分かれています。

- CI は CD なしで実行できる。
- CD は CI とは別に制御できる。
- CI の責務は `op-unit-ci` に属する。
- CD の責務は `op-unit-cd` に属する。

`cicd` command は、application 全体の CI と CD を管理する統合 entry point です。

`OP()->Unit()->CI()->Auto()` や `OP()->Unit()->CD()->Auto()` の内部挙動は実装依存です。

current の CD unit は `git push` で配信するかもしれませんが、別の CD unit であれば `rsync`、`ftp`、その他の方式で配信することも考えられます。同じように、CI unit が変われば、code をどのように検査するかも変わります。

framework level の contract は、固定された内部 algorithm ではなく、入口と出口の境界です。

concept level では次を前提にします。

- `cicd` は設定された CI unit を通して CI に入る。
- `cicd` は設定された CD unit を通して CD に入る。
- CI unit は期待される CI 側 contract を公開する。
- CD unit は期待される CD 側 contract を公開する。
- CI rule の入力と pass/fail の出力は、framework が定める configuration と inspection contract を通して理解できる状態に保つ。

理想的な流れは次です。

1. CI で全ての code を検査する
2. application 全体として安全であることを確認する
3. その全数検査に合格した code だけを配信する

短く言えば次です。

- CI は全数検査によって安全を保証する。
- CD は CI が承認した code だけを配信する。

## flow invariant

CD は、CI がまだ実行中の間に開始してはいけません。

ONEPIECE Framework の CI/CD flow では、CI unit が application 全体の合格結果を `cicd` に返した後でだけ、CD を開始できます。

これは次を意味します。

- 1 つの submodule が合格しただけでは CD を開始できない
- 1 つの submodule の CI marker を保存しただけでは CD を開始できない
- UNIT、MODULE、LAYOUT など、1 つの package type が完了しただけでは CD を開始できない
- full CI run が未完了の状態で CD が始まるのは異常な振る舞いである

順序 rule は次です。

1. CI が開始する
2. CI が application 全体を検査する
3. CI が global success または failure を返す
4. CI が global success を返した場合だけ CD が開始する

実装が step 3 の完了前に配信を開始するなら、その挙動は ONEPIECE CI/CD concept に違反します。

## ONEPIECE における CI

CI は、application に必要な全ての code を検査します。

目的は、code の振る舞いが間違っていないこと、意図せず変わっていないことを確認することです。

その保証の定義は、各 package の `ci/` directory に置かれる CI 定義 file に保存されます。

current の class-oriented CI 実装は、configuration file、method discovery、test execution rule を使ってその保証を定義します。これらの仕組みは active な CI unit の実装詳細ですが、重要な framework boundary は安定しています。

- CI は inspection definition を見つけられること。
- CI は検査対象の振る舞いを識別できること。
- CI は inspection を実行できること。
- CI は `cicd` flow に明確な pass/fail result を返すこと。

CI は、Git managed か non-Git-managed かに関係なく、application が必要とする全ての submodule を検査しなければなりません。対象には次のような framework package が含まれます。

- UNIT
- MODULE
- LAYOUT
- その他、設定された submodule package

特定の submodule だけが CI を通過しても、application 全体の保証にはなりません。

ONEPIECE Framework における CI 合格とは、application 全体の package set が検査に合格した状態を指します。

## ONEPIECE における CD

CD は、CI に合格した code だけを配信します。

CD は、特定の submodule が CI に合格しただけでは開始してはいけません。

正しい CD の境界は application 全体です。

1. 全ての submodule を CI で検査する
2. 必要な CI check が全て合格する
3. その全体合格の後で初めて CD に進む

application 全体が承認された後、CD は各 submodule を個別に配信して構いません。

配信方法は package や運用によって異なります。例えば次です。

- `git push`
- `rsync`
- `ftp`

重要なのは転送方式ではありません。重要なのは、application 全体が CI に合格した後でだけ配信を開始することです。

そのため、CD document は `git push` を CD の定義として扱うべきではありません。`git push` は current の配信方法の 1 つであり、CD concept そのものではありません。

## 世間一般の CI/CD との違い

世間一般の CI/CD では、remote pipeline model として説明されることが多くあります。

- CI は commit や pull request 後の build / test を意味する
- CD は pipeline stage 成功後の自動 deployment を意味する
- pipeline は 1 repository や 1 service 単位で走ることがある

ONEPIECE Framework は、より厳密な application-set model を採用します。

ONEPIECE では次のように扱います。

- CI は syntax check や isolated package test だけではない
- CI は application 全体の振る舞い contract を全数検査すること
- CD は package が 1 つ通過した時点で package ごとにすぐ始めるものではない
- CD は application 全体が通過した後でだけ始まる
- global CI 合格後の配信作業は package ごとに行ってよい

この違いは重要です。ONEPIECE application は、多数の疎結合 package によって構成されます。そのため package 単体の合格だけでは、結合された application 全体が配信して安全であることを証明できません。

## current 実装との関係

[DOC-FUTURE] 意図している model は、`cicd` がこの文書で説明した application 全体の CI/CD flow を制御することです。

current 実装には、歴史的な挙動や責務の散在がまだ残っている可能性があります。

current 挙動がこの文書と異なる場合、この文書を To-Be concept として扱い、実装との差分は責務を持つ As-Is document に記録してください。

主な As-Is 参照先:

- `asset/docs/cicd/usage.ja.md`
- `asset/docs/cicd/hooks.ja.md`
- `asset/unit/ci/docs/ci-spec.ja.md`
- `asset/unit/ci/docs/ci-flow.ja.md`
- `asset/unit/cd/docs/isCanPushToGithub.ja.md`
