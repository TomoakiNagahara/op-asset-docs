# Agent Mistake: CI Loader Contract Verification

## Summary

AI agent が、ONEPIECE Framework の CI contract ではなく、私的な verification harness に合わせて CI loader を変更しました。

このミスは、`asset/module/counter/InitGuidance.class.php` の CI 対応中に起きました。

agent は、framework を手動 bootstrap し、CI file を直接 include する isolated な `php -r` script で、新しい CI file を検証しようとしました。これは framework CI runner と同じ execution path ではありません。

その誤った検証に基づいて、agent は CI loader を既存の split CI layout:

```php
$name = basename(__FILE__);
$name = explode('.', $name)[0];
foreach( glob(__DIR__."/{$name}/*.php") as $path ){
	require_once($path);
}
```

から、`getcwd()` based lookup に変更しました。

これは誤りです。loader は local test harness ではなく、framework CI contract に従わなければいけません。

## Root Cause

agent は、自作の verification environment を authoritative なものとして扱いました。

framework には、すでに CI execution contract があります。

- class CI loader は split CI layout に従う
- loader file name は、同名の method directory に対応する
- `asset/unit/html/ci/Html.php` が reference pattern である
- CI execution は、期待される package context を設定する責任を持つ

agent は、この contract を source of truth として扱うべきでした。しかし、手動の `php -r` script の挙動に implementation を合わせようとしました。

## Why This Is Dangerous

verification code は、狭く、一時的で、不完全な場合があります。

production code や framework package code はそうではありません。

agent が private harness を満たすために production code や package code を変更すると、他 package が依存している framework convention を静かに壊す可能性があります。

今回の場合、UNIT / MODULE package 全体で使われる generic CI loader pattern を壊す危険がありました。

## Correct Verification Rule

framework CI behavior を検証する時に、isolated な execution path を勝手に作り、その実行結果に合わせて implementation を変更してはいけません。

CI loader work では、次を守ってください。

1. loader contract は `asset/unit/html/ci/Html.php` と揃える。
2. real `./cicd` entrypoint または framework CI runner path を優先する。
3. focused helper script が必要な場合は、framework CI contract を再現するために使い、再定義してはいけない。
4. focused helper と framework runner が食い違う場合、まず helper を疑う。
5. manual harness の current directory が異なるという理由で、package-local な `__DIR__` based lookup を `getcwd()` に置き換えてはいけない。

## Correct Pattern

確立済みの split CI loader pattern を使ってください。

```php
/* @var $ci \OP\UNIT\CI\CI_Config */
$ci = OP()->Unit()->CI()->Config();

$name = basename(__FILE__);
$name = explode('.', $name)[0];
foreach( glob(__DIR__."/{$name}/*.php") as $path ){
	require_once($path);
}

return $ci->Get();
```

loader の責任は、loader の隣にある同名 class directory から method CI file を読み込むことです。

## Prevention

CI loader code を変更する前に、次を確認してください。

1. `asset/docs/cicd/ci-file-layout.md` を読む。
2. `asset/unit/html/ci/Html.php` と比較する。
3. loader pattern を変更する前に、問題が class、namespace、`OP_CI`、CI config file name、method config のどこにあるか確認する。
4. isolated verification script は diagnostic aid としてだけ扱う。
5. 一時的な verification script を design authority にしてはいけない。

