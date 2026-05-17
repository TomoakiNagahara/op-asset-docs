# Agent Mistake: Hardcoded External IP Ranges in Config

## Summary

An AI agent added a long list of Cloudflare IP ranges directly into a local admin config file while trying to fix admin detection behind Cloudflare.

That was the wrong direction.

The user pointed out three problems:

- IP ranges owned by an external service can be added, removed, or changed.
- Putting that list in application config creates maintenance burden and operational risk.
- The agent added this logic without asking the user, even though it changed the maintenance model of the application.

## What Happened

The original task was to make admin detection work when requests pass through Cloudflare.

The framework's `isAdmin()` behavior compares:

- `REMOTE_ADDR`
- the configured admin IP value

When Cloudflare is in front of the origin, `REMOTE_ADDR` may be the proxy IP, while the visitor IP is available through `HTTP_CF_CONNECTING_IP`.

The agent tried to guard against header spoofing by hardcoding Cloudflare's published IPv4 and IPv6 ranges in `asset/config/_admin.php`.

That made the config file too long and mixed several responsibilities:

- local admin IP configuration
- external vendor IP range data
- CIDR matching implementation
- Cloudflare trust policy

## Why This Was Wrong

Published vendor data is still operational data.

Cloudflare IP ranges are public, but they are not owned by this repository. They can change over time. If the application hardcodes those ranges, the repository now owns the burden of tracking vendor changes and updating the list correctly.

That burden was introduced without user approval.

The change also made the config file hard to understand at a glance. A local config file should make the local policy obvious. It should not hide long procedural logic or large external lists.

## Correct Direction

Keep config short and obvious.

For this case, the local config should express only the local admin policy, for example:

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

If spoofing protection or proxy trust validation is required, choose one of these directions only after confirming with the user:

- enforce Cloudflare-only origin access in the web server, firewall, hosting panel, or Cloudflare Tunnel
- use a trusted platform feature that already maintains Cloudflare IP ranges
- add a dedicated function/class/module with an explicit update policy
- fetch or update vendor ranges through a deployment-managed process

Do not silently embed vendor-maintained ranges inside a config file.

## Added Guardrails

This mistake led to new AGENTS rules:

- keep config files short and immediately readable
- do not hide long procedural logic, external service data, or large hardcoded lists in config files
- do not silently hardcode externally maintained data such as CDN, proxy, cloud, or vendor IP ranges
- ask the user before adding logic that creates update burden or operational risk

## Prevention

Before adding external service data to code:

1. Ask who owns updates to that data.
2. Check whether the data changes over time.
3. Identify whether the data belongs in application code, deployment configuration, or infrastructure.
4. Ask the user before creating a new maintenance obligation.
5. Keep local config files focused on local values and simple policy choices.

The practical rule is:

```text
Do not turn public vendor data into application-owned hardcoded state without explicit approval.
```

