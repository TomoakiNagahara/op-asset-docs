# CI から見た git push の仕様

## 概要

ONEPIECE Framework では、`git push` は `pre-push` hook と、そこで解決された CI script によって制御されます。

CI 側から見ると、`push` を許可するためには、その branch の現在の commit が CI を通過したことを示す情報を残しておく必要があります。

## 目的

この仕組みの目的は、CI を通過していない commit を `push` させないことです。

そのため、CI は単にテストを実行するだけでなく、「どの commit が通過したか」を `git push` 側が確認できる形で残す必要があります。

## CI が担う役割

CI は、branch と PHP バージョンに対応した commit ID の記録を提供します。

その記録ファイルの形式は次の通りです。

```text
.ci_commit_id_<branch>_php<version>
```

例:

```text
.ci_commit_id_2030_php83
.ci_commit_id_2030_php84
```

このファイルには、CI を通過した commit ID を保存します。

## `git push` 側での利用方法

開発者が `git push` を実行すると、解決された CI script は次の手順で CI 結果を確認します。

1. 現在の branch 名を取得する
2. 対象の PHP バージョンを決定する
3. 対応する `.ci_commit_id_<branch>_php<version>` を特定する
4. そのファイルから commit ID を取得する
5. 現在の branch の commit ID を取得する
6. その 2 つを比較する

ファイルが存在しない場合は `push` を拒否します。

commit ID が一致しない場合も `push` を拒否します。

## この仕様が意味すること

CI 側の観点では、`push` の許可条件は次のように表現できます。

- ある commit に対して CI が成功する
- その commit ID が正しい CI 結果ファイルに保存される
- 開発者が `push` する時点で、現在の branch の先頭 commit がその commit ID と一致している

もし CI 通過後に新しい commit が追加された場合、その branch の先頭 commit は変わります。

この場合、以前の CI 結果はそのままでは使えず、新しい commit に対して再度 CI を通す必要があります。

## branch と PHP バージョンの分離

この CI 結果ファイルは branch ごと、PHP バージョンごとに分かれています。

そのため、次のような誤用を防ぎます。

- 別 branch の CI 結果を流用すること
- 別 PHP バージョンの CI 結果を流用すること

## `local` remote の例外の背景

`local` remote で解決された CI script が enforcement をスキップするのは、public 共有のための CI policy を弱めるためではなく、軽量な private workflow を確保するためです。

現在の repository 状態では `ci.sh` が存在しないため、この script は `.ci.sh` です。

- local push を高速にする
- offline でも履歴保存を可能にする
- WIP や test commit を後で整理する前提で一時保存できるようにする

## まとめ

CI 側から見たこの仕様の本質は、次の 1 点です。

CI は「成功した」という事実だけでなく、「どの commit が成功したか」を `git push` が照合できる形で残さなければならない。

これにより、ONEPIECE Framework では CI の状態と `git push` の許可が直接結び付けられます。
