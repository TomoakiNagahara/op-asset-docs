# Template Directory

## 概要

この skeleton では、`asset/template/` は主に framework 本体が利用する default template を保存する場所です。

これは、開発者向けやエンドユーザー向けの application template を何でも置くための一般的な置き場、という位置付けではありません。

## 運用上の意味

開発者やエンドユーザーが、自分の template をここに保存すること自体は可能です。

ただし、その場合は commit や template の ownership の管理が難しくなることがあります。

つまり次の意味です。

- 技術的には可能
- ただし version control 上の運用摩擦を生みやすい

その tradeoff を利用者自身が意図的に管理できるなら、利用しても構いません。

## 推奨される運用パターン

framework の基本運用としては、次のように site-specific な layout を `asset/layout/` 配下に作るのが基本です。

- `asset/layout/<layout-name>/`

そして、次のような shared template が必要な場合は:

- header
- footer
- menu

通常は次に配置するべきです。

- `asset/layout/<layout-name>/template/`

`asset/template/` に置くのではありません。

## 意図された読込優先順位

framework の仕様は次です。

1. current directory
2. `asset/layout/<layout-name>/template/`
3. `asset/template/`
4. unit template directory

[DOC-PRIORITY1] current の `OP()->Template()` As-Is はこの仕様に従っていません。

current As-Is は `OP_TEMPLATE` trait の documentation が所有します。`asset/core/trait/docs/op-template.ja.md` を参照してください。

つまり、次に置かれた file は:

- `asset/layout/<layout-name>/template/`

同名の file が次にあっても、それより先に判定されます。

- `asset/template/`

このため、site-level customization の shared template は、layout-specific template 側に置くのがより適切です。

## 実務上の注意

template を追加・編集する際は、`asset/template/` が純粋な user-owned application content というより、framework-default 寄りの領域であることを意識するべきです。

そのため、local application template をこの領域に混在させる前に、その影響を考慮する必要があります。
