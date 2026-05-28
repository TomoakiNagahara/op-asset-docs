# Coding Rules

## 目的

この document は、ONEPIECE Framework repository における framework-wide な coding rule を記録します。

これらの rule は、application code、CORE change、UNIT package、MODULE package、template、configuration、script、documentation-related code に適用します。

## public and durable name

file name、class name、method name、function name、config key、request key、URL、template entry name、command name、documented API は、durable な public surface になる可能性があります。

[DOC-RISK] requested public name または durable name に spelling mistake が含まれているように見える場合、それを無条件に code へ写してはいけません。
human user は word を typo することがあります。
name を作成または公開する前に、intended spelling を user に確認してください。

これは UNIT / MODULE だけの rule ではなく、framework-wide な coding rule です。

misspelled name との compatibility が後から必要な場合は、明示的な compatibility alias、wrapper、redirect、migration path として実装します。
偶然の typo を primary implementation name にしてはいけません。

## comment

code comment は English で書きます。

comment は、非自明な behavior、intent、assumption、constraint、risk を明確にするために使います。
code を読めば分かることを言い換えるだけの comment は避けます。

## method closing comment

method の閉じかっこには、常に method name を comment として追加します。

method name の大文字小文字は実際の method name に合わせます。

```php
class Foo
{
	function Bar()
	{

	} // Bar
}
```

理由は、Git の diff が method boundary を常に賢く扱えるわけではないためです。
ある method body を修正しただけでも、修正とは関係のない隣の method の閉じかっこが diff に含まれることがあります。
この不要な diff noise は、rebase や merge 時の conflict の大きな原因になります。

closing comment は、Git と human reviewer に安定した boundary marker を与え、無関係な隣接 method が change に巻き込まれる可能性を下げます。

## singleton local static variable

method 内の local static variable に軽量な singleton instance を保持する場合は、明示的な `if` block で初期化し、その後で variable を return します。

```php
class Foo
{
	function Common() : Common
	{
		static $common;

		if(!$common ){
			$common = new Common();
		}

		return $common;
	} // Common
}
```

この pattern では、`return $common ??= new Common();` のように return expression へまとめてはいけません。
Eclipse など一部の IDE では、初期化と return を 1 つの expression にまとめると、local static variable が未使用であるという警告が出ることがあります。
明示的な形式にすることで singleton の意図が読みやすくなり、IDE の不要な警告も避けられます。

## configuration

config file は短く、ぱっと見で分かる状態に保ちます。

長い procedural logic、external service data、大きな hardcoded list を config file に隠してはいけません。
その behavior は、責任を持つ function、class、UNIT、MODULE、または web-server / deployment setting へ移します。

読みやすい config default は coding manner として尊重します。
default config value は、third-party user が documentation を読んだり AI assistant に尋ねたりしなくても理解できるようにします。
config key が少数の意味ある値を受け取る場合は、comment で valid value を列挙または例示し、その value の意味と runtime effect を config file の中に直接残します。

## debugging

debugging に `var_dump()` や `print_r()` を使ってはいけません。

framework-aware debug output には `D()` を使います。
