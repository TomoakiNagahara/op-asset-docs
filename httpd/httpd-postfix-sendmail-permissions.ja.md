# HTTPD と Postfix sendmail の権限

## 対象範囲

この文書は、HTTPD の systemd service と Postfix の `postdrop` helper の
組み合わせによって発生したメール投入タイムアウトについて説明します。

application は PHP の `mail()` 関数を呼び出していました。PHP は続いて、
設定されている Postfix 互換コマンドを実行します。

```text
/usr/bin/sendmail -t -i
```

問題は、Postfix が外部 SMTP relay への配送を試みる前に発生していました。

## 症状

[DOC-ISSUE] フォーム送信処理が待機し続け、Web request がタイムアウトしました。

対象 process は次の状態で残っていました。

```text
httpd -> sendmail -> postdrop
```

system journal には次のエラーが繰り返し記録されていました。

```text
postfix/postdrop: warning: mail_queue_enter: create file maildrop/...: Permission denied
```

`postdrop` は10秒ごとに処理を再試行するため、PHP の同期的な `mail()` 呼び出しが
終了しませんでした。

## 原因

Postfix は `/usr/bin/postdrop` を `postdrop` group 用の setgid helper として
インストールします。この権限は、次のディレクトリ内に queue file を作成するために
必要です。

```text
/var/spool/postfix/maildrop
```

HTTPD の systemd unit では、次の hardening option が有効でした。

```ini
[Service]
NoNewPrivileges=on
```

`NoNewPrivileges=on` により、HTTPD child process が setgid helper を実行しても
`postdrop` group 権限を取得できませんでした。そのため `postdrop` は Web server
group のまま動作し、Postfix maildrop directory に書き込めませんでした。

このタイムアウトには外部 SMTP relay は関与していません。メールはまだローカルの
Postfix queue にも投入されていませんでした。

## 解決方法

HTTPD service に systemd drop-in を追加します。

```ini
# /etc/systemd/system/httpd.service.d/postfix-sendmail.conf
[Service]
NoNewPrivileges=no
```

その後、systemd を再読み込みして HTTPD を再起動します。

```sh
systemctl daemon-reload
systemctl restart httpd
```

これにより、Postfix 標準の setgid `postdrop` を利用するメール投入経路に戻ります。

代替策として `/var/spool/postfix/maildrop` を world-writable にしたり、Web server
account を特権 group に追加したりしないでください。service restriction 自体を
修正する必要があります。

## 確認方法

HTTPD の実効設定を確認します。

```sh
systemctl show httpd -p NoNewPrivileges
```

期待する結果は次の通りです。

```text
NoNewPrivileges=no
```

停止した `sendmail` または `postdrop` process が残っていないことを確認します。

```sh
ps -eo user,pid,ppid,etime,stat,args | grep -E 'sendmail|postdrop'
```

Postfix がメールを受け付け、queue が停止していないことを確認します。

```sh
postqueue -p
journalctl -u postfix --since today
```

application request は短時間で終了し、journal には `maildrop` の permission error
ではなく、`pickup` が発行した queue ID が記録される必要があります。

## セキュリティ上のトレードオフ

`NoNewPrivileges` を無効にすると、HTTPD service の hardening が1つ減ります。
一方、PHP の Postfix sendmail integration は Postfix の setgid helper 実行に
依存します。

`NoNewPrivileges=on` を維持する必要がある環境では、この sendmail 経路を使用せず、
setgid 実行に依存しない独立したメール投入 service または認証付き SMTP client を
設計してください。
