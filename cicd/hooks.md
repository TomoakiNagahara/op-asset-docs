# Hooks and Integrated CI/CD

## Overview

One of the major characteristics of the ONEPIECE Framework is that CI/CD is integrated into the framework workflow.

The Git hooks are not treated as optional decoration. They are part of the operating model of the framework.

At the same time, this does not mean that CI/CD is a mandatory or exclusive framework requirement.

Developers and end users may choose to use this integrated workflow, but they are not forced to use only this implementation.

## Related Framework Documents

- `../op/invariants.md`
- `../op/responsibility-boundaries.md`
- `../op/common-recipes.md`

The main purpose is simple:

- code that has not passed the required CI checks must not be pushed
- repository rules should be enforced before `push`, not only after a remote CI job starts

For this reason, `asset/init/submodules.php` configures `core.hooksPath` for the main repository and for submodules.

## Why Hooks Are Enforced

The framework uses hooks to move CI/CD checks closer to the developer workflow.

This has several goals:

- prevent unverified code from being pushed
- keep repository state consistent across related repositories
- apply the same workflow to the main repository and submodules
- fail early on the developer machine instead of failing later on the remote side

In other words, the hook configuration is intentional. It is part of the framework design.

## Relationship Between Hooks and CI

The `pre-push` hook executes the CI entry script before the push is allowed to continue.

Typical behavior:

1. `git push` starts
2. the configured `pre-push` hook runs
3. the hook loads `ci.sh` or `.ci.sh`
4. the current branch and commit are checked against the CI result markers
5. if the expected CI state is not satisfied, the push is blocked

This means the framework does not rely only on remote CI. It also enforces CI expectations locally at push time.

## About `asset/config/init.php`

`asset/config/init.php` is developer-owned configuration.

It is not a framework constant that must be identical for every environment. It is the place where a developer defines local initialization preferences, such as:

- GitHub account name
- whether local repository remotes are used
- whether SSH remotes are used
- host name for repository access
- local repository path

Because of that, the values in `asset/config/init.php` should be understood as local operational settings.

## Branch Policy

### Branch `2030`

The branch name `2030` is the main branch for this repository set.

- it is treated as the standard working branch
- it is the branch expected to be pushed to `origin`
- CI marker files and related workflow assumptions are based on this branch model

### Branch `local`

The branch name `local` is reserved for local-only work.

- it is not intended to be pushed to `origin`
- it is treated differently by the CI/CD workflow
- parts of the CD flow explicitly skip processing when the current branch is `local`

This branch exists to support local development and local experiments without treating that branch as a normal delivery branch.

## Historical Background of `ci.sh` and `.ci.sh`

The earliest CI entry script name was:

- `ci.sh`

Later, this was changed in practice to:

- `.ci.sh`

The reason was not technical necessity alone.

The concern was that when users listed files in each directory, a visible `ci.sh` could cause confusion for people who use the developer's delivered repository contents but are not themselves framework developers.

Because of that history, the current hook behavior is:

1. first look for `ci.sh`
2. if `ci.sh` does not exist, then look for `.ci.sh`

This preserves compatibility with older repositories while allowing the hidden-dot convention in newer ones.

## [DOC-GAP] Current Side Effect

Because the hook prefers `ci.sh` first, a side effect exists in the current implementation:

- if an empty `ci.sh` file is placed in the repository
- the hook sources that file first
- the CI check does not actually run
- push can continue to the later checks without CI enforcement

This is part of the current As-Is behavior.

It is not described here as a stable intended specification.

## [DOC-FUTURE] Possible Change

The current compatibility-first behavior is not necessarily a permanent contract.

The empty-`ci.sh` bypass is a consequence of the current implementation, not a guaranteed long-term feature.

This means the behavior may be changed in the future.

## Historical Note About `cd.sh`

In an older generation, there was also:

- `cd.sh`

It is no longer used in the current workflow, but some older repositories may still contain it.

## Operational Intention

The intention of this design is not to make Git hooks “strict” for their own sake.

The intention is to keep the following contract:

- development can move quickly
- local and related repositories can stay aligned
- CI/CD behavior is built into daily operations
- unsafe or unchecked code should stop before it reaches `origin`

This is why the ONEPIECE Framework treats hooks as part of the framework workflow rather than as optional personal tooling.

## Optionality and Replaceability

The integrated CI/CD workflow is a standard framework operating model, not a mandatory universal lock-in.

That means:

- developers may use the built-in CI/CD workflow
- end users may use it if it fits their operation
- third-party CI/CD systems may also be used
- independently developed CI/CD systems may also be used

In other words, `op-unit-ci` is the standard integrated choice, but not the only permissible choice.

## [DOC-FUTURE] Ideal Responsibility Layout

The ideal long-term layout is to keep CI-related responsibilities concentrated in `op-unit-ci` and CD-related responsibilities concentrated in `op-unit-cd`.

In that ideal model:

- CI-related checks and orchestration belong to `op-unit-ci`
- CD-related publication and push policy belong to `op-unit-cd`

The current implementation still contains historically scattered parts, but the intended To-Be direction is clearer concentration by responsibility.
