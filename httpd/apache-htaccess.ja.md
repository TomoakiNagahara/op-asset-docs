# Apache `.htaccess`

## 概要

ONEPIECE Framework では、Apache のリクエスト処理は `.htaccess` によって制御されます。

このファイルが、リクエストを `app.php` 経由で framework に渡すか、それとも静的ファイルとしてそのまま配信するかを決めます。

## 現在の挙動

現在の挙動は次の通りです。

### 1. 存在しないファイルを指定した場合

リクエスト先のパスが物理的に存在しない場合、そのリクエストは `app.php` に渡されます。

その後、framework の endpoint フローに進み、該当する `index.php` が実行されます。

つまり次の流れです。

- 存在しないファイルパス
- `app.php`
- endpoint の `index.php`
- framework 管理のレスポンス

### 2. 存在するファイルで、かつ拡張子が明示的に routing されていない場合

リクエスト先のファイルが物理的に存在し、かつ `.htaccess` でその拡張子が framework 側へ明示的に routing されていない場合、Apache はそれを静的ファイルとして配信します。

この場合、ONEPIECE Framework はレスポンスに関与しません。

つまり次の流れです。

- 存在するファイル
- 静的配信
- framework は関与しない

### 3. `.htaccess` で拡張子指定されているファイルを指定した場合

`.htaccess` で明示的に拡張子が指定されている場合、そのファイルが物理的に存在していても、リクエストは `app.php` に渡されます。

その後、framework の endpoint フローに進み、該当する `index.php` が実行されます。

これは画像に限りません。

`.htaccess` に設定されている拡張子に適用されます。

## この設計の意味

この設計は、静的配信と framework 管理の実行を分離するためのものです。

つまり、次のことが可能になります。

- 必要な静的ファイルはそのまま配信する
- 存在しないリソースは framework で動的に扱う
- 特定の拡張子は強制的に framework フローに通す

## まとめ

現在の Apache `.htaccess` の挙動は、次のように整理できます。

- 存在しないファイル -> `app.php` -> endpoint の `index.php`
- 存在するファイルで、拡張子が明示的に routing されていないもの -> 静的配信
- `.htaccess` で拡張子指定されているファイル -> `app.php` -> endpoint の `index.php`
