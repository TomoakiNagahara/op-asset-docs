# HTML Pass-Through

## 概要

**HTML Pass-Through** という用語は、歴史的経緯のために現在も ONEPIECE Framework で使われています。

この名称が維持されているのは、元々の出発点が、controller を使わずに HTML ファイルを framework 経由で出力する仕組みだったためです。

その後、同じ考え方は次のような他の resource にも転用・拡張されました。

- JavaScript
- CSS
- SVG
- その他の text 指向 resource

## 技術的な意味

技術的には、考え方は次の通りです。

- request が resource file そのものに解決されることがある
- その file であっても framework フローの中で扱える
- App unit が endpoint を実行する
- その後で layout を適用するかどうかを framework が判断する

これは NEW WORLD の実行モデルと深く関係しています。

## 設計上の背景

元々の設計目的は、従来の controller 層を必須にせず、HTML ファイルの中で PHP スクリプトを実行できるようにすることでした。

これにより、controller-less な HTML エントリーモデルが成立しました。

その後、同じ仕組みは他の resource に対しても有効であることが分かりました。

その結果、HTML 以外にも次のような framework 機能を適用できるようになりました。

- 動的レスポンス生成
- endpoint 側の layout 制御
- body 出力前の header / cookie 制御
- resource ごとの描画挙動の切り替え

もうひとつ重要な背景は、URL routing です。

多くの従来型 framework は、URL segment を次に結び付けていました。

- class 名
- method 名

これは実装しやすい一方で、運用は重くなりがちでした。

単純な page であっても、しばしば次が必要でした。

- 空 class
- 空 method

さらに、深いネスト構造も不自然になりやすくなります。

HTML Pass-Through は、それを変えました。

file 指向の endpoint を framework flow の中で直接実行できるため、

- 単純な page に空の controller 的儀式が不要になる
- `index.php` が controller 的 endpoint になれる
- directory の深いネストも自然に扱える

このため、HTML Pass-Through は単なる convenience feature ではありません。

現在の framework の理解では、これは NEW WORLD を構成する 3 つの本質的要素のひとつです。

## 歴史的経緯

歴史的な流れは次の通りです。

1. 最初の対象は HTML だった
2. framework はその仕組みを使って controller-less な HTML 出力を実現した
3. その後、同じ仕組みが JS、CSS、SVG などにも転用された
4. scope は広がったが、名称として `HTML Pass-Through` が残った

この経緯のため、現在の名称は歴史的には正しい一方で、技術的な現状よりは狭い表現になっています。

## [DOC-GAP] 現状の実装との齟齬

歴史的名称、現在の思想、現在の実装の間にはいくつか齟齬があります。

### 1. 名称が現在の scope より狭い

名称は `HTML` ですが、実際の思想は HTML だけに留まりません。

### 2. Router の拡張子一覧が名称より広い

現在の Router の pass-through 対象拡張子には次が含まれています。

- `html`
- `css`
- `js`
- `txt`
- `png`
- `ico`

これはすでに `HTML` という名称より広い範囲です。

### [DOC-GAP] 2b. 拡張子一覧がハードコードされている

現在の pass-through 対象拡張子一覧は、Router 実装の中にハードコードされています。

これは、framework のより大きな思想にはあまり適合していません。

pass-through 対象の集合が framework policy の一部であるなら、本来は source code 固定ではなく config に分離されるべきです。

### [DOC-FUTURE] 2c. Pass-Through 対象集合は config に移すべき

長期的な方向性は次です。

- 現行 As-Is 挙動はそのまま文書化する
- pass-through 拡張子制御は config に移す
- 実装を framework のより大きな思想に揃える

### 3. Apache routing と Router の scope が一致していない

現在の Apache `.htaccess` では、

- 既存の `html`, `php`, `js`, `css`, `txt` は `app.php` に渡る
- 存在しない file も `app.php` に渡る
- 既存の画像 file は通常は静的配信される

そのため、Router の実装と、現在の Apache routing の有効範囲は完全には一致していません。

### 4. 名称が resource 指向の現在の意図を十分には表していない

framework はすでに HTML 以上のものに同じ概念を適用していますが、歴史的名称はまだそれを十分には表現していません。

## 思想

この用語上の齟齬があっても、根底にある思想は一貫しています。

- resource を framework フローに参加させられるようにする
- endpoint 実行と最終描画を分離する
- layout を動的に制御可能にする
- value がある場面では framework 制御を適用できるようにする

この広い意味での HTML Pass-Through は、単に HTML file の扱い方ではありません。

これは NEW WORLD の **グランドライン** の一部でもあります。

- 不要な controller 的儀式を減らす
- routing を file 指向に保つ
- ネストを自然にする
- 単純な page と richer な application endpoint を共存させる

## [DOC-FUTURE] 将来方針

現在の

- 歴史的な名称
- 技術的な scope
- 現在の実装詳細

の間にある齟齬は、将来的に改善すべきものとして認識されています。

当面の方針としては、歴史的名称である `HTML Pass-Through` を使い続けつつ、実際の意味や実装上の差異を文書で明確にしていきます。

長期的には、この齟齬をより自然な形で解消していく方針です。
