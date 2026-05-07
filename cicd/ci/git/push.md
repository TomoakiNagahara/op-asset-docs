# Git Push from the CI Side

## Overview

In the ONEPIECE Framework, `git push` is protected by a local `pre-push` hook.

From the CI side, this means the CI system must leave behind a verifiable result that the hook can check before allowing a push.

The hook does not inspect test logs directly. Instead, it checks a CI marker file that records the commit ID that passed CI.

## CI Responsibility

CI is responsible for preparing the commit marker that proves a specific branch and PHP version have passed the required checks.

The marker file format is:

```text
.ci_commit_id_<branch>_php<version>
```

Examples:

```text
.ci_commit_id_2030_php83
.ci_commit_id_2030_php84
```

The file content is the commit ID that CI accepted for that branch and PHP version.

## How the Push Gate Uses CI Output

When a developer runs `git push`, the resolved CI script performs the following check:

1. detect the target branch
2. detect the effective PHP version
3. build the expected CI marker file name
4. read the commit ID stored in that file
5. read the current local branch commit ID
6. compare both values

If the file is missing, push is rejected.

If the values do not match, push is rejected.

## Operational Meaning

From the CI point of view, the marker file is the contract between CI and Git push control.

That contract is:

- CI passes for a specific commit
- the passed commit ID is written to the correct marker file
- a later `git push` is allowed only when the local branch still points to that same commit

If the developer adds a new commit after CI passed, the commit ID changes. In that case, the existing marker is no longer valid for push control, and a new CI pass is required.

## Branch and Version Scope

The CI marker is branch-specific and PHP-version-specific.

That means:

- one branch has its own marker file
- a different branch needs its own marker file
- a different PHP version needs its own marker file

This design prevents a CI result for one branch or one PHP version from being reused incorrectly for another case.

## Special Case: `local` Remote

If the push target is the `local` remote, the resolved CI script skips the enforcement and exits successfully.

In the current repository state, that script is `.ci.sh`, because no `ci.sh` is present.

This exception is intended for local repository handling and is outside the normal `origin` push gate.

The operational reason is not to weaken CI policy for public sharing, but to preserve a lightweight private workflow:

- fast local pushes
- offline history preservation
- temporary private storage for WIP or test commits before later cleanup

## Summary

From the CI side, the rule is straightforward:

- CI must produce or update the correct `.ci_commit_id_<branch>_php<version>` file
- that file must contain the exact commit ID that passed CI
- `git push` is permitted only when the developer's local branch commit matches that recorded CI result

This is how the ONEPIECE Framework connects CI state to push permission.
