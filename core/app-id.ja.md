# App ID

## 概要

ONEPIECE Framework では、`_APP_ID_` は次のファイルで定義されます。

`asset/config/app_id.php`

この値には大きく 2 つの役割があります。

- 暗号化の基礎情報として使われる
- session namespace の一部として使われる

## 定義

App ID は、次のファイルで定義された seed 値から生成されます。

`asset/config/app_id_seed.php`

その後、次のように定義されます。

```php
define('_APP_ID_', $app_id);
```

## 暗号化での利用

App ID は framework の暗号化レイヤで使われます。

現在の実装では、`Encrypt.class.php` が `OP::AppID()` を次の source material として使っています。

- 初期ベクトルの元
- password の元

ただし、server 変数で明示的に上書きされている場合はそちらが優先されます。

つまり、通常時の framework 暗号化では App ID が基礎情報の一部になっています。

## session namespace での利用

App ID は framework の session 保存構造にも使われています。

現在の session 参照構造は概ね次の形です。

```php
$_SESSION[_OP_NAME_SPACE_][unit-group][unit-name][_APP_ID_]
```

より正確には、framework は session データを次の多段キー配下に保存します。

- framework namespace
- unit group
- unit name
- App ID

## この仕様が重要な理由

PHP の session 保存領域は、同じ session context を共有していれば、同一ドメインや同一 session 環境の中で複数アプリが関わることがあります。

そのため、単純に `$_SESSION['key']` のように使うだけでは、アプリケーションごとのデータ分離が不十分です。

ONEPIECE Framework では `_APP_ID_` を session namespace の一部に含めることで、framework 内部の保存構造で app 単位の分離を行っています。

## 厳密な意味

この設計は、別アプリが生の PHP session 全体を絶対に読めなくする、という意味ではありません。

より正確には次の意味です。

- framework 標準の session access は App ID で namespaced される
- framework 管理の session 値は別アプリと衝突しにくくなる
- framework 内部では App ID によりアプリ単位の分離が行われる

したがって、この仕様の目的は絶対的な不可視化ではなく、分離と衝突回避です。

## 目的

この設計の意図は次の通りです。

- 異なるアプリ間で session key が衝突することを避ける
- framework 管理の session データを app identity ごとに分離する
- app identity を暗号化の基礎情報の一部として使う

## まとめ

`_APP_ID_` は、ONEPIECE Framework における application identity です。

これは次の用途に使われます。

- 暗号化の基礎情報
- session namespacing

特に、複数アプリが同じ広い session 環境を共有し得る状況で、framework 管理状態を分離するために重要な役割を持ちます。
