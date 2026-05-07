# App Unit

## Overview

The App unit is responsible for managing the outer application flow of the ONEPIECE Framework.

It does not exist to own every detail of rendering or routing logic.

Its role is to manage the large-scale application-side flow after framework startup has completed.

## Historical Background

The ONEPIECE Framework originally began as an integration of individually created libraries.

In the early stage, the result was effectively monolithic:

- application logic and library logic were tightly integrated
- maintenance became difficult
- specifications became complicated

To solve this, the framework moved toward looser coupling.

Core behavior was separated, and many functions were reorganized as units.

One of the consequences of that separation was that application management itself was split out from the old monolithic system behavior.

## Relationship Between `app.php` and the App Unit

`app.php` is intentionally minimal.

Its main role is:

1. act as the application entry point
2. start bootstrap
3. set app-root-related baseline values where that is easiest to determine
4. define timing or memory-related baseline values for benchmark-style measurement
5. hand application management over to the App unit

In other words, `app.php` starts the framework and launches the unit that manages the application flow.

## Meaning of the Split

This split is important because it keeps the responsibilities clear:

- `app.php` is the entry-point and startup side
- the App unit is the application flow management side

This is part of the broader architectural move away from a monolithic system toward a unit-oriented and more loosely coupled structure.

