# Important Gaps

## 概要

この文書は、重要な documented gap を人間が一覧しやすいようにまとめた index です。

目的は、優先度の高い齟齬や実務上重要な齟齬が、多数の文書に散らばったままにならないようにすることです。

この file は、各所にある `[DOC-GAP]`, `[DOC-FUTURE]`, `[DOC-PRIORITY1]` を置き換えるものではありません。

人間向けの curated summary です。

## 最優先課題

### [DOC-PRIORITY1] `OP()->Template()` の読込優先順位

current status:

- 意図された仕様順は次です。
  1. current directory
  2. `asset/layout/<layout-name>/template/`
  3. `asset/template/`
  4. unit template directory
- current の `OP()->Template()` As-Is は `OP_TEMPLATE` trait の documentation が所有する
- current As-Is の lookup order と current-directory behavior は `asset/core/trait/docs/op-template.ja.md` を参照する

なぜ重要か:

- 同名 file が複数箇所にあるとき、どの template が勝つかが変わる
- shared site template の基本運用は、この仕様順を前提にしている
- `OP()->Template()` の current-directory behavior は implementation-dependent なので、trait docs 以外では重複して書かず、trait As-Is を参照する

主な参照先:

- `asset/docs/skeleton/template-directory.md`
- `asset/docs/skeleton/template-directory.ja.md`
- `asset/core/trait/docs/op-template.md`
- `asset/core/trait/docs/op-template.ja.md`
- `asset/core/trait/OP_TEMPLATE.php`

## 重要な current gap

### `OP()->URL('.')` が FQDN を含む完全 URL を返す

current status:

- `OP()->URL('.')` は現在 `scheme://host/request_uri` を返す

なぜ重要か:

- framework の思想では、URL abstraction level で FQDN を含めたくない

将来方針:

- 後で修正予定

主な参照先:

- `asset/core/docs/op-url.md`
- `asset/core/docs/op-url.ja.md`

### Pass-Through 拡張子制御がハードコードされている

current status:

- pass-through 対象拡張子は current router 実装にハードコードされている

なぜ重要か:

- その種の policy は本来 config にあるべきという framework のより大きな思想とずれる

将来方針:

- pass-through 拡張子制御を config に移す

主な参照先:

- `asset/docs/new-world/html-pass-through.md`
- `asset/docs/new-world/html-pass-through.ja.md`
- `asset/unit/router/docs/calc-route-2018.md`
- `asset/unit/router/docs/calc-route-2018.ja.md`

### 空の `ci.sh` で CI gate を bypass できる

current status:

- current hook 解決順は `.ci.sh` より `ci.sh` を優先する
- そのため空の `ci.sh` があると、real inspection 無しで CI gate を通せる

なぜ重要か:

- これは意図された安定 contract ではなく、実装上の副作用である

将来方針:

- 安定仕様ではなく、changeable な As-Is 挙動として扱う

主な参照先:

- `asset/unit/ci/docs/ci-spec.md`
- `asset/unit/ci/docs/ci-spec.ja.md`
- `asset/unit/ci/docs/ci-flow.md`
- `asset/unit/ci/docs/ci-flow.ja.md`
- `asset/docs/cicd/hooks.md`
- `asset/docs/cicd/hooks.ja.md`

### WebPack module / unit が layout asset directory を自動登録している

current status:

- `asset/module/webpack/content/js/index.php` と `asset/module/webpack/content/css/index.php` は、`asset:/layout/<layout>/<extension>/` を `WebPack()->Auto()` に登録している
- `op-unit-webpack` 側にも、request の `layout` を見て layout asset directory を登録する経路がある

なぜ重要か:

- layout にある `js` / `css` directory は、自動的に pack されるべきではない
- ONEPIECE Framework の思想では、各 layout が自分の asset を主体的に登録するべきである
- pack する unit や中間 module が layout asset を自動登録すると、責務境界が曖昧になり、意図しない asset inclusion や debugging difficulty の原因になる

あるべき姿:

- layout-specific asset の WebPack 登録は、各 layout が自分の初期化処理や template で明示的に行う
- WebPack unit は登録済み asset の state / output / cache / minify に集中する
- WebPack module は delivery-side request entry に集中し、layout asset policy を持たない

主な参照先:

- `asset/docs/module/webpack.md`
- `asset/docs/module/webpack.ja.md`
- `asset/module/webpack/docs/as-is.md`
- `asset/module/webpack/docs/as-is.ja.md`
- `asset/module/webpack/content/js/index.php`
- `asset/module/webpack/content/css/index.php`
- `asset/unit/webpack/WEBPACK_2024.trait.php`

## 優先度が少し低い将来調整

### `isAdmin()` の localhost 自動 admin は current では code 側固定

current status:

- current 挙動では localhost は自動的に admin 扱い

将来方針:

- 将来的に設定で切り替え可能にする

主な参照先:

- `asset/docs/core/is-admin.md`
- `asset/docs/core/is-admin.ja.md`
- `asset/core/docs/is-admin.md`
- `asset/core/docs/is-admin.ja.md`

## 維持ルール

新しい gap が、人間が多数の文書を読まなくても素早く見つけるべき重要度を持つなら、詳細文書に local tag を残すだけでなく、この file にも追加して下さい。
