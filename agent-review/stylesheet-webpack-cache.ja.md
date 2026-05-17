# Stylesheet WebPack Cache Agent Review

## 対象

この記録は、CSS の変更が反映されず、WebPack 周辺で問題が起きているように見えた件の調査を残すものです。

作業範囲は次です。

- 編集前に skeleton customization boundary を確認した
- framework request flow 経由で WebPack CSS output を再現した
- `op-unit-webpack` の WebPack cache hash timing を修正した
- ユーザーの指摘を受け、`public/` が virtual-host container であることを document 化した

## トラブル

最近の public site 変更後、stylesheet が反映されない状態でした。

見えていた症状は WebPack CSS delivery 周辺でした。page は次のような stylesheet URL を生成していました。

- `/webpack/css/index.css?layout=<レイアウト名>&hash=...`

最初に PHP built-in server で直接試したときは、`asset/module/webpack/content/css/index.php` から 500 が返りました。しかしこれは再現方法の誤りでした。router script なしで server を起動したため、framework bootstrap 前に module entry file を直接実行していました。`public/app.php` または `public/<サブドメイン名>/app.php` を router として起動すると、通常の skeleton flow 経由で WebPack output は動作しました。

## 原因

実際の原因は cache invalidation でした。

`OP()->Unit()->WebPack()->Hash('css')` が、directory registration を concrete file に展開する前に hash を生成していました。

そのため、次のような directory registration は:

- `OP()->Unit()->WebPack()->Auto('css')`

directory 内の実 file ではなく、登録済み directory state を元に hash を作る可能性がありました。

`css/` 配下の file が変更、移動、追加されても、hash が古いままになることがあります。WebPack はこの hash を APCu cache key として使うため、browser は古い cached CSS に対応する URL を要求し続ける可能性がありました。

## 修正

修正は `asset/unit/webpack/WEBPACK_2024.trait.php` に行いました。

`Hash()` は、extension session を読み取って hash を作る前に、登録済み directory を展開するようになりました。

```php
// Include directory contents before generating the cache key.
self::_RegisterFilesFromDirectory();
```

これにより、cache key は request extension で実際に出力される file list を含むようになります。

## 確認

次を確認しました。

- `php -l asset/unit/webpack/WEBPACK_2024.trait.php`
- `php -S ... -t public public/app.php` による local framework request
- `php -S ... -t public/<サブドメイン名> public/<サブドメイン名>/app.php` による local virtual-host request
- 生成された WebPack CSS URL への same-session request

CSS response には次が含まれていました。

- `public/<サブドメイン名>/css/color.css`
- `public/<サブドメイン名>/css/welcome.css`
- `asset/layout/<レイアウト名>/css/flexbox.css`

修正後に hash が変わり、concrete registered CSS files が cache key に影響していることを確認しました。

## CI 結果

`./cicd` を実行しました。

ただし、WebPack patch とは別の既存 repository structure issue により完了しませんでした。

- `public/<サブドメイン名>` は nested repository / submodule-like path として存在するが、root `.gitmodules` に mapping がない。
- CI が、この checkout には存在しない `asset/core//docs` へ移動しようとした。

## Documentation Follow-up

ユーザーは、app root にある `public/` directory が virtual-host container であることを明確にしました。

通常は `public_html/` が public document root です。複数 virtual host がある場合、`public/` 配下の entry は、`public/<サブドメイン名>/` のように個別 virtual host の app root になり得ます。

これは次に記録しました。

- `asset/docs/CUSTOMIZATION_MAP.md`
- `asset/docs/CUSTOMIZATION_MAP.ja.md`
- `asset/docs/skeleton/public-vhost-directory.md`
- `asset/docs/skeleton/public-vhost-directory.ja.md`

## 教訓

[DOC-RISK] WebPack や module URL を local で再現するときは、bootstrap 済みになるよう skeleton router entry 経由で実行する。module content file の直接実行は、誤った 500 error を作ることがある。

[DOC-RISK] grouped asset の cache key は、pre-expanded directory registration state ではなく、実際に出力される concrete file list を元にする必要がある。

[DOC-GAP] multi-host skeleton setup では `public/` に virtual-host-container としての特別な意味がある。host mapping を確認せず、単一の flat な public web root として扱ってはいけない。

[DOC-FUTURE] WebPack cache test には、登録済み directory 配下で file が追加、移動、rename される case を含めるべき。
