# PHP Version Support

## 目的

この document は、ONEPIECE Framework 2030 skeleton の PHP version support policy を記録します。

AI と contributor が通常参照する documentation として使うことを意図しています。

## 現在のサポート範囲

ONEPIECE Framework 2030 line は、PHP 8.0 以上をサポートします。

実用上の意味は次の通りです。

- PHP 8.0 以降は supported runtime line に含まれる
- PHP 7.x は 2030 runtime ではサポートしない
- PHP 5.x 以前もサポートしない

## Runtime Enforcement

runtime requirement は `asset/core/Bootstrap.php` で enforce されます。

bootstrap responsibility boundary については、次を参照します。

- `asset/docs/core/bootstrap.md`

現在の bootstrap rule は、PHP 8.0.0 より古い version を拒否します。

```php
if( version_compare(PHP_VERSION, '8.0.0') < 0 ){
	exit('...');
}
```

この bootstrap check が、現在もっとも重要な implementation signal です。

この check が変わる場合は、この document も同時に更新するべきです。

## README Signal

repository README は、現在 framework を PHP 8.x project として示しています。

これは runtime rule と一致しています。

```text
PHP 8.0 or higher
```

## CI Matrix Note

CI configuration には historical、experimental、transition 用の entry が含まれる場合があります。

CI matrix entry が runtime bootstrap requirement と衝突する場合、それを primary public support statement として扱ってはいけません。

通常の documentation と agent decision では、この document と bootstrap check を support baseline として使います。

## AI と contributor 向け guidance

2030 skeleton に新しい code を追加する場合、PHP 8.0 以上を前提にします。

task が historical analysis や migration documentation を明示的に要求していない限り、PHP 7.x 用の compatibility workaround を追加してはいけません。

2030 runtime が PHP 7.x をサポートしているように説明してはいけません。

compatibility を document する場合は、次を使います。

```text
PHP 8.0 or higher
```

または:

```text
PHP 8.x
```

minimum version が重要な場合は `PHP 8.0 or higher` を使います。

短い marketing-style summary だけなら `PHP 8.x` を使っても構いません。
