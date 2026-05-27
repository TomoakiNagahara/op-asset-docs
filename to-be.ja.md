# To-Be

## 概要

この文書は、将来方針を人間と AI が一覧しやすいように集約するための文書です。

これは各文書にある `[DOC-FUTURE]` note を置き換えるものではありません。

むしろ、重要な To-Be を 1 か所に集めることで、将来設計の方向が多数の technical document に散らばったままにならないようにするためのものです。

## この file が必要な理由

As-Is と To-Be は区別できるべきです。

framework 文書では、実務上次のように分けます。

- 各 technical document
  current の As-Is を主に説明する
- 各文書
  必要なら短い `[DOC-FUTURE]` note を持ってよい
- この file
  より大きい、または複数領域にまたがる To-Be を集約する

これにより、人間にも AI にも次の混同を避けやすくなります。

- current の挙動
- current の bug や gap
- 将来の目標
- 意図している再設計の方向

## current の To-Be 方針

### CI と CD の責務集約

長期的な理想構造は次です。

- CI 関連の責務は `op-unit-ci` に集約する
- CD 関連の責務は `op-unit-cd` に集約する

current 実装には歴史的経緯による散在が残っていますが、望ましい方向はこの集中です。

より広い CI/CD concept としては、`cicd` が application 全体を管理し、CI が必要な全 submodule を全数検査し、CD は application 全体が CI に合格した後でだけ開始する、という形を理想とします。

主な参照先:

- `asset/docs/cicd/concept.ja.md`
- `asset/docs/cicd/hooks.md`
- `asset/unit/ci/docs/ci-spec.md`
- `asset/unit/cd/docs/isCanPushToGithub.md`

### prefix ベースの push block は `op-unit-cd` に寄せるべき

current 実装では、prefix による push block は hook level の file 群に散在しています。

望ましい将来設計は、その push-policy 責務を `op-unit-cd` に集約することです。

主な参照先:

- `asset/unit/cd/docs/isCanPushToGithub.md`

### Pass-Through 拡張子制御は config へ移すべき

current router は pass-through 対象拡張子をハードコードしています。

望ましい将来方針は、その policy を config へ移すことです。

主な参照先:

- `asset/docs/new-world/html-pass-through.md`
- `asset/unit/router/docs/calc-route-2018.md`

### Layout asset の WebPack 登録は各 layout が主体的に行うべき

current 実装では、WebPack module や WebPack unit 側に、layout の `js` / `css` directory を自動的に register する経路があります。

ONEPIECE Framework の To-Be では、layout-specific asset の選択と登録は各 layout が主体的に行います。

望ましい将来方針は次です。

- `asset/layout/<layout-name>/js/` や `asset/layout/<layout-name>/css/` が存在するだけでは pack されない
- 各 layout は、自分が必要とする JavaScript / CSS を自分の初期化処理や template flow で `OP()->Unit()->WebPack()->Auto()` に明示登録する
- WebPack unit は、登録済み asset の state、grouped output、cache、minify に集中する
- WebPack module は、grouped asset request の delivery-side entry に集中する
- WebPack unit/module は、layout-specific asset policy を自動的に決めない

この方針により、layout の意図しない asset inclusion を避け、責務境界と debugging point を明確にします。

主な参照先:

- `asset/docs/important-gaps.md`
- `asset/docs/important-gaps.ja.md`
- `asset/docs/op/responsibility-boundaries.md`
- `asset/docs/op/responsibility-boundaries.ja.md`
- `asset/docs/webpack.md`
- `asset/docs/webpack.ja.md`
- `asset/docs/module/webpack.md`
- `asset/docs/module/webpack.ja.md`
- `asset/module/webpack/docs/as-is.md`
- `asset/module/webpack/docs/as-is.ja.md`

### `OP()->URL('.')` は default で FQDN を返さない方がよい

current 挙動では scheme と host を含む完全 URL を返します。

望ましい将来方針は、framework の思想に合わせて、その abstraction level では default で FQDN を返さないことです。

主な参照先:

- `asset/core/docs/op-url.md`

### `isAdmin()` の localhost 自動 admin は設定化したい

current 挙動では localhost は自動的に admin 扱いです。

望ましい将来方針は、これを設定で切り替え可能にすることです。

主な参照先:

- `asset/docs/core/is-admin.md`
- `asset/core/docs/is-admin.md`

### request merge を追加するなら explicit API が望ましい

current の `OP()->Request()` は GET と POST を暗黙 merge しません。

もし merged request access を追加するとしても、望ましい将来方針は、default 挙動を黙って変えるのではなく、explicit な API または explicit な mode にすることです。

主な参照先:

- `asset/docs/core/op-request.md`
- `asset/core/docs/op-request.md`

## 維持ルール

将来設計の方向性が、人間が 1 か所で確認できるべき重要度を持つなら、詳細文書に local `[DOC-FUTURE]` note を残すだけでなく、この file にも追加して下さい。
