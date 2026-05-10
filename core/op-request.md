# `OP()->Request()`

## Overview

`OP()->Request()` is the framework request accessor for both web and CLI execution.

The main idea is that the caller does not have to switch manually between:

- CLI arguments
- web GET
- web POST
- JSON request bodies

The framework resolves the current request style and returns request values through one access point.

## Main Behavior

Typical usage is:

```php
$request = OP()->Request();
$value   = OP()->Request('key');
```

This means:

- in CLI, key-value arguments can be read through the same API
- in web, GET and POST can be read through the same API
- JSON request bodies are also parsed automatically in the supported case

## Practical Meaning

The purpose is to let application code use one request accessor instead of directly branching on environment and transport details.

That makes the calling side simpler and more consistent.

It also matches the framework preference for unified and explicit interfaces.

## Why Returned Values Are Encoded

The design intention is practical.

Humans often forget to escape values before output.

From that point of view, returning an encoded value is considered safer than returning a completely raw value by default.

This is a safety-oriented framework decision intended to reduce accidental output-side mistakes, especially in HTML-oriented use.

## Current Web Behavior

In the current implementation:

- if `CONTENT_TYPE` starts with `application/json`, the request body is decoded as JSON
- otherwise, the framework reads either `$_POST` or `$_GET`

So the current model is:

- JSON body
  or
- POST parameters
  or
- GET parameters

## [DOC-NOTE] About GET and POST Merge

The framework does not treat a silent GET+POST merge as an ideal default.

The practical concern is that a silent merge makes it harder to understand:

- where a value came from
- which side should win when the same key exists in both places
- why a final value became what it became

From the framework philosophy point of view, keeping request-source behavior explicit is easier to understand than merging different transport sources implicitly.

## [DOC-NOTE] Safety Interpretation

`OP()->Request()` should not be interpreted as a universal sanitizer for every context.

It is better understood as a request accessor that returns values already shifted toward HTML-side safety, because that is where humans most often forget to protect output.

For non-HTML contexts such as SQL, shell, URL, JSON, or signature verification, additional context-appropriate handling is still required.

## [DOC-FUTURE] Possible Direction

If merged request access is ever needed, the preferred direction would be an explicit API or explicit mode, rather than silently changing the default behavior of `OP()->Request()`.

## [DOC-FUTURE] About `PUT`, `PATCH`, and `DELETE`

At the current design level, there is no plan to add dedicated request parsing support for `PUT`, `PATCH`, or `DELETE` to `OP()->Request()`.

The current scope remains centered on practical CLI input, GET, POST, and JSON handling.

## Current CLI Behavior

In CLI execution, arguments in the form:

```text
key=value
```

are collected and exposed through the same `OP()->Request()` API.

The current CLI/Web branch is selected by shell detection based on the framework's shell check, not by `$_SERVER['SHELL']`.

## Note

This document describes the current As-Is behavior at the framework level.

Technical details such as request-source selection and final encoding are described separately in the core technical document.
