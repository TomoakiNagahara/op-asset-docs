# Unit Replacement

## Overview

One of the major characteristics of the ONEPIECE Framework is that units can be called in a unified form such as:

```php
OP()->Unit()->App()
```

At the same time, the actual unit implementation can be replaced.

## Related Framework Documents

- `../op/invariants.md`
- `../op/responsibility-boundaries.md`
- `../op/common-recipes.md`

## Main Idea

The important point is that the caller can continue to use the same high-level access path while the concrete unit behind it may be changed.

This is supported by three elements:

- unit interfaces in `asset/core/interface/`
- the unit mapper in `asset/core/trait/OP_UNIT_MAPPER.php`
- application-side mapping config in `asset/config/unit.php`

## Two Access Styles

The framework currently has two unit access styles:

- `OP()->Unit()->App()`
- `OP()->Unit('App')`

The method-chain style is the newer typed style.

The string-argument style is still important when calling units that are not officially exposed through interface-based typed mapping.

## Historical Background

In older generations, only this style existed:

```php
OP()->Unit('UnitName')
```

At that time, the interface-based method chain had not yet been introduced.

Because of that history, older repositories may still contain the older call style even when the same unit could now be reached through a typed method chain.

## Why This Matters

This design makes it possible to:

- keep the calling style stable
- swap a unit implementation without changing every caller
- preserve framework-level consistency while allowing application-specific replacement
- leave room for third-party and forked implementations when the required contract is satisfied

## Meaning

In other words, the framework is designed so that:

- callers depend on the unit contract
- the actual implementation may be redirected by mapping

This is one of the characteristics that gives the ONEPIECE Framework flexibility at the unit layer.

## Uniqueness

Frameworks with replaceable subsystems do exist, especially in DI-container-based or adapter-based ecosystems.

However, the ONEPIECE Framework has a distinctive combination:

- a unified high-level access path such as `OP()->Unit()->App()`
- interface-based contract guarantees
- replacement through application-side mapping
- freedom for the unit implementation outside the interface-defined contract

This can be described as:

- `interface-driven replaceable subsystem`
- `contract-based swappable unit architecture`

The important point is that the interface guarantees and constrains the required specification, while behavior not defined by the interface remains free for the unit implementation to design.

That balance between replaceability and implementation freedom is one of the unique characteristics of the ONEPIECE Framework.

## Practical Value

This replaceable unit system is especially valuable for areas such as:

- Form
- SQL
- database access

From the user side, the important point is that the user can choose which unit implementation to use.

The internal processing can remain hidden inside the unit, but the user can still work through a unified interface where method names and arguments are constrained by the contract.

Because of that, the user can expect the same kind of result from the same interface even when the internal implementation is replaced.

## Beyond Official Implementations

This flexibility is not limited to official units only.

The broader design idea of the framework is that subsystems may be replaced as long as the required role and contract are satisfied.

In practice, that can include:

- official implementations
- third-party implementations
- forked custom implementations

At the repository-operation level, one practical way to realize this is to change the configured repository URL and use that implementation instead.
