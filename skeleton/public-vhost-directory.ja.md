# Public Virtual Host Directory

## 概要

app root にある `public/` directory は、1 つの skeleton が複数の virtual host を含む場合に使われます。

通常の single-site public document root は、その layer を有効にしている場合は `public_html/` です。multi-host setup では、`public/` が virtual-host app root の container になります。

## `public/` の意味

`public/` を、単なる flat な static public directory だと決めつけてはいけません。

複数 virtual host が設定されている場合、`public/` の child directory は 1 つの virtual host の app root になり得ます。その child には、その host に必要な entry file、web-server-facing file、local public asset、configuration が置かれることがあります。

例:

- `public/<サブドメイン名>/`
- `public/<サイト名>/`

これらはそれぞれ、web server 側の設定によって別々の virtual host の app root として割り当てられます。

## `public_html/` との関係

`public_html/` は、その submodule を有効にしている場合の single public site 向けの通常の public document-root layer です。

`public/` は、skeleton が複数の virtual-host app root を保持する必要がある場合に使います。

## 編集ルール

`public/` 配下を編集する前に、まず変更を所有している階層を特定してください。

- `public/` 自体: virtual-host container structure
- `public/<host>/`: 1 つの virtual host の app root
- `public/<host>/css`、`public/<host>/img` など: その virtual host 専用の public asset

task が shared hosting structure の変更を明示していない限り、virtual host 間で behavior を移動してはいけません。

## runtime 上の意味

どの `public/<host>/` directory が request の active app root になるかは、virtual-host web-server configuration が決めます。

request がその virtual host app root に到達した後は、通常の skeleton startup flow が続きます。host entry が `app.php` を読み込み、`APP_ROOT` がその app root から設定され、bootstrap が実行され、application lifecycle が続きます。
