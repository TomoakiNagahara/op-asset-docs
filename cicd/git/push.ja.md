# git push の仕様

## 概要

ONEPIECE Framework では、`git push` の実行時に Git hook によって CI script が実行されます。

これは任意の補助機能ではなく、ONEPIECE Framework の CI/CD が Git 運用に融合していることを前提とした仕様です。

## 目的

CI script の目的は、その repository が CI を通過しているかを確認することです。

CI を通過していないコードは `push` できないようにします。

つまり、`git push` の可否は、現在の branch の状態が CI 済みであることを条件に判定されます。

## `git push` 時の動作

`git push` を実行すると、`pre-push` hook が動作します。

その hook は次の流れで処理します。

1. まず `ci.sh` を探し、無ければ `.ci.sh` を探す
2. 解決された CI script を実行する
3. CI スクリプトが異常終了した場合は `push` を中止する
4. CI スクリプトが正常終了した場合のみ `push` を継続する

この仕様により、`push` の直前にローカル側で CI 状態を確認できます。

## 判定方法

現在の repository 状態では `ci.sh` が存在しないため、`.ci.sh` が実際に使われます。

`.ci.sh` は、現在の branch 名と PHP バージョンから、対応する CI 結果 file を決定します。

CI 結果ファイルの形式は次の通りです。

```text
.ci_commit_id_<branch>_php<version>
```

例:

```text
.ci_commit_id_2030_php83
.ci_commit_id_local_php84
```

そのうえで、次の内容を確認します。

1. 対応する CI 結果ファイルが存在するか
2. そのファイルに保存されている commit ID
3. 現在の branch が指している commit ID
4. その 2 つが一致しているか

CI 結果ファイルが存在しない場合は、`push` できません。

保存されている commit ID と現在の commit ID が一致しない場合も、`push` できません。

## 開発者から見た意味

開発者の視点では、この仕様は次の意味を持ちます。

- CI を通過していない commit は `push` できない
- CI 通過後に commit が進んだ場合、そのままでは `push` できない
- `push` するには、現在の commit が CI 通過済みである必要がある

この仕様により、未検証の変更が `origin` に送られることを防ぎます。

また、`local` remote が CI チェックをスキップする背景には次の実務上の理由があります。

- local push は GitHub push より速い
- offline でも履歴を保存できる
- WIP や一時的な test code などの trial commit を local repository に逃がしておける

要するに、開発者の視点では次のように理解できます。

- push すると `pre-push` hook が走る
- hook は解決された CI script を実行する
- 現在の repository 状態では、その script は `.ci.sh` である
- その CI script は現在の branch の commit が CI 通過済みの commit と一致するか確認する

## 補足

ONEPIECE Framework において hook は単なる個人設定ではありません。

hook は、CI/CD を日常の Git 操作に組み込むための仕組みとして扱われます。
