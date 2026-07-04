# `D()` Debug Output

## Overview

In the ONEPIECE Framework, `D()` is one of the most important debugging tools used in daily development.

Its purpose is to display variables for debugging, but it is not just a plain `var_dump()`.

For usage and package-owned technical details, see `asset/core/docs/d-function-usage.md` and `asset/core/docs/d-function-overview.md`.

## Main Characteristics

The current design has several important characteristics.

### 1. File path and line number are included

When `D()` is used, the output includes the file path and line number of the call site.

This makes it easy to identify where the debug output came from.

### 2. Output is designed to be easier to read than `var_dump()`

The output is intended to be much easier to read than native `var_dump()`.

In particular, the Dump unit adds structured formatting for HTML-oriented display.

### 3. Arrays are a major focus of readability

One of the strongest practical differences is array readability.

Array output is formatted in a way that is much easier to scan than plain `var_dump()`.

### 4. Output is restricted to administrators

This is one of the most important differences from many other frameworks.

`D()` output is shown only when the requester is considered an administrator.

That decision is based on `OP()->isAdmin()`.

## Technical Split

The `D()` function itself is only the debug entry point.

It checks whether the current requester is an administrator and then passes the actual formatting work to `op-unit-dump`.

That means:

- `D()` is the debug entry function
- `op-unit-dump` is responsible for formatting and rendering

## Summary

`D()` is the standard debug output function in the ONEPIECE Framework.

It is important not only because it dumps variables, but because it combines:

- admin-only visibility
- source location display
- readable formatting
- specialized array rendering
