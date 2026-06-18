# `GitSubmoduleRepository()` As-Is

## Overview

`asset/init/function/GitSubmoduleRepository.php` は current repository に optional remotes を追加します。

current repository に `origin` URL が無い場合は、何もせず return します。

request values で `local` と `ssh` のどちらも有効でない場合も、何もせず return します。

## Local Remote

`local=1` の場合、この function は current `origin` URL と requested `dir` value から local bare repository path を組み立てます。

local bare repository creation と local remote registration は `GitInitLocal()` に委譲します。

`git-init-local.ja.md` を参照してください。

## Repository Path Conversion

`origin` から取り出した repository name は、`dir` 配下の relative path として使われます。

path separator の変換は、次の request value で明示的に選択します。

- `dot2slash=1` は `.` を `/` に変換します。
- `hyphen2slash=1` は `-` を `/` に変換します。

option が有効でない場合、その文字は維持されます。default では、どちらの変換も行いません。

末尾の `.git` suffix は変換対象から除外され、`.git` のまま維持されます。

たとえば、両方の option を有効にした場合:

```text
www.example.com-2030.git
```

は次になります。

```text
www/example/com/2030.git
```

変換後の relative path は、local remote と SSH remote の生成で共通して使われます。これにより両 remote の repository path を同じに保ちながら、separator policy は user が選択できます。

## SSH Remote

`ssh=1` の場合、この function は次から SSH remote URL を組み立てます。

- `host`: default は `repo`
- `dir`: default は `~/repo`
- `origin` から導出した repository path

その後、`host` 名の remote を追加し、fetch します。

request value loading は `Request()` が担当します。`request.ja.md` を参照してください。
