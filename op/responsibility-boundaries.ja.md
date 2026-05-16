# Responsibility Boundaries

## 目的

この文書は、主要な framework subsystem 間の責務境界を定義します。

目的は、AI や contributor が「どこを触るべきか」を判断しやすくすることです。

## 関連文書

- `for-tomoaki-nagahara.md`
- `invariants.md`
- `unit-module-boundary.md`
- `common-recipes.md`

## `app.php`

- framework entry と startup を担当する
- page 固有の application logic は担当しない
- App、Router、Layout の責務を吸収してはならない

## App Unit

- startup 後の application execution flow を担当する
- 解決済み endpoint を実行する
- 後段の rendering のために content を buffer する
- 必要に応じて Layout へ委譲する

## Router Unit

- endpoint と request args を解決する
- routing target を決める
- 最終 page framing は担当しない

## Layout Unit

- shared page framing と最終 HTML wrapper を担当する
- layout が有効な場合に content 生成後に実行される
- routing decision は担当しない

## Layout

- 自分自身に必要な layout-specific asset の選択と登録を担当する
- `asset/layout/<layout-name>/js/` や `asset/layout/<layout-name>/css/` が存在するだけで、自動的に pack されることを前提にしない
- WebPack unit や WebPack module に、自分の asset policy を暗黙に委譲しない

## Notice Unit

- 保存済み error を消費する
- 画面表示か mail 通知かを決める
- PHP error の捕捉自体は担当しない

## `OP_ERROR`

- framework が収集した error の保存を担当する
- 同一 error message の集約を担当する
- PHP handler の登録は担当しない
- 画面表示か mail 通知かの判断は担当しない

## Error Handler

- 到達可能な PHP error、未捕捉例外、shutdown 時 error を捕捉する
- error 保存層へ流し込む
- 最終的な notice 表示方法は決めない

## CI Unit

- code inspection flow を担当する
- commit marker の生成を担当する
- 保存済み CI 状態を通じて push-gate enforcement に関与する
- すべての Git rule を担当するわけではない

## CD Unit

- delivery 側の branch 制御や deployment 関連制御を担当する
- pure な CI marker check 以外の GitHub 向け push restriction を含む

## WebPack Unit

- asset registration state を担当する
- grouped output の生成を担当する
- cache / minify / output behavior を担当する
- layout-specific asset directory の自動発見や自動登録は、本来の責務ではない

## WebPack Module

- grouped asset の delivery-side request entry を担当する
- request と layout context を準備する
- 実際の grouped output は WebPack unit に委譲する
- layout-specific asset policy を持つべきではない

## Config System

- layered configuration loading を担当する
- `name.php` / `_name.php` override behavior を担当する
- rendering logic とは分離されるべきである

## `OP_UNIT_MAPPER`

- typed unit access mapping を担当する
- config mapping を通じて対応 unit 名を差し替える
- generic Unit system contract を超えて arbitrary な unit を instantiate する責務は持たない

## Interfaces

- 必要な contract を定義する
- 呼び出し側が依存してよい範囲を拘束する
- すべての内部実装選択を定義するわけではない
