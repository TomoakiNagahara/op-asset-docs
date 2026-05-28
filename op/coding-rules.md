# Coding Rules

## Purpose

This document records framework-wide coding rules for ONEPIECE Framework repositories.

These rules apply to application code, CORE changes, UNIT packages, MODULE packages, templates, configuration, scripts, and documentation-related code.

## Public And Durable Names

File names, class names, method names, function names, config keys, request keys, URLs, template entry names, command names, and documented APIs can become durable public surfaces.

[DOC-RISK] When a requested public or durable name appears to contain a spelling mistake, do not blindly copy it into code.
Human users can mistype words.
Pause and confirm the intended spelling before creating or exposing the name.

This is a framework-wide coding rule, not a UNIT/MODULE-only rule.

If compatibility with the misspelled name is required later, implement it as an explicit compatibility alias, wrapper, redirect, or migration path.
Do not let an accidental typo become the primary implementation name.

## Comments

Write code comments in English.

Use comments to clarify non-obvious behavior, intent, assumptions, constraints, or risks.
Avoid comments that only restate what the code already says.

## Method Closing Comments

Always add the method name as a comment on a method's closing brace.

Use the exact method name casing:

```php
class Foo
{
	function Bar()
	{

	} // Bar
}
```

This is required because Git diffs are not always smart about method boundaries.
When a method body changes, Git can include the neighboring method's closing brace in the diff even when that neighboring method was not changed.
That unnecessary diff noise can become a major source of conflicts during rebase and merge.

The closing comment gives Git and human reviewers a stable boundary marker and makes unrelated neighboring methods less likely to be pulled into a change.

## Singleton Local Static Variables

When a method keeps a lightweight singleton instance in a local static variable, initialize it with an explicit `if` block and return the variable afterward.

```php
class Foo
{
	function Common() : Common
	{
		static $common;

		if(!$common ){
			$common = new Common();
		}

		return $common;
	} // Common
}
```

Do not use a compact return expression such as `return $common ??= new Common();` for this pattern.
Some IDEs, including Eclipse, can warn that the local static variable is unused when initialization and return are merged into one expression.
The explicit form keeps the singleton intent clear and avoids noisy IDE warnings.

## Configuration

Keep configuration files short and immediately readable.

Do not hide long procedural logic, external service data, or large hardcoded lists in config files.
Move that behavior to an owned function, class, UNIT, MODULE, or web-server/deployment setting.

Treat readable config defaults as coding manners.
Default config values should be understandable to third-party users without reading documentation or asking an AI assistant.
When a config key accepts a small set of meaningful values, list or demonstrate valid values in comments, and explain the value's meaning and runtime effect directly in the config file.

## Debugging

Do not use `var_dump()` or `print_r()` for debugging.

Use `D()` for framework-aware debug output.
