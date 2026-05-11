# `GitInitLocal()` As-Is

## Overview

`asset/init/function/GitInitLocal.php` prepares the local bare repository used by the skeleton initialization workflow.

It is called from `GitSubmoduleRepository()` when the init request enables the local repository path:

```text
local=1
```

## Responsibility

`GitInitLocal()` owns the local bare repository setup and the local remote/fetch behavior.

This keeps the related local-repository operations in one file:

- create the local repository directory when it does not exist
- initialize that path as a bare Git repository
- add the current repository's `local` remote
- fetch from the `local` remote when the local bare repository already exists

The local remote/fetch behavior used to live in `GitSubmoduleRepository()`.
It was moved into `GitInitLocal()` so that newly-created bare repositories are not immediately fetched before they can contain refs.

## Current Behavior

When the target path does not exist:

1. `GitInitLocal()` creates the directory.
2. It runs `git init --bare <path>`.
3. If bare initialization succeeds, it adds the current repository's `local` remote pointing to that path.
4. It does not fetch from the newly-created bare repository.

When the target path already exists:

1. `GitInitLocal()` checks for `<path>/HEAD`.
2. If `HEAD` is missing, it returns `false`.
3. If `HEAD` exists, it adds the current repository's `local` remote pointing to that path.
4. If the remote is added successfully, it fetches from `local`.

The function returns the result of the last Git operation it runs.

## Caller Relationship

`GitSubmoduleRepository()` is responsible for deriving the local bare repository path from the current repository's `origin` URL.

After deriving that path, it calls:

```php
GitInitLocal($local)
```

`GitSubmoduleRepository()` no longer owns the local remote/fetch sequence directly.
That behavior belongs to `GitInitLocal()`.

## Reason For The Move

The local remote/fetch sequence was moved because fetching from a bare repository immediately after creating it can fail.

Keeping local bare repository creation, local remote registration, and local fetch behavior in `GitInitLocal()` also makes maintenance easier because the local-repository behavior has one owner.

## Compatibility Note

The previous implementation is intentionally kept as commented code in the source file for smoother `git diff`, rebase, and pick workflows.
Do not remove it casually during nearby edits.

