# `GitSubmoduleRepository()` As-Is

## Overview

`asset/init/function/GitSubmoduleRepository.php` adds optional remotes to the current repository.

If the current repository has no `origin` URL, it returns without doing work.

If neither `local` nor `ssh` is enabled through request values, it returns without doing work.

## Local Remote

When `local=1`, the function derives a local bare repository path from the current `origin` URL and the requested `dir` value.

It delegates local bare repository creation and local remote registration to `GitInitLocal()`.

See `git-init-local.md`.

## Repository Path Conversion

The repository name taken from `origin` is used as the relative path below `dir`.

Path separator conversion is controlled explicitly by request values:

- `dot2slash=1` converts `.` to `/`.
- `hyphen2slash=1` converts `-` to `/`.

If an option is not enabled, its character is preserved. Neither conversion is applied by default.

The trailing `.git` suffix is excluded from conversion and remains `.git`.

For example, with both options enabled:

```text
www.example.com-2030.git
```

becomes:

```text
www/example/com/2030.git
```

The resulting relative path is shared by local and SSH remote generation. This keeps both remotes on the same repository path while leaving the separator policy under user control.

## SSH Remote

When `ssh=1`, the function builds an SSH remote URL from:

- `host`, defaulting to `repo`
- `dir`, defaulting to `~/repo`
- the repository path derived from `origin`

It then adds a remote named by `host` and fetches it.

Request value loading is handled by `Request()`. See `request.md`.
