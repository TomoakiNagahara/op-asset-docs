# WebPack

## 概要

この文書は、ONEPIECE Framework における WebPack の位置づけを説明します。

ONEPIECE Framework における WebPack とは、拡張子ごとに複数の file をひとまとめにして、各拡張子の group を 1 回の request でまとめて送信できるようにするために ONEPIECE Framework が独自に作成した system です。

たとえば、複数の CSS file は 1 つの CSS request にまとめられ、複数の JavaScript file は 1 つの JavaScript request にまとめられます。

これは Node.js 製の webpack ではなく、Node.js の build tool である `webpack` と混同してはいけません。

ONEPIECE Framework の文書で `WebPack` と書く場合、明示的に別の説明がない限り、この framework-native な grouping / delivery system を指します。

ただし、単に directory にある file を自動的に集める機能ではありません。

どの asset を pack するかは、`OP()->Unit()->WebPack()->Auto()` による明示登録を基本とします。

## Packed Content Hash

WebPack は拡張子ごとに content を packing します。各 extension group は、その拡張子に登録された file から構築されます。

WebPack は、拡張子ごとに packing した extension group ごとの hash value を生成します。

ONEPIECE Framework の概念は、hash を何から生成するかを強制しません。WebPack unit は、packed binary output、content value、registered file list、または unit が所有する別の cache identity から hash を導出できます。

hash source を選び実装する責任は `op-unit-webpack` が負います。

生成された hash は grouped asset URL に含められます。delivery request にその hash が含まれる場合、WebPack は対応する extension group の cached packed content を出力できます。

この hash は debug 時にも役立ちます。page がどの packed content version を request しているか、browser、server-side cache、WebPack unit state のどれかが古い packed result を参照していないかを確認する手がかりになります。

## File Authoring Rules

WebPack は同じ拡張子の複数 file を 1 つの request にまとめるため、frontend file は framework 共通の asset authoring rule に従う必要があります。

JavaScript または CSS を追加・変更する前に `op/frontend-asset-authoring.ja.md` を参照してください。

## NewWorld Rendering Flow

WebPack の登録タイミングは、ONEPIECE Framework の NewWorld rendering flow と合わせて理解する必要があります。

通常の HTML 出力では、application endpoint は layout が描画される前に実行されます。

1. Router unit が endpoint を解決する
2. App unit が Router unit から、Router unit が解決した endpoint を取得する
3. App unit が endpoint を `OP()->Template()` で実行する
4. endpoint の出力を App unit の buffer に保持する
5. Layout unit を実行する
6. layout が `layout/head.phtml` などの shared template を呼ぶ
7. layout が `OP()->Content()` で buffer 済み endpoint content を出力する

つまり、endpoint content の中で行われた WebPack 登録は、`layout/head.phtml` が WebPack の `<link>` や `<script>` tag を出力する前に完了しています。

この flow を確認せずに、endpoint-owned な WebPack 登録を「head への出力に間に合っていない」と判断してはいけません。

WebPack の hash や grouped asset URL が期待通りでない場合は、次を確認します。

- endpoint 実行中にどの file が登録されたか
- layout rendering 中に active WebPack unit が hash をどのように生成したか
- 別 request である `/webpack/css/...` や `/webpack/js/...` の delivery request が、登録済み asset list をどのように再構築または参照しているか
- delivery request が意図した layout-owned / app-owned asset directory を登録しているか

grouped asset delivery request は、HTML page request とは別の request です。どちらも WebPack unit によって連携しますが、delivery request の責務は endpoint rendering とは同じではありません。

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
