# Design Philosophy

## Overview

The ONEPIECE Framework prefers explicit behavior over hidden or overly magical behavior.

The name `ONEPIECE` itself also reflects an important intention of the framework.

It comes from the idea of connecting all functions into one whole.

This is not only a coding style preference.

It is connected directly to the broader framework philosophy:

- simple
- intuitive
- concise
- easy to understand

## Why Explicitness Matters

The framework assumes that behavior is easier to maintain when it is visible and direct.

In practice, explicit design makes it easier to understand:

- what is happening
- where a decision is made
- which layer is responsible
- what should be changed and what should be preserved

This matters for both humans and AI.

## Design Preference

The framework generally prefers:

- explicit contracts over hidden conventions
- visible participation over implicit side effects
- direct structure over heavy indirection
- understandable operational behavior over clever but opaque shortcuts

It also places strong value on freedom and flexibility of system design.

In practical terms, if a subsystem satisfies the required contract, the framework prefers to leave room for that subsystem to be replaced rather than forcing a single canonical implementation forever.

This preference appears in many parts of the framework.

Examples include:

- `app.php` as a clear entry point
- NEW WORLD separating endpoint execution from final rendering
- `OP_CI` as an explicit CI participation contract
- unit replacement through interface-based contracts
- local override config files with predictable loading order
- replaceable repositories and subsystems through configurable repository URLs and mapping

## Error Handling Preference

The framework also prefers careful error propagation over relying on `throw` as the primary application-level control flow.

Throwing exceptions everywhere may be faster to implement.

However, the framework's design preference is to report failure carefully back to the caller whenever possible, so the caller can decide what to do next in an understandable way.

This is harder to implement.

The framework still supports PHP exception capture at the runtime level.

But as an application design preference, it does not treat "throw first" as the ideal default.

The reason is practical.

Careful error handling is often more helpful for the side that uses the code than aggressive exception-driven flow.

This preference is also rooted in bitter past experience, not only in abstract theory.

## Meaning

This philosophy is one of the reasons the framework can remain practical even when it grows.

The goal is not to remove all abstraction.

The goal is to keep abstraction understandable.

That is why explicit processing is treated as a positive design value in the ONEPIECE Framework.

This is also why the framework does not assume that only official implementations must be used.

For example, even `op-core` does not have to remain the pure official implementation forever. A third-party implementation or a forked custom implementation may be used, as long as the required role and contract are satisfied.
