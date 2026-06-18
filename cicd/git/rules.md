# Git Operation Rules

## Overview

The ONEPIECE Framework applies several Git operation rules through hooks and related workflow code.

These rules are intended to keep commit history, CI/CD state, and repository compatibility under control.

For the meanings and selection of change-type commit message prefixes, see `asset/docs/git/commit-message-prefix.md`.

## Rebase Rules

### Commit Message Prefix Check

During `git rebase`, commit messages are validated by the `pre-rebase` hook.

The beginning of each commit message must match an allowed prefix.

If the prefix is not allowed, the rebase is blocked.

### Prefix Whitelist Source

The main whitelist is defined in:

`asset/config/git-rebase-rules.php`

This file provides the allowed commit message prefixes.

### Additional Allowed Prefixes

In addition to the configured whitelist, the current branch name is also allowed automatically as a prefix.

For example, if the current branch is `2030`, the prefix:

```text
2030:
```

is allowed automatically.

### Year-Based Prefixes

Year-like prefixes are not generated dynamically by the hook.

Instead, they are allowed because they are explicitly listed in `asset/config/git-rebase-rules.php`.

So the actual behavior is:

- configured year prefixes are allowed
- the current branch name prefix is also allowed

## Push Rules

### Commit Message Prefix Check

During `git push`, commit messages are also validated.

The pushed commits must use allowed prefixes at the beginning of the commit message.

If an unapproved prefix is found, the push is blocked.

The whitelist source is the same:

`asset/config/git-rebase-rules.php`

The current branch name is also added automatically as an allowed prefix.

### CI Requirement

Before a push is accepted, the hook runs `ci.sh` or `.ci.sh`.

The purpose of this script is to verify that the branch being pushed has already passed CI.

If CI has not passed, the push is blocked.

The check is based on comparing:

- the current commit ID of the branch
- the stored CI-approved commit ID

### Exception for `local` Remote

If the remote name is `local`, the CI check is skipped.

That means a push to `local` is allowed even if the branch has not passed CI.

However, the commit message prefix check still applies.

### Why the `local` Remote Skips CI

The `local` remote exception exists for practical workflow reasons.

1. Pushing to GitHub is slower.
   Even if the difference is only a few seconds, a push to a local repository is much faster.
2. A local push can still be used when the internet is unavailable.
   This allows history to be saved even when the developer is not working in an office, at home, or in another stable online environment.
3. It supports trial commits.
   Developers can push work-in-progress commits, temporary test code, and intermediate states to a private local repository, then clean them up before pushing to a shared or public repository.

## Additional Push Restriction for GitHub

There is another push restriction outside the hook prefix and CI checks.

In:

`asset/unit/cd/function/isCanPushToGithub.php`

the framework defines which branch names may be pushed to GitHub.

### Allowed Branches for GitHub Push

A push to GitHub is allowed when one of the following is true:

- the branch is equal to `_OP_APP_BRANCH_`
- the branch name matches a year-like pattern such as `2025`, `2026`, `2030`, `2031`
- the branch name is explicitly allowed in CD configuration

In practical terms, if a developer wants to allow an additional branch name, one standard way is:

1. copy `asset/unit/cd/config.php`
2. create `asset/config/cd.php`
3. add the branch name to the allowed `branch` list

After that, the branch can be pushed under the current CD rule set.

If the push target is not GitHub, this GitHub-specific branch restriction is not applied.

The intent is that a non-GitHub remote is treated as a private repository area.

Under that assumption, an accidental push is considered less problematic than an accidental publication to GitHub.

If the push target is GitHub and none of the allowed branch conditions match, the push is blocked.

This GitHub-specific branch restriction is separate from the commit message prefix rule and separate from the CI check.

In that sense, the ONEPIECE Framework can also be seen as partially vendor-locked to GitHub, because the stricter publication gate is designed specifically around GitHub pushes.

### Purpose of Branch-Based Push Blocking

The purpose of this branch-based push restriction is to prevent unintended code from being pushed.

In particular, it is meant to prevent pushing:

- work-in-progress code
- experimental code
- research branches that are not ready for the shared remote history

This helps reduce:

- history pollution
- unnecessary conflicts
- accidental exposure of incomplete work to shared GitHub repositories

It also helps prevent work-in-progress commit messages such as:

- `WIP: ALL`

from being pushed to a public repository before the work is ready to be shared.

## About `hook-the-hooks.sh`

The framework enforces its own hooks through `core.hooksPath`.

Without additional handling, that would make user-defined hooks difficult to use.

The purpose of `hook-the-hooks.sh` is to solve that problem.

It allows the framework hook to call:

- a user-defined local hook in `.git/hooks/`
- a user-defined global hook from the global `core.hooksPath`

This means the ONEPIECE Framework can enforce its own hooks while still allowing user-defined hooks to run.

## Other Rules Found

### Ignore Rules for Private Local Files

The repository `.gitignore` contains these broad patterns:

- `.*`
- `_*`

Historically, this ignore behavior came first.

It existed before the config override convention based on underscore-prefixed files.

This is also part of the operational technique of the ONEPIECE Framework.

The intent is to make dot-prefixed files and underscore-prefixed files local by default, so they do not get included in normal `git add .` workflows.

This helps reduce accidental publication of local-only files to:

- production deployment flows
- shared repositories
- public GitHub repositories

As a practical operating rule, if a developer has a file or directory that should stay local and should not normally be stored in the repository, giving it an underscore-prefixed name is a good default choice.

This helps reduce the tragedy of accidentally committing, pushing, and publishing something to the world that was never meant to leave the local environment.

Typical examples include local override files such as:

- `_admin.php`
- `_database.php`
- `_php.php`

Those config override files should be understood as a later feature that was designed to ride on top of the pre-existing underscore-ignore behavior.

These files may still be forced into Git with `git add -f`, so this is a guardrail rather than an absolute protection mechanism.

### Advice About Hook Bypass

If a Git hook blocks an operation because of a rule violation, Git itself still provides bypass options in some cases.

For example:

- `git commit -n`
- `git commit --no-verify`
- `git push --no-verify`

These options allow the operation to continue without running the normal verification hook.

In practical terms:

- `git commit -n` or `git commit --no-verify` can bypass `pre-commit`
- `git push --no-verify` can bypass `pre-push`

In the context of the ONEPIECE Framework, this should be treated as an emergency or exceptional escape hatch, not as the normal workflow.

The purpose of documenting this is practical awareness:

- hooks are strong guardrails
- but they are not an absolute sandbox
- an operator can still choose to bypass them intentionally

### Deny Word Support

Both rebase and push prefix validation also support a deny-word list.

The deny list is currently empty in the config, but the mechanism exists.

### Pre-commit Restrictions

The `pre-commit` hook checks staged `php`, `phtml`, and `html` changes.

It blocks commits if added lines contain forbidden patterns such as:

- `var_dump`
- `print_r`
- `exit;`
- `$_GET`
- `$_POST`
- `$_REQUEST`
- `$_COOKIE`
- `$_SESSION`
- `$_SERVER`

## Summary

The current implementation enforces the following:

- rebase checks commit message prefixes
- push checks commit message prefixes
- push requires CI unless the remote is `local`
- GitHub push is restricted by branch rules in CD logic
- framework hooks still allow user-defined hooks through `hook-the-hooks.sh`
- dot-prefixed and underscore-prefixed files are ignored by default as part of local-override operations

The only point that is different from a simplified verbal summary is this:

- year prefixes are not generated automatically as a “current year or below” rule
- they are allowed because they are explicitly listed in the config, plus the current branch prefix is added automatically
