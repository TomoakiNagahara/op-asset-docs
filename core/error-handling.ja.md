# エラーハンドリング

ONEPIECE Framework は、framework が有効化された後に取得可能なエラーを、すべて取得することを方針にしています。

より正確には、通常の PHP アプリケーション設計において、どのような手段を使っても本質的に取得できないエラーを除き、framework が到達できる範囲のエラーをすべて取得します。

## application level の設計上の好み

ONEPIECE Framework は、framework の思想として、あらゆる application error を例外 `throw` で表現することを default とは考えていません。

可能であれば、失敗を丁寧に呼び出し元へ返す設計を好みます。

それは `throw` するより手間がかかります。

それでも、コードを使う側にとっては、その方が助けになると考えています。

これは設計上の好みであり、例外が一切存在しないという主張ではありません。

framework は runtime level では未捕捉の例外を引き続き捕捉します。

## 取得対象

- `set_error_handler()` による通常の PHP エラー
- `set_exception_handler()` による未捕捉の例外および未捕捉の `Throwable`
- `register_shutdown_function()` と `error_get_last()` による終了時の fatal 系エラー

## PHP 実行時の方針

`asset/config/php.php` では現在、次を設定しています。

- `display_errors = Off`
- `log_errors = Off`

これは、通常の application 向けエラーハンドリングにおいて、PHP 標準の直接画面出力や標準 error log に依存しない方針であることを意味します。

その代わりに、framework 自身の保存と notice の流れにエラーを流し込みます。

## 関連 framework 文書

- `../op/invariants.ja.md`
- `../op/responsibility-boundaries.ja.md`
- `../op/common-recipes.ja.md`

## 取得対象外

framework は、原理的に取得不能なエラーまで取得できるとは主張しません。

例えば次は対象外です。

- PHP のアプリケーション実行に到達する前の失敗
- framework の bootstrap や handler 登録が完了する前の失敗
- ユーザーコードが `try/catch` で既に処理済みの例外

これらは framework の欠陥ではなく、framework が現実的に観測できる範囲の外側です。

## 保存先

取得したエラーは session に保存されます。

保存先の名前空間は次です。

- `$_SESSION[_OP_NAME_SPACE_][_APP_ID_]['OP_ERROR']`

これにより、エラーは現在の application のスコープ内に保持されます。

## 出力と通知

application の終了時に、session に保存されているエラーが処理されます。

- `OP()->isAdmin()` が `true` の場合は、画面に表示されます
- `OP()->isAdmin()` が `false` の場合は、管理者へメール送信されます

この設計により、開発者は直接エラーを確認でき、production の利用者には内部エラー詳細を見せない運用ができます。

## 関連する技術ドキュメント

技術的な詳細は分離して記述します。

- `asset/core/include/docs/error-handler.md`
- `asset/core/include/docs/error-handler.ja.md`
- `asset/unit/notice/docs/error-notice.md`
- `asset/unit/notice/docs/error-notice.ja.md`
