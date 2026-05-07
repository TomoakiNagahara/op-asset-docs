# `isAdmin()`

## 概要

`OP()->isAdmin()` は、ONEPIECE Framework における重要な環境判定のひとつです。

現在の request を管理者アクセスとして扱うかどうかを決定します。

この判定は、例えば次の挙動に影響します。

- `D()` による debug 出力
- 画面上のエラー表示
- 開発者向け notice の描画

## 基本的な挙動

通常の application 運用では、`isAdmin()` は次によって制御されます。

- localhost からのアクセス
- `asset/config/admin.php` に設定された admin IP

つまり、この framework は local 環境で developer mode に入るまでの手間をかなり小さくしています。

## localhost を常に admin 扱いする理由

localhost を admin 扱いする目的は、利便性です。

これにより次が可能になります。

- 追加設定なしで即座に debug できる
- local 開発で `D()` や error 出力を使える
- framework の初回利用をスムーズにできる

これは、開発生産性のための意図的な設計です。

## [DOC-RISK] 注意点

ただし、localhost ルールにはいくつかの注意点があります。

- 特殊な環境では、`REMOTE_ADDR` が想定より広く localhost に見えることがある
- reverse proxy、container、VM、remote development 環境では localhost の意味が曖昧になることがある
- localhost が自動的に特権扱いされるため、local の挙動と production の挙動がずれることがある

## 実務上のリスク評価

とはいえ、これは通常、静かに潜伏するリスクではありません。

`isAdmin()` が意図せず `true` になっていれば、例えば次のような挙動が見えやすくなります。

- `D()` の出力が見える
- エラー詳細が画面に出る
- admin 向けの挙動がすぐに表面化する

そのため、開発者や管理者は比較的早い段階で異常に気づきやすいです。

この意味では、これは隠れた長期的障害というより、可視化されやすい誤判定です。

ただし、可視化されやすいことは無害であることを意味しません。

公開到達可能な request であれば、開発者が気づく前に第三者が debug 寄りの挙動を目にする可能性はあります。

## 運用上の指針

現在の設計は local 開発には実用的ですが、その前提は明確に理解されるべきです。

- localhost の自動 admin は利便性のための仕様である
- 特殊環境では明示的な確認が必要である
- production に近い環境では admin 判定を意図的に確認した方がよい

## [DOC-FUTURE] 将来方針

将来的には、localhost を自動的に admin 扱いするかどうかを、設定ファイルで切り替えられるようにしたい意図があります。

つまり、localhost を常に admin とするかを application 設定で決められるようにする方向性があります。

現行の挙動は利便性のために固定されていますが、この hardcoded な挙動を永続前提としているわけではありません。
