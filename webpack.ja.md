# WebPack

## 概要

この文書は、ONEPIECE Framework における WebPack の位置づけを説明します。

WebPack は、JavaScript / CSS を必要に応じてまとめて送信するための framework-level な仕組みです。

ただし、単に directory にある file を自動的に集める機能ではありません。

どの asset を pack するかは、`OP()->Unit()->WebPack()->Auto()` による明示登録を基本とします。

## 構成要素

WebPack に関係する主な package は次です。

- `op-unit-webpack`
- `op-module-webpack`
- `op-webpack-js`
- `op-webpack-css`

## `op-unit-webpack`

`op-unit-webpack` は、WebPack の unit 側です。

主な責務は次です。

- asset registration state を管理する
- 登録済み asset を grouped output として出力する
- cache / minify / output behavior を扱う

`op-unit-webpack` は、どの layout asset を使うべきかという policy を自動的に決めるべきではありません。

### File Registration

WebPack に file を登録するには `OP()->Unit()->WebPack()->Auto()` を使います。

`Auto()` には、次の形式で対象を渡せます。

- 複数 file の一括指定
- directory 指定
- wildcard 指定

### Path Rules

WebPack に渡す file/directory の指定には、次の rule があります。

- meta path が使える
- full path は指定できない
- upper directory は指定できない
- current path 指定が使える

full path と upper directory の禁止は、意図しない server-side file への access を防ぐための security measure です。

## `op-module-webpack`

`op-module-webpack` は、grouped asset request を受ける delivery-side module です。

主な責務は、request entry と output delivery です。

`op-module-webpack` は、WebPack 全体のすべてを説明するものではありません。

また、layout-specific asset policy を持つべきではありません。

## `op-webpack-js` / `op-webpack-css`

`op-webpack-js` と `op-webpack-css` は、framework が default で用意する standard JavaScript / CSS asset の package です。

これらは `asset:/webpack/js/` と `asset:/webpack/css/` として参照されます。

これらの file は、自動的には pack されません。

application や layout は、これらを参考にしてもよいし、使ってもよいし、無視しても構いません。

推奨する使い方は、必要な standard asset への symbolic link を layout 側の `js` directory または `css` directory に作る方法です。

そのうえで、各 layout が自分の asset として `OP()->Unit()->WebPack()->Auto()` に明示登録します。

この運用により、どの standard asset を使うかを layout 側で明確にできます。

## Layout との関係

ONEPIECE Framework の思想では、layout-specific asset の選択と登録は各 layout が主体的に行います。

`asset/layout/<layout-name>/js/` や `asset/layout/<layout-name>/css/` が存在するだけで、自動的に pack されるべきではありません。

layout は、自分が必要とする asset を明示的に WebPack に登録します。

## 関連文書

- `module/webpack.ja.md`
- `op/responsibility-boundaries.ja.md`
- `important-gaps.ja.md`
- `to-be.ja.md`
