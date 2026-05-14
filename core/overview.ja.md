# OP-CORE Overview

Source folder: `asset/core/`

OP-CORE はこの Skeleton で使われている framework core だが、この Skeleton 専用ではない。

OP 公式 Skeleton は、OP-CORE を利用する application の一つである。third-party application も、OP-CORE を直接、または独自の skeleton を通して利用する可能性がある。

OP-CORE は shared framework core であるため、その behavior をこの repository だけに向けたものとして説明しない。core behavior、contract、test、documentation は、OP 公式 application と external application のどちらにも通用する前提で書く。

## Folder Model

`asset/core/` は submodule folder に分割されている。各 folder は core responsibility の種類ごとにまとまっている:

- `ci/`: core behavior の CI-facing entry point。
- `class/`: named runtime core class。
- `docs/`: op-core 自体の package-owned documentation。
- `function/`: standalone global helper function。
- `include/`: startup と request の include file。
- `interface/`: unit、module、service、integration の framework contract。
- `testcase/`: runnable または inspectable core test case。
- `trait/`: core class に mix in される shared implementation。
- `tutorial/`: core behavior を学ぶための runnable または renderable example。

## Documentation Model

overview-level context はこの file に置く。

folder-specific definition は `class.md`、`function.md`、`interface.md` など、各 folder document に置く。

分離された class、trait、function、include file、interface、testcase、tutorial page の 1 file に依存する documentation は、overview や shared folder document にまとめず、その file name の document を作成する。

これにより、OP の file-level commit model が明確になる。feature file、対応する test または CI file、same-named documentation を一緒に動かせるため、rebase や cherry-pick の conflict を減らせる。
