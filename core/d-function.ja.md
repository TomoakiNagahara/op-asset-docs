# `D()` デバッグ出力

## 概要

ONEPIECE Framework において、`D()` は日常的な開発で非常によく使われるデバッグ機能のひとつです。

目的は変数をデバッグ用に表示することですが、単なる `var_dump()` ではありません。

## 主な特徴

現在の設計には、いくつか重要な特徴があります。

### 1. file path と line number が含まれる

`D()` を使うと、出力には呼び出し元の file path と line number も含まれます。

そのため、どこから debug 出力されたかを簡単に特定できます。

### 2. `var_dump()` より見やすいことを重視している

出力は native の `var_dump()` よりもかなり見やすくなるよう設計されています。

特に、Dump unit が HTML 向けの構造化表示を行います。

### 3. 配列の見やすさを重視している

もっとも大きな実務上の違いのひとつが、配列の見やすさです。

配列出力は plain な `var_dump()` よりもかなり追いやすい形に整形されます。

### 4. 出力は administrator に限定される

これは多くの framework との差として重要な点です。

`D()` の出力は、requester が administrator と判断された場合にだけ表示されます。

この判定は `OP()->isAdmin()` に基づきます。

## 技術的な責務分担

`D()` 関数自体は、debug の入口にすぎません。

administrator 判定をしたあと、実際の整形や描画は `op-unit-dump` に委譲します。

つまり、

- `D()` は debug entry function
- `op-unit-dump` は formatting と rendering の担当

という分担です。

## まとめ

`D()` は、ONEPIECE Framework における標準的な debug 出力関数です。

重要なのは、単に変数を dump するだけでなく、次を組み合わせていることです。

- administrator 限定表示
- 呼び出し元位置の表示
- 見やすい整形
- 配列の見やすい出力
