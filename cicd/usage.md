# CI/CD Usage

## Overview

The ONEPIECE Framework provides an executable `cicd` file at the framework root.

That means CI/CD can be started with:

```sh
./cicd
```

This is the standard integrated workflow, but not a mandatory universal requirement.

Developers and end users may adopt it, but they may also choose third-party or independently developed CI/CD systems.

For the intended ONEPIECE CI/CD concept and terminology boundary, see `concept.md`.

## Standard Execution

The normal usage is:

```sh
./cicd
```

This runs CI against the currently committed repository state.

[DOC-GAP] The current `cicd` implementation may continue to CD after CI succeeds and dry-run is not active. That is valid only if CI has already returned whole-application success. If CD begins before the full CI run has completed, that behavior is abnormal and violates the flow invariant described in `concept.md`.

## Common Options

Common operator-facing options include:

- `ci=1` or `ci=0`
- `cd=1` or `cd=0`
- `unit=core`
- `unit=<unit-name>`

## Unit-Scoped Execution

If the operator specifies:

```sh
./cicd unit=core
```

or:

```sh
./cicd unit=<unit-name>
```

the current implementation automatically turns the run into dry-run behavior.

That means unit-focused execution implicitly behaves like `test=1`.

In practice:

- `unit=core` is the way to test only op-core
- `unit=<unit-name>` is the way to test only the specified unit
- both automatically become dry-run style execution

## Class-Scoped Execution

If the operator wants to test only a specific class in a unit, the operator can use:

```sh
./cicd unit=app class=App
```

This tests only the `App` class in `op-unit-app`.

## Method-Scoped Execution

If the operator wants to test only a specific method of that class, the operator can use:

```sh
./cicd unit=app class=App method=Title
```

This tests only the `Title` method of the `App` class.

## CI File Layout

UNIT and MODULE class CI files should use the split CI file layout described in `ci-file-layout.md`.

In short:

- `<ClassName>.class.php` is the class target.
- `ci/<ClassName>.php` is the CI loader.
- `ci/<ClassName>/<MethodName>.php` contains method-level cases.

## Why Uncommitted Changes Disappear Temporarily

Before CI runs, `op-unit-ci` automatically performs `git stash save` across the repository set.

That means uncommitted changes temporarily disappear from the working tree while CI is running.

This behavior exists because the standard CI flow is intended to inspect the committed state, not the local dirty working tree.

## Testing Uncommitted Changes

If the operator wants to test uncommitted code, for example while investigating why CI fails, the operator can use:

```sh
./cicd test=1
```

or:

```sh
./cicd dry-run=1
```

In that mode:

- uncommitted changes remain visible and testable
- the run behaves as dry-run
- CI-approved commit ID markers are not saved

The same dry-run behavior is also entered automatically when `unit=...` is used.

## Codex Sandbox Investigation

[DOC-RISK]

When an agent investigates `./cicd` from the Codex sandbox, the full command result is not always authoritative.

The sandbox can change the result before the framework reaches the actual application or package failure. Known examples include:

- Git index writes failing when the CI pre-step tries to stash or stage repository state
- local mail checks failing because the sandbox cannot execute the configured sendmail path

These are sandbox environment artifacts, not necessarily framework regressions.

For GitHub Actions failures, agents should treat the GitHub Actions log as the authoritative failure source. Use local `./cicd` runs only to reproduce the specific failing condition after matching the relevant environment as closely as possible, for example PHP version, `GITHUB_ACTIONS=true`, and focused `unit=...` / `class=...` / `method=...` options.

Do not spend investigation context repeatedly explaining sandbox-only `index.lock`, stash, or sendmail failures unless the user is specifically asking about those sandbox failures.

## Operational Meaning

The practical distinction is:

- `./cicd`
  inspect the committed state and record CI-approved commit IDs
- `./cicd test=1`
  inspect the current working tree for investigation, without recording CI-approved commit IDs

This makes standard CI strict, while still leaving a practical debugging path for developers.
