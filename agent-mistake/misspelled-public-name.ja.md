# Agent Mistake: Misspelled Public Name

## Summary

AI agent は、spelling mistake の可能性が高い name を、意図確認せず public module file として作成しました。

agent が spelling issue に気づいたのは、実装後、その name がすでに module surface になった後でした。

## Root Cause

agent は、user の spelling を likely human typo として扱わず、正確な implementation requirement として扱いました。

これは誤った default です。

human user は、特に短い file name や API name で word を misspell することがあります。
requested name が typo に見え、その name が public または durable interface になる場合、agent は作成前に intended spelling を確認しなければいけません。

## Why This Is Dangerous

file name、method name、class name、config key、URL、template name、documented API は durable contract になります。

agent が typo を primary implementation name にしてしまうと、後から直す時に compatibility shim、redirect、documentation cleanup、breaking change が必要になる可能性があります。

実装前に短く確認する cost より、後から直す cost の方がずっと大きくなります。

## Correct Rule

[DOC-RISK] durable name になり得る spelling mistake を、無条件に維持してはいけません。

public または durable name を作る前に、それが standard English なのか、project established term なのかを確認してください。

誤りに見える場合は、実装前に user へ intended spelling を確認してください。

特に次の name では、この rule を守ります。

- file name
- class name
- method name
- function name
- config key
- template name
- route name
- URL parameter
- documentation heading

## Correct Handling

requested durable name が misspelled に見える場合、agent は編集前に次のように確認するべきです。

```text
requested name に spelling mistake が含まれている可能性があります。
standard spelling を使いますか、それとも requested spelling は意図したものですか？
```

misspelled name との backward compatibility が必要な場合は、明示的に扱います。

- primary implementation: standard spelling または確認済み spelling
- compatibility wrapper: misspelled legacy name
- documentation: legacy name は compatibility のためだけに存在すると説明する

## Prevention

新しい durable name を実装する前に、次を確認してください。

1. obvious typo がないか確認する。
2. existing project naming と比較する。
3. requested name が standard spelling と矛盾する場合は確認する。
4. unconfirmed spelling を primary name にしない。
5. typo を support する必要がある場合は、documented compatibility alias として扱う。
