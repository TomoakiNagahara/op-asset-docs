# Public HTML Submodule

## 概要

ONEPIECE Framework の skeleton の王道の使い方のひとつに、`public_html` submodule の利用があります。

該当する設定は次です。

- `asset/config/submodule/public_html/www.php`

## current の既定値

current の file では次を返しています。

- `skip => 1`

これは、既定では `public_html` repository を skip することを意味します。

## clone を有効にする

開発者が次を:

- `skip => 1`

から次へ変更すると:

- `skip => 0`

`public_html` repository は clone 対象になります。

つまり、`skip` を `1` から `0` に変更することが、`public_html` layer を有効にする切り替えです。

この設定変更を後から行った場合でも、

```sh
php asset/init/update.php
```

を実行すると、skeleton は `asset/config/submodule/*/*.php` を再走査します。その時点で `public_html` が skip されていなければ、clone 対象になります。

## 自分の repository を使う

さらに `url` を自分の repository に変更すれば、開発者は次を行えます。

- `public_html` を clone する
- `public_html` 配下を自由に編集する
- その変更を自分の repository に自由に push する

つまり、`public_html` 配下の内容を開発者自身が完全に ownership して管理できます。

## 運用上の意味

これは、ONEPIECE Framework skeleton の canonical かつ王道の使い方です。

実務上の意味は次です。

- framework / skeleton 側は再利用可能なまま保てる
- public HTML 側は application 開発者が ownership を持って育てられる
- 公開側 file 群を、自分の repository で管理できる
- `public_html` layer を、再利用可能な skeleton 側と分離して自由に編集・push できる

## `public_html` が便利な理由

`public_html` を document root にすれば、`asset/` を document root の外へ置けます。

すると、`asset/` へのアクセスを、runtime の挙動や web server rule だけに頼らず、単純な配置構造で防げます。

## それでも強制しない理由

skeleton は、`public_html` を framework 標準の document root として強制しません。

理由は次です。

- application を document root 直下だけでなく、subdirectory にも設置できるようにしたい
- hosting や deployment 環境によっては、document root を自由に選べない場合がある
- skeleton は意図的にこの自由度を残している

## まとめ

基本パターンは次です。

1. `skip` を `1` から `0` に変更する
2. 必要なら `url` を自分の repository に変更する
3. `php asset/init/update.php` を実行する
4. `public_html` を clone し、自分の editable / pushable layer として管理する

これは skeleton の canonical な利用パターンです。
