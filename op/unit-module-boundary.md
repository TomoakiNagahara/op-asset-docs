# Unit And Module Boundary

## Purpose

This document explains the practical difference between UNIT and MODULE in the ONEPIECE Framework.

Its goal is to help AI and contributors decide whether a new behavior belongs in `asset/unit/`, `asset/module/`, or somewhere else.

This is a framework-level responsibility guide, not an implementation detail for one specific package.

## Short Rule

A UNIT is a reusable internal capability.

A MODULE is an independent feature package or request-facing feature.

Use a UNIT when the behavior is expected to be called across the framework, application, other units, modules, templates, or layouts.

Use a MODULE when the behavior forms a relatively self-contained feature with a clear purpose, entry point, or user-facing role.

Another practical distinction is call direction.

UNITs are expected to be easy to call from many namespaces through access paths such as `OP()->Unit()`.

MODULEs are not normally designed as cross-namespace shared APIs. They are usually invoked as a focused feature, endpoint, or adapter.

## UNIT

A UNIT is a general-purpose functional unit.

It is usually closer to system behavior than to one finished user feature.

Typical UNIT responsibilities include:

- providing reusable framework or application capability
- exposing an API or contract that other code can depend on
- supporting multiple callers across units, modules, templates, layouts, or application code
- managing shared state, transformation, validation, routing, output generation, or orchestration
- being replaceable or configurable through framework mapping when the unit contract allows it

Examples of UNIT-style responsibilities:

- application flow management
- routing
- layout rendering control
- validation
- form handling
- ORM / database access
- notice handling
- WebPack asset registration and grouped output generation
- CI / CD orchestration

If a feature is useful because many other features need to call it, it is probably a UNIT.

## Call Style And Developer Center Of Gravity

UNITs sit closer to the framework and CORE developer side.

This does not mean only CORE developers can write units, but it does mean a UNIT usually carries framework-like expectations:

- many callers may depend on it
- it may be called frequently from different namespaces
- it may sit under `OP()->Unit()` or the generic unit access path
- it may expose an interface or stable contract
- it may become part of the shared vocabulary used by other units, modules, templates, layouts, and application code

Because of that, changing a UNIT can have a wider blast radius than changing one self-contained feature.

Database access, form handling, validation, routing, layout control, and similar capabilities are UNIT-shaped because other framework parts are expected to call them rather than own their internals.

MODULEs sit closer to the end-user or application developer side.

This does not mean modules are only for end users, but it does mean a MODULE usually has a more feature-oriented center of gravity:

- it is installed or enabled because the application wants that feature
- it usually has a focused entry point or request-facing role
- it may compose several units internally
- other packages should not normally depend on its internal implementation
- it should not become a hidden shared system layer unless the design is intentionally changed

In short, UNITs are shared mechanisms. MODULEs are feature packages that may use those mechanisms.

## MODULE

A MODULE is an independent feature package.

It is usually closer to a complete feature than to a shared internal capability.

Typical MODULE responsibilities include:

- owning a specific feature purpose
- exposing a request-facing or feature-facing entry point
- composing one or more units internally
- staying relatively independent from other modules
- avoiding becoming a shared system layer unless that is the explicit design

Examples of MODULE-style responsibilities:

- access counter
- contact form
- sitemap delivery
- RSS delivery
- a focused admin feature
- WebPack delivery endpoint

If a feature is useful because it is itself a complete feature or endpoint, it is probably a MODULE.

## Relationship Between UNIT And MODULE

MODULEs may use UNITs.

This is normal.

The usual dependency direction is MODULE to UNIT.

UNITs should be cautious about depending on MODULE internals because that can turn a feature package into an accidental framework layer.

For example, a contact form module may use:

- a validation unit
- a form unit
- a mail-related unit
- a template or layout path
- notice or session-related framework behavior

The contact form remains a MODULE because the complete feature is the contact form.

The reusable mechanics inside it remain UNIT responsibilities.

## WebPack Example

WebPack shows the boundary clearly.

`op-unit-webpack` is the UNIT side.

It owns:

- asset registration state
- cache / minify / output behavior
- grouped output generation

`op-module-webpack` is the MODULE side.

It owns:

- the delivery-side request entry
- request-to-asset-type handling
- handoff to the WebPack unit

The module receives the external request.

The unit owns the reusable mechanism.

## Decision Checklist

Choose a UNIT when:

- other units or modules should call this behavior
- the behavior is a reusable internal capability
- the behavior is expected to be reachable frequently from different namespaces through `OP()->Unit()` or generic unit access
- a stable API or contract matters
- the behavior may need mapping, replacement, or framework-level configuration
- the behavior is infrastructure-like rather than one complete feature

Choose a MODULE when:

- the behavior is a complete feature
- the behavior has a clear request-facing or user-facing entry point
- the feature can be installed, enabled, disabled, or reasoned about independently
- the feature mainly composes existing units instead of becoming a shared lower layer
- other features should not normally depend on its internals
- the behavior is closer to an application-facing package than a framework-wide shared mechanism

If both seem possible, start by asking what other code should depend on.

If many features should depend on the behavior, make the reusable part a UNIT.

If the behavior is mainly valuable as one feature, make it a MODULE and keep reusable mechanics in existing or new units.

## Anti-Patterns

Do not put application-specific behavior into an existing framework UNIT just because the unit is easy to call.

Do not turn a MODULE into a hidden shared system layer unless the design has clearly changed.

Do not duplicate reusable mechanics inside many modules when the behavior should become a UNIT.

Do not move request-facing feature ownership into a UNIT when a MODULE would keep the boundary clearer.

## Documentation Placement

UNIT-system philosophy, background, and history belong in `asset/docs/unit/`.

MODULE-system philosophy, background, and history belong in `asset/docs/module/`.

Framework-wide responsibility boundaries between UNIT and MODULE belong in `asset/docs/op/`.

Implementation details for one specific unit or module belong in that package's own `docs/` directory.
