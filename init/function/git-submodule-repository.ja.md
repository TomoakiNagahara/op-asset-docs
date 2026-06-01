# `GitSubmoduleRepository()` As-Is

## Overview

`asset/init/function/GitSubmoduleRepository.php` は current repository に optional remotes を追加します。

current repository に `origin` URL が無い場合は、何もせず return します。

request values で `local` と `ssh` のどちらも有効でない場合も、何もせず return します。

## Local Remote

`local=1` の場合、この function は current `origin` URL と requested `dir` value から local bare repository path を組み立てます。

local bare repository creation と local remote registration は `GitInitLocal()` に委譲します。

`git-init-local.ja.md` を参照してください。

## SSH Remote

`ssh=1` の場合、この function は次から SSH remote URL を組み立てます。

- `host`: default は `repo`
- `dir`: default は `~/repo`
- `origin` から導出した repository path

その後、`host` 名の remote を追加し、fetch します。

request value loading は `Request()` が担当します。`request.ja.md` を参照してください。
