# Responsibility Boundaries

## Purpose

This document defines the main responsibility boundaries between major framework subsystems.

Its goal is to help AI and contributors decide where a change belongs.

## Related Documents

- `for-tomoaki-nagahara.md`
- `invariants.md`
- `unit-module-boundary.md`
- `common-recipes.md`

## `app.php`

- owns framework entry and startup
- does not own page-specific application logic
- should not absorb responsibilities that belong to App, Router, or Layout

## App Unit

- owns post-startup application execution flow
- executes the resolved endpoint
- buffers content before later rendering stages
- delegates to Layout when needed

## Router Unit

- resolves endpoint and request arguments
- decides routing targets
- does not own final page framing

## Layout Unit

- owns shared page framing and final HTML wrapper behavior
- runs after content generation when layout is enabled
- does not own routing decisions

## Layout

- owns selection and registration of its own layout-specific assets
- should not assume that `asset/layout/<layout-name>/js/` or `asset/layout/<layout-name>/css/` is packed automatically just because the directory exists
- should not implicitly delegate its asset policy to the WebPack unit or WebPack module

## Notice Unit

- consumes stored errors
- decides screen output versus mail notification
- does not capture PHP errors itself

## `OP_ERROR`

- stores collected framework errors
- aggregates repeated error messages
- does not register PHP handlers
- does not decide screen versus mail output

## Error Handler

- captures reachable PHP errors, uncaught exceptions, and shutdown-time errors
- feeds error storage
- does not decide how notices are finally displayed

## CI Unit

- owns code inspection flow
- owns commit marker generation
- participates in push-gate enforcement through saved CI state
- does not own every Git rule

## CD Unit

- owns delivery-side branch and deployment-related controls
- includes GitHub-oriented push restrictions outside pure CI marker checks

## WebPack Unit

- owns asset registration state
- owns grouped output generation
- owns cache/minify/output behavior
- should not own automatic discovery or registration of layout-specific asset directories

## WebPack Module

- owns delivery-side request entry for grouped assets
- prepares request and layout context
- delegates actual grouped output work to the WebPack unit
- should not own layout-specific asset policy

## Config System

- owns layered configuration loading
- owns `name.php` / `_name.php` override behavior
- should remain separate from rendering logic

## `OP_UNIT_MAPPER`

- owns typed unit access mapping
- redirects supported unit names through config mapping
- does not instantiate arbitrary units beyond the generic Unit system contract

## Interfaces

- define required contracts
- constrain what callers may rely on
- do not define every internal implementation choice
