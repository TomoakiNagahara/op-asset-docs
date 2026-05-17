# エージェントのミス: 外部IPレンジをconfigにハードコードした

## 概要

AI エージェントが、Cloudflare 経由の admin 判定を修正しようとして、Cloudflare の IP range の長い list を local admin config file に直接追加しました。

これは誤った方向でした。

ユーザーが指摘した問題は次です。

- 外部 service が所有する IP range は、追加、削除、変更される可能性がある。
- その list を application config に置くと、更新負荷と運用リスクが発生する。
- application の maintenance model を変える内容なのに、agent は user に確認せず logic に組み込んだ。

## 何が起きたか

元の task は、Cloudflare 経由の request で admin 判定が動くようにすることでした。

framework の `isAdmin()` behavior は次を比較します。

- `REMOTE_ADDR`
- config に設定された admin IP value

Cloudflare が origin の前にいる場合、`REMOTE_ADDR` は proxy IP になり、visitor IP は `HTTP_CF_CONNECTING_IP` で渡されることがあります。

agent は header spoofing を防ごうとして、Cloudflare が公開している IPv4 / IPv6 range を `asset/config/_admin.php` に hardcode しました。

その結果、config file が長くなり、複数の責務が混ざりました。

- local admin IP configuration
- 外部 vendor IP range data
- CIDR matching implementation
- Cloudflare trust policy

## なぜ誤りか

公開されている vendor data であっても、それは operational data です。

Cloudflare IP range は公開されていますが、この repository が所有する data ではありません。時間とともに変わる可能性があります。application がその range を hardcode すると、この repository が vendor change を追跡し、list を正しく更新する責務を持つことになります。

その負荷を user の承認なしに持ち込んだことが問題でした。

また、config file がぱっと見で理解できなくなりました。local config file は local policy を明確に示すべきです。長い procedural logic や外部 list を隠す場所ではありません。

## 正しい方向

config は短く、分かりやすく保ちます。

この case では、local config は local admin policy だけを表すべきです。

```php
$admin_ips = [
	'<admin-ip>',
];

$remote_addr = $_SERVER['REMOTE_ADDR'] ?? '';
$cf_connecting_ip = $_SERVER['HTTP_CF_CONNECTING_IP'] ?? '';

$visitor_ip = $cf_connecting_ip ?: $remote_addr;

if( array_search($visitor_ip, $admin_ips, true) !== false ){
	$ip = $remote_addr;
}
```

spoofing protection や proxy trust validation が必要な場合は、user に確認したうえで、次のどれかを選びます。

- web server、firewall、hosting panel、Cloudflare Tunnel などで origin への direct access を制限する
- Cloudflare IP range を既に保守している trusted platform feature を使う
- 明確な update policy を持つ dedicated function / class / module を追加する
- deployment-managed process で vendor range を取得または更新する

vendor が保守する range を、config file に黙って埋め込んではいけません。

## 追加したガードレール

このミスを受けて、AGENTS に次の rule を追加しました。

- config file は短く、ぱっと見で分かる状態に保つ
- 長い procedural logic、外部 service data、大きな hardcoded list を config file に隠さない
- CDN、proxy、cloud、vendor の IP range など、外部で管理される変動 data を黙って hardcode しない
- 更新負荷や運用リスクを生む logic を追加する前に user に確認する

## 再発防止

外部 service data を code に追加する前に、次を確認します。

1. その data の更新責任者は誰か。
2. その data は時間とともに変わるか。
3. その data は application code、deployment configuration、infrastructure のどこに置くべきか。
4. 新しい maintenance obligation を作る前に user に確認したか。
5. local config file は local value と単純な policy choice に集中しているか。

実務上の rule は次です。

```text
公開 vendor data を、明示的な承認なしに application-owned hardcoded state にしない。
```

