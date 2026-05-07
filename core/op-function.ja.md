# `OP()` の思想的背景

## 概要

`OP()` は、ONEPIECE Framework の実務で最も多用される入口のひとつです。

これは、framework の機能を単一の統一入口から呼び出せるようにするための仕組みです。

## 背景

`OP()` が生まれた背景には、実務上の記述負荷があります。

統一入口がない場合、異なる namespace 間で core 機能や unit 機能を呼び出す記述が煩雑になります。

開発者は毎回、次のことを意識しなければなりません。

- 現在の class namespace
- 呼び出し先 class namespace
- fully-qualified class name
- static / object access の書き分け

## 目的

`OP()` の目的は、framework の利用を単純で統一されたものにすることです。

`OP()` を使うことで、開発者は class namespace を毎回意識せずに framework 機能を呼び出せるようになります。

これにより、次の効果があります。

- どこからでも core 機能を使いやすい
- どこからでも unit 機能を使いやすい
- 記述を短く保ちやすい
- namespace に起因する日常的な煩わしさを減らせる

## 設計上の意味

実務上の意味として、`OP()` は framework の統一 gateway として機能します。

そのため、ONEPIECE Framework における開発体験の中核的な仕組みのひとつになっています。
