# Core Bootstrap

## 目的

この document は、`asset/core/Bootstrap.php` の high-level responsibility を記録します。

bootstrap behavior を変更する前に、AI と contributor が関連する framework-level document に気付けるようにするための文書です。

## 範囲

`asset/core/Bootstrap.php` は OP-CORE の startup gate です。

通常の framework runtime が fully available になる前に実行されます。

現在の責務には次が含まれます。

- minimum supported PHP runtime version を確認する
- core definition を読み込む
- autoloader を登録する
- framework error handler を読み込む
- core trait、class、global function を読み込む
- core startup 後に必要になる baseline request value を設定する

## PHP Version Gate

`asset/core/Bootstrap.php` は現在、minimum PHP runtime version を enforce します。

現在の framework support baseline は次に記録されています。

- `asset/docs/op/php-version-support.md`

`asset/core/Bootstrap.php` の PHP version check を変更する場合は、同じ変更で `asset/docs/op/php-version-support.md` も更新します。

理由は、`php-version-support.md` が supported PHP versions について通常参照される public documentation だからです。

## Editing Guidance

`asset/core/Bootstrap.php` は high-impact startup file として扱います。

通常の application behavior、template、layout、routing、feature configuration のために変更してはいけません。

bootstrap behavior を変更する場合は、次を確認します。

- `app.php`
- bootstrap include order
- pre-core assumption
- 変更が `asset/docs/op/php-version-support.md` に影響するかどうか
- contract が変わる場合は、同じ commit で関連 documentation を更新する

## 関連文書

- `asset/docs/op/php-version-support.md`
- `asset/docs/op/invariants.md`
- `asset/docs/skeleton/entry-point.md`
- `asset/docs/skeleton/runtime-lifecycle.md`
- `asset/docs/CUSTOMIZATION_MAP.md`
