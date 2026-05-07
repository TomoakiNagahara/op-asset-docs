# `isAdmin()`

## Overview

`OP()->isAdmin()` is one of the key environment decisions in the ONEPIECE Framework.

It controls whether the current request should be treated as administrator access.

This affects behavior such as:

- debug output through `D()`
- on-screen error display
- developer-oriented notice rendering

## Main Behavior

In normal application use, `isAdmin()` is controlled by:

- localhost access
- the admin IP configured in `asset/config/admin.php`

That means the framework gives developers a very low-friction way to enter developer mode in a local environment.

## Why Localhost Is Always Admin

The purpose of treating localhost as admin is convenience.

It allows:

- immediate debugging without extra setup
- use of `D()` and error output in local development
- smooth first use of the framework

This is a deliberate design choice for development productivity.

## [DOC-RISK] Cautions

There are some cautions around the localhost rule.

- in unusual environments, `REMOTE_ADDR` may appear as localhost more broadly than expected
- reverse proxies, containers, VMs, or remote development setups may blur the meaning of localhost
- local behavior may differ from production behavior because localhost is automatically privileged

## Practical Risk Assessment

Even so, this is usually not a silent risk.

If `isAdmin()` becomes unexpectedly `true`, the effect is often highly visible because:

- `D()` output appears
- error details may be shown on screen
- admin-oriented behavior becomes obvious quickly

That means developers and administrators are likely to notice the issue early.

In that sense, this is usually a visible misclassification rather than a hidden long-term failure.

However, visibility does not make it harmless.

If the request is publicly reachable, a third party may see debug-oriented behavior before the developer notices it.

## Operational Guidance

The current design is practical for local development, but it should be understood clearly.

- localhost auto-admin is a convenience feature
- unusual environments should be checked explicitly
- production-like environments should confirm admin behavior intentionally

## [DOC-FUTURE] Planned Direction

There is an intention to make the localhost auto-admin behavior configurable in the future.

In other words, the framework may later allow application settings to decide whether localhost should always be treated as admin.

The current behavior is fixed for convenience, but it is not necessarily intended to remain permanently hardcoded.
