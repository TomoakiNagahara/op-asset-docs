# WebPack

## 概要

ONEPIECE Framework の特徴のひとつに、複数の JavaScript file や複数の CSS file を、1 回の request で送信できることがあります。

これは次を組み合わせて実現されます。

- `op-unit-webpack`
- `webpack` module

## 関連 framework 文書

- `../op/invariants.ja.md`
- `../op/responsibility-boundaries.ja.md`
- `../op/common-recipes.ja.md`

## 基本的な考え方

目的は単なる file collection ではありません。

framework は、次の仕組みを提供します。

- 複数の `js` file をまとめて送信できる
- 複数の `css` file をまとめて送信できる
- response を framework logic によって動的に組み立てられる

## 役割分担

大きく分けると次です。

- `op-unit-webpack` は registration、selection、output preparation を担当する
- `webpack` module は、まとめられた asset を送信する delivery-side module として働く

## これが重要な理由

これにより、front-end asset の送信を、単なる静的 file 群ではなく framework workflow の一部として扱えます。

その結果、framework は次を協調させられます。

- layout に関連する asset grouping
- 動的な output behavior
- request 駆動の統一された delivery
