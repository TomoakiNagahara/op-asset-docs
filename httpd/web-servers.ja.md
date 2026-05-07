# 対応する Web Server

ONEPIECE Framework は、複数の Web server 環境に対応しています。

代表例は次です。

- Apache
- Nginx
- LiteSpeed
- PHP built-in web server

つまり、この framework は単一の Web server 実装に縛られていません。

## 非対応

ONEPIECE Framework は、現時点では FrankenPHP に対応していません。

FrankenPHP は採用が進んでおり、もはや極端に niche な選択肢ではありませんが、従来の PHP-FPM 系と比べると、まだ標準的な実行基盤とは言えません。

理由は、worker-style operation では static 変数が request をまたいで process に残り得るためです。

その挙動は、framework が前提としている request lifecycle と state handling に合いません。

それでも、将来的に FrankenPHP をサポートしたい意思はあります。
