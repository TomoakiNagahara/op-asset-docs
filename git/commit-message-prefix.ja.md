# コミットメッセージ prefix

commit の目的を表す prefix を使います。

- `New:` 新しい file または構成要素を作成する。
- `Add:` 既存対象へ機能または設定を追加する。
- `Chg:` 仕様、設定、挙動を意図的に変更する。
- `Fix:` 不具合または意図しない挙動を修正する。
- `Doc:` documentation を追加または変更する。
- `Del:` file、機能、設定などの既存対象を削除する。
- `Mov:` file、処理、責務などの既存対象を移動する。

repository、branch、Year に固有の prefix は、これらの変更種別 prefix とは別です。利用できる prefix は repository config と hook behavior によって決まります。

rebase や push における CI/CD 関連の prefix 検査は、`asset/docs/cicd/git/rules.ja.md` を参照してください。
