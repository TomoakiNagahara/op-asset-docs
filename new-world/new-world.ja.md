# NEW WORLD

## 概要

ONEPIECE Framework における **NEW WORLD** は、3 つの本質的な考え方で成り立つ application execution model です。

その 3 つは次です。

1. file 指向の endpoint モデル
2. `index.php` を先に実行して結果を保持し、後から layout 側で展開すること
3. HTML Pass-Through

それ以外の特徴は、これら 3 つから派生した結果や拡張として理解するべきであり、別の核心原理として扱うべきではありません。

## 1. file 指向の endpoint モデル

最初の本質は routing model です。

request URL を document root 相当の path に換算したとき、framework はその path を走査して `index.php` を探します。

もし `index.php` が見つかれば、その file が endpoint になります。

従来の MVC の言い方をすれば、その `index.php` は多くの framework における controller に相当します。

ここで重要なのは、NEW WORLD が次を起点にしていないことです。

- class 名 routing
- method 名 routing

NEW WORLD は file 指向の endpoint モデルから始まります。

これにより、深いネスト構造を自然に扱え、不要な controller 的儀式も減らせます。

### 例

```text
https://example.com/foo/bar/hoge/fuga?key=var
```

で、最も近い endpoint が次だとします。

```text
/foo/bar/index.php
```

この場合:

- `/foo/bar/index.php` が endpoint になる
- その後ろの path segment は router 引数になり得る
- `key=var` は通常の query string のまま扱われる

実際には、`OP()->Unit()->Router()->Args()` で次に相当する値を取得できます。

```php
['hoge', 'fuga']
```

## 2. endpoint を先に実行し、後から layout で展開する

2 つ目の本質は実行順です。

NEW WORLD では、まず endpoint を実行します。

その結果は保持されます。

その後で必要に応じて layout 側で最終レスポンスとして展開します。

これは、多くの従来型 framework が、まず layout を先頭から実行し、その途中で controller や view の処理を呼び出す流れとは大きく異なります。

従来の順序では、レスポンス後半での変更が扱いにくくなることがありました。

例えば Cookie のような header 系情報は、レスポンスボディ出力前に設定しなければいけません。

layout の出力が先に始まってしまうと、後から Cookie や header を変更するには、特別な hook 的な仕組みが必要になることがあります。

NEW WORLD は順序を逆にすることで、これを避けます。

1. endpoint を実行する
2. 結果を保持する
3. 最終的な layout 挙動を決める
4. 最終レスポンスを出力する

この順序こそが、endpoint 側からの制御を自然に成立させる中心理由のひとつです。

## 3. HTML Pass-Through

3 つ目の本質は HTML Pass-Through です。

歴史的には、これは NEW WORLD の最初期から見えていた特徴のひとつです。

HTML file を、従来型の controller class / controller method 構造を強制せずに、framework フローへ参加させる仕組みでした。

つまり、HTML 指向の plain な file であっても、framework の実行モデルの中で動かせるということです。

その後、同じ考え方は他の resource にも拡張されましたが、歴史的名称は残りました。

したがって HTML Pass-Through は side feature ではありません。

NEW WORLD を構成する最初期からの柱のひとつです。

## 実行フロー

これら 3 つの考え方に基づく current flow は次です。

1. Router unit が URL から endpoint を解決する
2. App unit が `OP()->Template()` により endpoint を実行する
3. 出力を保持する
4. framework が layout を適用するか判断する
5. layout を使う場合は Layout unit が最終レスポンスを展開する
6. そうでなければ保持済み content をそのまま出力する

## この model が重要な理由

この 3 つの考え方は、複数の問題を同時に解決します。

- routing を class 名や method 名起点にしなくてよい
- 深い directory 構造を自然に保てる
- HTML file を直接 framework フローへ参加させられる
- endpoint 側で Cookie などのレスポンス状態を先に準備できる
- 十分な文脈が分かるまで最終描画を遅延できる

## **グランドライン**

NEW WORLD の **グランドライン** は、単独の trick ではありません。

次の 3 つの組み合わせです。

- file 指向の endpoint 解決
- 先に実行して後から描画する順序
- HTML Pass-Through

この組み合わせが、NEW WORLD を従来型 framework model から分ける核心です。

## current implementation との関係

MIME を見て layout を判断することや、Router の拡張子一覧のような current implementation は、NEW WORLD そのものの本質と混同すべきではありません。

それらは current implementation detail です。

本質は、上記 3 つの原理にあります。

## まとめ

NEW WORLD は、まず次の 3 点から理解するべきです。

1. endpoint モデルが file 指向である
2. endpoint を先に実行し、後から最終 layout を描画する
3. HTML Pass-Through が初期からの本質的な構成要素である
