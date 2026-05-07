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
- current 実装順は次です。
  1. current directory
  2. unit template directory
  3. `asset/layout/<layout-name>/template/`
  4. `asset/template/`

なぜ重要か:

- 同名 file が複数箇所にあるとき、どの template が勝つかが変わる
- shared site template の基本運用は、この仕様順を前提にしている

主な参照先:

- `asset/docs/skeleton/template-directory.md`
- `asset/docs/skeleton/template-directory.ja.md`
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
