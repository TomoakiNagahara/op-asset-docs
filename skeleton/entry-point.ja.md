# アプリケーションのエントリーポイント

## 概要

ONEPIECE Framework では、常に `app.php` がアプリケーションのエントリーポイントになります。

アプリケーションは `app.php` を通して実行され、個別のページファイルを直接メインの実行起点にすることは前提としていません。

## `app.php` の役割

`app.php` は次の役割を持ちます。

- アプリケーション起動用の定数を設定する
- `APP_ROOT` を設定する
- bootstrap 処理を読み込む
- framework を初期化する
- app unit に処理を委ねる

また、`asset/bootstrap/` 配下の layer についても注意が必要です。

ここは OP-CORE が fully available になる前の段階に属します。

そのため bootstrap 側の file は、通常の reusable な framework/application logic ではなく、自立した startup code として理解するべきです。

bootstrap の後、アプリケーションは次の処理に進みます。

```php
OP()->Unit()->App()->Auto();
```

通常の起動プロセスという意味では、ここで startup は終了し、その後の application-side control は App unit に引き渡されます。

その意味では、`app.php` の目的はかなり限定的に表現できます。

- framework を初期化する
- framework を利用可能な状態にする
- その後の処理を app unit に委ねる

## app unit の役割

`app.php` による framework 初期化が終わった後は、app unit が残りの処理を引き受けます。

つまり、初期化後のアプリケーション処理全体は、framework のライフサイクルの中で app unit がハンドリングする、という整理になります。

## Webサーバー側の責務

`app.php` の実行は、Webサーバーの設定によって行います。

つまり、リクエストが `app.php` に到達するように Webサーバー側を設定する必要があります。

例:

- rewrite rule
- document routing の設定
- built-in server の起動設定

## この設計の意味

この設計は、framework が単一のアプリケーション入口を前提としていることを意味します。

リクエストは最初に `app.php` に入り、その後 framework が次を処理します。

- bootstrap
- routing
- template dispatch
- アプリケーションライフサイクル制御

より正確には、次の分担です。

- `app.php` が初期化を担当する
- app unit がその後のアプリケーション処理を担当する

なお、submodule 未初期化などで bootstrap file が無い場合、`app.php` は通常 runtime に入らず、guidance 出力側へ fallback します。

## bootstrap の境界

`asset/bootstrap/` は startup 側の境界です。

そこから、実務上は次の 2 つのルールが導かれます。

1. bootstrap template や bootstrap 側の file は、一般的な application 用 reusable template として使うことを想定しない
2. bootstrap code は、bootstrap 自身の中で availability が確立される前に、通常の OP-CORE 機能が使えると仮定しない

理由は、bootstrap が framework fully up 前の実行段階に属するためです。

## 初期化と実行を分ける理由

framework の初期化と framework の実行を分業することで、repository の役割分担が明確になります。

具体的には、次の役割分担が整理しやすくなります。

- メイン repository である `Skeleton`
- submodule repository 群

さらに、この分離には Git 運用上の大きな利点があります。

初期化の責務とアプリケーション処理の責務を分けることで、次の効果につながります。

- 不要なコンフリクトの抑制
- 目的の混在した commit の抑制
- メイン repository と submodule repository の履歴のノイズ低減

その結果、Git の履歴をより簡潔に保ちやすくなり、履歴の意味も追いやすくなります。

## まとめ

ルールは単純です。

- 常に `app.php` がアプリケーションのエントリーポイントである
- `app.php` の主目的は framework の初期化である
- 初期化後の処理は app unit に委ねられる
- `app.php` を実行させるのは Webサーバー設定の責務である
- この分離は repository の役割分担を明確にし、Git のコンフリクト抑制と履歴の簡潔化にもつながる
