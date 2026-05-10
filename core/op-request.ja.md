# `OP()->Request()`

## 概要

`OP()->Request()` は、web 実行と CLI 実行の両方に対する framework の request accessor です。

主な考え方は、呼び出し側が次を手動で切り替えなくてよいようにすることです。

- CLI 引数
- web の GET
- web の POST
- JSON request body

framework が current の request 形式を解決し、ひとつの access point から request 値を返します。

## 主な挙動

典型的な使い方は次です。

```php
$request = OP()->Request();
$value   = OP()->Request('key');
```

これは次を意味します。

- CLI では `key=value` 形式の引数を同じ API で読める
- web では GET と POST を同じ API で読める
- 対応している場合は JSON request body も自動で parse される

## 実務上の意味

目的は、application code 側が環境や transport の違いで分岐せず、request accessor を一つに統一できるようにすることです。

その結果、呼び出し側はより simple で一貫した書き方になります。

これは、統一された明示的 interface を好む framework の思想にも合っています。

## なぜ encode 済みの値を返すのか

この設計意図は実務的です。

人間は、出力前の escape をうっかり忘れがちです。

そのため、default で完全な生値を返すより、encode 済みの値を返す方が安全だという判断があります。

これは、特に HTML 指向の利用で accidental な出力事故を減らすために、安全側へ倒した framework の設計判断です。

## current の web 挙動

current 実装では次のようになっています。

- `CONTENT_TYPE` が `application/json` で始まる場合は request body を JSON decode する
- それ以外は `$_POST` または `$_GET` のどちらかを読む

したがって current model は次です。

- JSON body
  または
- POST parameter
  または
- GET parameter

## [DOC-NOTE] GET と POST の merge について

framework は、暗黙の GET+POST merge を理想的な default とは考えていません。

実務上の懸念は、暗黙 merge にすると次が分かりにくくなることです。

- 値の出所がどこか
- 同じ key が両方にある時にどちらを優先すべきか
- なぜ最終値がその値になったのか

framework の思想から見ても、異なる transport source を暗黙に混ぜるより、request source の挙動を明示的に保つ方が理解しやすいと考えています。

## [DOC-NOTE] 安全性の解釈

`OP()->Request()` は、あらゆる文脈に対する universal sanitizer だと解釈すべきではありません。

むしろ、人間が最も protection を忘れやすい HTML 出力側に寄せて、安全側へ倒した request accessor と理解する方が適切です。

SQL、shell、URL、JSON、署名検証のような非 HTML 文脈では、引き続き文脈に応じた別の処理が必要です。

## [DOC-FUTURE] 将来の方向性

もし merged request access が必要になるとしても、望ましい方向は、`OP()->Request()` の default 挙動を黙って変えることではなく、明示的な API または明示的な mode として提供することです。

## [DOC-FUTURE] `PUT`, `PATCH`, `DELETE` について

current の設計方針では、`OP()->Request()` に `PUT`, `PATCH`, `DELETE` 専用の request parse 対応を追加する予定はありません。

現在の対象範囲は、practical な CLI 入力、GET、POST、JSON 処理を中心にしています。

## current の CLI 挙動

CLI 実行では、次の形式の引数:

```text
key=value
```

が収集され、同じ `OP()->Request()` API で参照できます。

current の CLI/Web 分岐は、`$_SERVER['SHELL']` ではなく、framework の shell 判定に基づいて選ばれます。

## 注意

この文書は framework level の current As-Is を説明しています。

request source の選択や最後の encoding のような技術詳細は、別の core 技術文書に分けています。
