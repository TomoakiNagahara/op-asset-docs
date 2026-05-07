# OP()->URL()

## 概要

`OP()->URL()` は、meta path または local full path を、document root 基準の URL path に変換するための ONEPIECE Framework の entry point です。

これは path 処理系に対する URL 側の counterpart です。

`OP()->Path()` が path 変換の unified entry point だとすれば、`OP()->URL()` は URL 変換の unified entry point です。

## 主な役割

`OP()->URL()` があることで、呼び出し側は次を手計算する必要がありません。

- application が document root 配下のどこに設置されているか
- meta path を web URL としてどう表現すべきか
- directory に末尾 slash を付けるべきか

これは特に `app:/` と相性がよいです。

application が document root 配下のどこに展開されていても、`OP()->URL('app:/...')` は正しい URL path を生成できます。

## 典型的な使い方

例:

- `OP()->URL('app:/foo/bar/')`
- `OP()->URL('/actual/full/path/to/app/foo/bar/')`
- `OP()->URL('.')`

current の挙動では次のようになります。

- `app:/...` は document root 相対の URL に変換される
- application root 配下の full path も document root 相対の URL に変換される
- `.` は current request の完全 URL を返す

## 重要な制約

`OP()->URL()` は、あらゆる meta path を汎用的に URL 化する converter ではありません。

これは、document root 配下で web-visible な URL として表現できる path を対象にした仕組みです。

そのため current 実装では、`asset:/...` のような path は通常の public URL target としては扱いません。

## Meta Path との関係

`OP()->URL()` は meta path system と密接に関係していますが、目的は別です。

- `OP()->Path()`
  path 変換
- `OP()->URL()`
  URL 変換

両者を合わせることで、framework は local path と web URL を統一的な概念モデルで扱えます。
