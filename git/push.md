# Git Push from the Developer Side

## Overview

When a developer runs `git push`, the ONEPIECE Framework executes a Git `pre-push` hook before the push is allowed to continue.

This is not optional behavior in the normal framework workflow. It is part of the integrated CI/CD design.

## What Happens During `git push`

The push flow is:

1. the developer runs `git push`
2. Git executes the configured `pre-push` hook
3. the hook looks for `ci.sh` first, then `.ci.sh`
4. the hook executes the resolved CI script
5. if the script exits with a non-zero status, the push is blocked
6. if the script succeeds, the push can continue

In the current repository state, `.ci.sh` is a symbolic link to `asset/unit/ci/.ci.sh`, and no `ci.sh` is present.

That means `.ci.sh` is the CI script that is normally resolved here.

## Purpose of the CI Script

The purpose of the resolved CI script is to verify that the current repository state has already passed CI.

It does this by comparing:

- the current commit ID of the branch being pushed
- the saved commit ID recorded by CI

If those values do not match, the push is rejected.

## How the Check Works

Under the current repository state, `.ci.sh` determines the current branch and the active PHP version, then builds a file name in this format:

```text
.ci_commit_id_<branch>_php<version>
```

Examples:

```text
.ci_commit_id_2030_php83
.ci_commit_id_local_php84
```

The script then:

1. checks whether the CI marker file exists
2. reads the commit ID stored in that file
3. reads the current commit ID of the branch being pushed
4. compares both values

If the marker file does not exist, the push is blocked.

If the commit IDs do not match, the push is blocked.

## Meaning of a Successful Push

A successful push means the local hook accepted the current branch state as matching the expected CI result marker for that branch and PHP version.

From the developer point of view, the rule is:

- if CI has not passed for the current commit, do not push
- if the saved CI commit ID does not match the current branch commit, do not push

## Special Case: `local` Remote

If the push target remote is `local`, the resolved CI script exits successfully without enforcing the CI check.

This is intended for local repository workflows and does not represent a normal push to `origin`.

The background of this exception is practical:

- a local push is faster than a GitHub push
- local history can still be saved while offline
- developers can use a local repository for trial commits, WIP commits, and temporary test code before cleaning history for shared publication

## Summary

From the side of the person running `git push`, the behavior is simple:

- pushing runs the `pre-push` hook
- the hook runs the resolved CI script
- in the current repository state, that script is `.ci.sh`
- the CI script checks whether the current branch commit is the commit that already passed CI
- if not, the push is rejected

This is how the ONEPIECE Framework prevents unchecked code from being pushed in the standard workflow.
