# HTTPD and Postfix Sendmail Permissions

## Scope

This document describes a mail submission timeout caused by the interaction
between an HTTPD systemd service and the Postfix `postdrop` helper.

The application called PHP's `mail()` function. PHP then executed the configured
Postfix-compatible command:

```text
/usr/bin/sendmail -t -i
```

The problem occurred before Postfix attempted delivery to an external SMTP
relay.

## Symptoms

[DOC-ISSUE] A form submission waited until the web request timed out.

The affected processes remained active as:

```text
httpd -> sendmail -> postdrop
```

The system journal repeatedly reported:

```text
postfix/postdrop: warning: mail_queue_enter: create file maildrop/...: Permission denied
```

`postdrop` retried the operation every ten seconds, so PHP's synchronous
`mail()` call did not return.

## Cause

Postfix installs `/usr/bin/postdrop` as a setgid helper for the `postdrop`
group. This privilege is required to create queue files under:

```text
/var/spool/postfix/maildrop
```

The HTTPD systemd unit had this hardening option enabled:

```ini
[Service]
NoNewPrivileges=on
```

`NoNewPrivileges=on` prevented the HTTPD child process from gaining the
`postdrop` group privilege when it executed the setgid helper. `postdrop`
therefore continued to run with the web-server group and could not write to the
Postfix maildrop directory.

The external SMTP relay was not involved in this timeout. The message had not
yet entered the local Postfix queue.

## Resolution

Add a systemd drop-in for the HTTPD service:

```ini
# /etc/systemd/system/httpd.service.d/postfix-sendmail.conf
[Service]
NoNewPrivileges=no
```

Then reload systemd and restart HTTPD:

```sh
systemctl daemon-reload
systemctl restart httpd
```

This restores the standard Postfix setgid `postdrop` submission path.

Do not make `/var/spool/postfix/maildrop` world-writable and do not add the web
server account to privileged groups as a substitute for fixing the service
restriction.

## Verification

Confirm the effective HTTPD setting:

```sh
systemctl show httpd -p NoNewPrivileges
```

The expected result is:

```text
NoNewPrivileges=no
```

Confirm that no `sendmail` or `postdrop` processes remain blocked:

```sh
ps -eo user,pid,ppid,etime,stat,args | grep -E 'sendmail|postdrop'
```

Confirm that Postfix accepts the message and that the queue is not stuck:

```sh
postqueue -p
journalctl -u postfix --since today
```

The application request should return promptly, and the journal should show a
queue ID from `pickup` instead of repeated `maildrop` permission errors.

## Security Tradeoff

Disabling `NoNewPrivileges` reduces one HTTPD service hardening measure.
However, PHP's Postfix sendmail integration depends on executing Postfix's
setgid helper.

Deployments that must retain `NoNewPrivileges=on` should avoid this sendmail
path and use a separately designed mail submission service or authenticated
SMTP client whose permissions do not depend on setgid execution.
