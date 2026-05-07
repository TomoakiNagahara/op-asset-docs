# App Unit

## 概要

App unit は、ONEPIECE Framework における application 全体の大枠、外側の flow を管理する unit です。

これは rendering や routing のすべての詳細を自分で抱えるための unit ではありません。

framework startup 完了後の、大きな application-side flow を管理するのが役割です。

## 歴史的背景

ONEPIECE Framework は、元々は個別に作っていた library 群を統合したところから始まりました。

初期段階では、結果としてかなり monolithic な状態でした。

- application logic と library logic が密結合していた
- maintenance が難しくなった
- specification が複雑になった

これを解消するために、framework はより疎結合な方向へ進みました。

core behavior を分離し、多くの機能を unit として再整理しました。

その分離の結果の一つが、従来 system 側に一体化していた「application の管理」も切り出したことです。

## `app.php` と App unit の関係

`app.php` は意図的に最小限にされています。

主な役割は次です。

1. application entry point として振る舞う
2. bootstrap を起動する
3. app root 関連の基準値を、最も判定しやすい場所として設定する
4. 実行時間やメモリ使用量の benchmark 用の基準値を定義する
5. application の管理を App unit へ委ねる

つまり `app.php` は、framework を起動し、application flow を管理する unit を起動する役目です。

## この分離の意味

この分離が重要なのは、責務を明確に保てるからです。

- `app.php` は entry-point / startup 側
- App unit は application flow management 側

これは、monolithic な system から、unit-oriented でより疎結合な構造へ移行したことの一部です。

