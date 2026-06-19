# 利用可能な UNIT

## 目的

この document は、skeleton dependency map から利用できる可能性がある ONEPIECE Framework の UNIT package を、AI agent 向けに簡潔にまとめます。

database access、form handling、SQL、ORM、validation、model 的な persistence が必要な作業では、application code 側で別系統の layer を独自実装せず、責任を持つ UNIT とその public API または設定済み mapping を使います。

## agent 向け rule

DATABASE、FORM、ORM、SQL、validation、model 的な作業では、次の順で判断します。

1. 責任を持つ UNIT が既に存在するか確認する。
2. UNIT の public API または設定済み unit mapping を使う。
3. 適切な UNIT が存在するのに、同じ責務を application-local な layer として別実装しない。
4. install 済み UNIT が要件を満たさない場合は、application configuration、`asset/config/unit.php` mapping、project-owned UNIT、UNIT を組み合わせる MODULE、または責任を持つ framework unit package への変更のどれが適切かを判断する。

## unit mapping

`asset/config/unit.php` は、互換性のある代替実装を選ぶための application-side mapping point です。

unit repository の skeleton dependency map は `asset/config/submodule/unit/` にあります。

## skeleton unit repository map

| UNIT | dependency map | 典型的な責務 |
| --- | --- | --- |
| API | `asset/config/submodule/unit/api.php` | API 関連の unit behavior。 |
| App | `asset/config/submodule/unit/app.php` | bootstrap 後の application flow。 |
| Bitcoin RPC | `asset/config/submodule/unit/bitcoin-rpc.php` | Bitcoin RPC integration。 |
| CD | `asset/config/submodule/unit/cd.php` | continuous delivery orchestration。 |
| CI | `asset/config/submodule/unit/ci.php` | continuous integration checks。 |
| Database | `asset/config/submodule/unit/database.php` | database access。 |
| Dump | `asset/config/submodule/unit/dump.php` | debug と structured dump output。 |
| Form | `asset/config/submodule/unit/form.php` | form configuration、rendering helper、submitted value、form state。まず `asset/unit/form/docs/usage.ja.md` を読む。source-level troubleshooting や refactoring の場合だけ `current-spec.ja.md` を読む。 |
| Git | `asset/config/submodule/unit/git.php` | Git 関連操作。 |
| Html | `asset/config/submodule/unit/html.php` | HTML 関連 output helper。 |
| Layout | `asset/config/submodule/unit/layout.php` | layout rendering control。 |
| Login | `asset/config/submodule/unit/login.php` | login 関連 behavior。 |
| Model | `asset/config/submodule/unit/model.php` | model 的な application data behavior。 |
| Notice | `asset/config/submodule/unit/notice.php` | notice と error notification behavior。 |
| ORM | `asset/config/submodule/unit/orm.php` | ORM behavior と persistence abstraction。 |
| QQL | `asset/config/submodule/unit/qql.php` | QQL query 関連 behavior。 |
| Router | `asset/config/submodule/unit/router.php` | route resolution。 |
| SQL | `asset/config/submodule/unit/sql.php` | SQL query handling。 |
| Validate | `asset/config/submodule/unit/validate.php` | form や submitted data 周辺を含む validation behavior。 |
| WebPack | `asset/config/submodule/unit/webpack.php` | asset registration、grouping、output generation。 |

## Form と Validation

Form unit は `OP()->Unit()->Form()` で呼び出します。

Form unit の source は `asset/unit/form` です。

Form unit は form configuration、rendering helper、submitted value、form state を担当します。validation は Form unit ではなく、Validate unit が担当します。

通常の Form 利用では `asset/unit/form/docs/usage.ja.md` を読みます。source-code troubleshooting、behavior investigation、refactoring では `asset/unit/form/docs/current-spec.ja.md` を読みます。

## 関連 document

- `unit-replacement.ja.md`
- `app-unit.ja.md`
- `../../unit/form/docs/usage.ja.md`
- `../../unit/form/docs/current-spec.ja.md`
- `../op/unit-module-boundary.ja.md`
- `../op/unit-module-authoring.ja.md`
