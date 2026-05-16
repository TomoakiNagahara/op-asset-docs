# `OP()` Philosophy

## Overview

`OP()` is one of the most frequently used entry points in practical ONEPIECE Framework development.

It is designed to let developers call framework features from a single unified access point.

## Background

The background of `OP()` is practical developer ergonomics.

Without a unified entry point, calling core functions or unit functions across different namespaces becomes noisy and repetitive.

Developers would need to care more about:

- current class namespace
- target class namespace
- fully-qualified class names
- repeated static or object access patterns

## Purpose

The purpose of `OP()` is to make framework usage simple and uniform.

With `OP()`, developers can call framework features without having to think about class namespaces every time.

At the implementation level, this unified access is backed by a singleton instance of `\OP\OP`.

This makes it easier to:

- use core features from anywhere
- use unit features from anywhere
- keep code short and readable
- reduce namespace-related friction in daily work

## Design Meaning

In practical terms, `OP()` acts as a unified gateway to the framework.

More concretely, the global `OP()` function returns the reusable singleton instance of `\OP\OP`.

That is why it is such an important part of the developer experience in the ONEPIECE Framework.

## Namespace-Free Use

One important feature of ONEPIECE Framework is that `OP()` is a global function entry point that is intentionally easy to call from namespaced code.

In many PHP frameworks, code inside a namespace often has to resolve framework symbols through imports, fully-qualified names, service containers, facades, or dependency injection.
ONEPIECE Framework deliberately gives `OP()` a different role: it is the namespace-free gateway to the framework.

This is not an accidental convenience. It is part of the framework's developer ergonomics.
The purpose is to let application, unit, module, template, and framework code reach common framework features without repeatedly solving namespace access.

Even when application or module code is inside a namespace, call it as:

```php
OP()->Request('key');
OP()->isAdmin();
OP()->Template('file.phtml');
```

Do not mechanically add namespace resolution, imports, or fully-qualified class-like calls.

These are wrong:

```php
\OP\OP();
use function OP\OP;
```

The name `\OP\OP` is the framework class used behind the global function.
It is not the function name to call.

The implementation confirms this distinction: `asset/core/function/OP.php` has no namespace declaration and defines `function OP()`.

## Agent Caution

[DOC-RISK] Namespace fixes must not be applied mechanically.

Before adding `\`, `use function`, or other imports for a framework API call, confirm the symbol's real declaration and framework contract.

For `OP()`, the contract is:

- `OP()` is the global function to call.
- `\OP\OP` is the class instance returned by that function.
- `OP\OP()` is not a valid function call.
- `use function OP\OP;` is not valid for this use case.

See `asset/docs/agent-mistake/op-function.md` for a recorded AI-agent mistake and the concrete failure this rule prevents.
