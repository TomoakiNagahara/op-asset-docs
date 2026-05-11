# `Init()` の As-Is

## 概要

`asset/init/function/Init.php` は、`asset/config/submodule/**` 配下の設定に基づいて OP-managed repository を初期化します。

`asset/init/update.php` から、各 submodule configuration file ごとに呼び出されます。

## clone URL の取得元

`Init()` は clone URL を次から読み取ります。

- `$config['url']`

current の submodule config files では、通常次のような HTTPS GitHub URL を使います。

```text
https://github.com/onepiece-framework/op-core-8.git
```

## GitHub owner override

clone 前に、次が設定されている場合:

```php
Request('github')
```

かつ URL が `onepiece-framework` を対象にしていれば、`Init()` は GitHub owner name を置き換えます。

たとえば:

```text
https://github.com/onepiece-framework/op-core-8.git
```

は次のようになります。

```text
https://github.com/example-account/op-core-8.git
```

clone 後、元の `onepiece-framework` remote は `onepie` として保持されます。

## SSH clone scheme

次の場合:

```php
Request('scheme') === 'ssh'
```

`Init()` は `git clone` を実行する前に、HTTPS GitHub clone URL を SSH GitHub clone URL に変更します。

current の変換は次です。

```text
https://github.com/owner/repository.git
```

は次になります。

```text
git@github.com:owner/repository.git
```

この変換は、任意の `github` owner replacement の後、shell escaping の前に行われます。

そのため、次の 2 つの option は組み合わせられます。

```text
php asset/init/update.php github=example-account scheme=ssh
```

この場合、次のような config URL は:

```text
https://github.com/onepiece-framework/op-core-8.git
```

次として clone されます。

```text
git@github.com:example-account/op-core-8.git
```

## Scope

`scheme=ssh` の挙動が変更するのは、次に一致する clone URL だけです。

```text
https://github.com/owner/repository.git
```

その他の URL format は変更されません。

request value は `asset/init/function/Request.php` を通して読み込まれるため、次のいずれかから指定できます。

- `asset/config/init.php`
- `asset/config/_init.php`
- `scheme=ssh` のような CLI argument

