# AGENTS.md

This file provides instructions for AI coding agents working on this repository.

## Project

This repository is part of the ONEPIECE Framework.

ONEPIECE Framework is an Apache-2.0 licensed OSS project.

## Core Principle

Do not modify framework core files unless explicitly requested.

Application-specific customization should be done under the `asset/` directory.

## Important Paths

- `app.php`
  - Application entry point.
  - Do not modify unless explicitly requested.

- `asset/bootstrap/index.php`
  - Framework bootstrap.
  - Do not modify unless explicitly requested.

- `asset/config/`
  - Configuration files.
  - Safe customization area.

- `asset/docs/`
  - Project documentation.

- `index.php`
  - Directory-level controller.

- `*.phtml`
  - Template/view files.

## ONEPIECE Framework Rules

- HTML files may be passed through the framework.
- PHP code inside `.html` files may be executed.
- Layout rendering may still be applied to `.html` files.
- Directory-level `index.php` files work as controllers.
- Use `.phtml` for templates.

## Coding Rules

- Write code comments in English.
- Keep documentation clear and concise.
- Prefer framework APIs over raw PHP superglobals.
- Do not use raw `$_GET`, `$_POST`, `$_REQUEST`, `$_COOKIE`, `$_SESSION`, or `$_SERVER` unless explicitly necessary.
- Use `OP()->Request()` where appropriate.
- Do not use `var_dump()` or `print_r()` for debugging.
- Use `D()` for framework-aware debug output.

## Git / CI Rules

- Run the project CI before pushing changes.
- Prefer the `cicd` command when available.
- Do not bypass Git hooks unless explicitly requested.
- Commit messages should use approved prefixes such as:
  - `New:`
  - `Add:`
  - `Chg:`
  - `Fix:`
  - `Doc:`
  - `Del:`

## Documentation Rules

- README.md is for humans.
- AGENTS.md is for AI coding agents.
- CUSTOMIZATION_MAP.md should describe safe customization points.
- CODEX.md may contain Codex-specific workflow notes.

## Do Not

- Do not rewrite the project architecture casually.
- Do not move files unless requested.
- Do not edit core files as a shortcut.
- Do not introduce unnecessary dependencies.
- Do not change public behavior without explaining the reason.
- Do not remove existing comments or documentation unless clearly obsolete.

## Preferred Workflow

1. Inspect the existing structure.
2. Identify the smallest safe change.
3. Modify files only in the appropriate customization area.
4. Run checks if available.
5. Explain what changed and why.

