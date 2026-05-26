# CI Purpose

この document は、ONEPIECE Framework における CI の目的を記録します。

CI は単なる syntax check ではありません。主な目的は、共有される behavior contract が壊れていないことを機械的に確認することです。

## Class contract

ONEPIECE Framework では、class file が特に重要です。CORE、UNIT、MODULE の class は、他の code から再利用される境界になるためです。

class-based CI flow は `*.class.php` file を見つけ、`OP_CI` を要求し、`CI_AllMethods()` と `CI_Inspection()` を使って behavior を検査します。

つまり CI は、class が caller に提供する contract を確認しています。

- 期待される input
- 期待される output
- 変更後も安定しているべき behavior

## Responsibility boundary

CI は responsibility boundary も明確にします。

現在の framework CI は、すべての function や template を無差別に検査するものではありません。class-based contract に集中することで、各 package が次のことを示せるようにしています。

- この class は、この package が責任を持つ behavior の一部である
- この class は、検査対象 contract を安定させる必要がある
- この package は、`CI_AllMethods()` が返す class method に CI 上の責任を持つ

そのため、`OP_CI` は単なる技術的な要求ではありません。class が framework CI contract に参加するための入口です。

## Mechanical regression detection

CI は、人間の記憶ではなく機械によって regression を検出するために存在します。

変更によって期待される class behavior が壊れた場合、CI は壊れた method と期待値を指し示すべきです。そうすることで failure は actionable で repeatable になります。

## Non-CI helper

[DOC-ISSUE] rare recovery や error handling のためだけに存在する helper class でも、package root や走査対象の class directory に visible な `*.class.php` file として置かれている場合、CI に発見されます。

この場合、考えるべきことは CI を通す方法だけではありません。設計上の問いは次です。

- この class は CI-inspected contract の一部なのか
- それとも、この class は意図的に CI target boundary の外側なのか

ONEPIECE Framework では、environment-dependent code を安易に CI 対象外にするべきではありません。

framework には、CI inspection 中かどうかを知るための標準手段として `OP()->isCI()` がすでにあります。method が runtime environment の値に依存する場合、その依存を小さな method に分け、`OP()->isCI()` が true の間は deterministic な固定値を返してください。

例:

```php
function GetPosixUid()
{
	return OP()->isCI() ? 1000 : posix_geteuid();
}
```

この pattern は、deterministic な database record を必要とする CI など、既存の framework unit でも使われています。framework 自身が CI-mode branch を提供できる場合、外部 stub class の代替になります。

したがって、visible な `*.class.php` helper では、次の順序を優先してください。

1. refactor で壊れ得る package behavior の一部なら、class を CI 対象に保つ
2. environment read を小さな method に隔離する
3. CI 中は `OP()->isCI()` で deterministic な値を返す
4. `OP_CI`、`CI_AllMethods()`、method-level CI config で安定した contract を検査する

`OP_CI` を追加して空の `CI_AllMethods()` を返す対応は、一時的な橋渡しであり、望ましい最終設計ではありません。

CI が収集しない placement または filename を選ぶのは、その code が package behavior contract の一部ではないと明示できる場合に限ります。その判断は package の近くに document 化してください。
