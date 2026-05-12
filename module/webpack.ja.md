# WebPack Module

## 概要

この文書は、ONEPIECE Framework における `op-module-webpack` の目的を説明します。

WebPack 全体の仕組みを説明する文書ではありません。

framework-level な WebPack の概要は `../webpack.ja.md` を参照してください。

`op-module-webpack` の current 実装詳細は、`op-module-webpack` package 側の `asset/module/webpack/docs/as-is.ja.md` を参照してください。

## 関連 framework 文書

- `../webpack.ja.md`
- `../op/invariants.ja.md`
- `../op/responsibility-boundaries.ja.md`
- `../op/common-recipes.ja.md`

## 目的

`op-module-webpack` は、grouped asset request を受ける delivery-side module です。

目的は、WebPack request path を受け取り、実際の grouped output 生成を `op-unit-webpack` に委譲することです。

つまり、public な WebPack URL と WebPack unit の間にある request-facing adapter です。

## 責務境界

`op-module-webpack` が担当すべきこと:

- grouped asset の delivery-side request entry
- request から asset type への routing
- output work の `op-unit-webpack` への委譲

`op-module-webpack` が担当すべきではないこと:

- asset registration state
- cache / minify / output internals
- standard JavaScript / CSS asset definitions
- layout-specific asset policy

## 関連 package

`op-module-webpack` は次と連携しますが、それらとは別の package です。

- `op-unit-webpack`
- `op-webpack-js`
- `op-webpack-css`

`op-unit-webpack` は registration state と grouped output behavior を担当します。

`op-webpack-js` と `op-webpack-css` は、framework standard JavaScript / CSS asset を提供します。

## ドキュメント配置

framework-level な WebPack concept は次に置きます。

- `asset/docs/webpack.ja.md`

current の `op-module-webpack` behavior は次に置きます。

- `asset/module/webpack/docs/as-is.ja.md`

この file は、framework documentation tree 側から module の目的と責務境界を説明するためだけに存在します。
