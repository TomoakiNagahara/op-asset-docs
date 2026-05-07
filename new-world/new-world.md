# NEW WORLD

## Overview

In the ONEPIECE Framework, **NEW WORLD** is the name of an application execution model built on three essential ideas.

Those three essentials are:

1. a file-oriented endpoint model
2. execute `index.php` first, capture its result, and expand it later through layout
3. HTML Pass-Through

Everything else should be understood as a consequence or extension of those three ideas rather than as a separate core principle.

## 1. File-Oriented Endpoint Model

The first essential point is the routing model.

When a request URL is converted into a path relative to the document root, the framework walks that path and searches for `index.php`.

If an `index.php` is found, that file becomes the endpoint.

In traditional MVC terms, that `index.php` plays the role that many frameworks would assign to a controller.

This is important because NEW WORLD does not begin from:

- class-name routing
- method-name routing

It begins from a file-oriented endpoint model.

That makes deep nesting natural and avoids unnecessary controller ceremony for many cases.

### Example

```text
https://example.com/foo/bar/hoge/fuga?key=var
```

If the nearest endpoint is:

```text
/foo/bar/index.php
```

then:

- `/foo/bar/index.php` becomes the endpoint
- the remaining path segments can become router arguments
- `key=var` remains a normal query string

In practice, `OP()->Unit()->Router()->Args()` can return the equivalent of:

```php
['hoge', 'fuga']
```

## 2. Execute Endpoint First, Render Layout Later

The second essential point is execution order.

NEW WORLD executes the endpoint first.

The result is captured and stored.

Only after that does the framework expand the final response through the layout side when appropriate.

This is a major difference from many traditional frameworks, where the layout starts first and then calls into controller and view logic in the middle of rendering.

That older order often made later response changes awkward.

For example, cookies and similar headers must be set before the response body is emitted.

If layout output starts too early, changing cookies or headers later may require special hook-like mechanisms.

NEW WORLD avoids that by reversing the order:

1. execute endpoint
2. capture result
3. decide final layout behavior
4. emit final response

This is one of the central reasons NEW WORLD can support endpoint-side control more naturally.

## 3. HTML Pass-Through

The third essential point is HTML Pass-Through.

Historically, this was one of the original visible features of NEW WORLD.

It allowed HTML files to participate in the framework flow without forcing a conventional controller class and controller method structure.

That means a plain HTML-oriented file can still be executed through the framework model.

Later, the same idea was extended to other resource types as well, but the historical name remained.

HTML Pass-Through is therefore not a side feature.

It is one of the original pillars of NEW WORLD.

## Execution Flow

The current flow based on those three ideas is:

1. the Router unit resolves the endpoint from the URL
2. the App unit executes that endpoint through `OP()->Template()`
3. the output is captured and stored
4. the framework decides whether layout should be applied
5. the Layout unit expands the final response when layout is used
6. otherwise the stored content is emitted directly

## Why This Model Matters

These three ideas solve several problems at once.

- routing does not need to start from class and method names
- nested directory structures remain natural
- HTML files can participate directly in the framework flow
- the endpoint can prepare cookies and similar response state before final output
- the framework can delay final rendering until enough context is known

## **Grand Line**

The **Grand Line** of NEW WORLD is not a single trick.

It is the combination of these three essentials:

- file-oriented endpoint resolution
- execute first and render later
- HTML Pass-Through

That combination is what separates NEW WORLD from older framework models.

## Relationship to Current Implementation

Current implementation details, such as MIME-based layout decisions or the exact Router extension list, should not be mistaken for the essence of NEW WORLD itself.

Those are current implementation details.

The core ideas remain the three principles above.

## Summary

NEW WORLD should be understood from these three points first:

1. the endpoint model is file-oriented
2. the endpoint runs before final layout rendering
3. HTML Pass-Through is one of its original essential features
