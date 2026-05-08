# Safe Customization Map

This map separates application-owned customization points from framework-managed core in `op-skeleton-2030`.

Use the left side first. Move right only when the task is truly changing framework behavior, not just site behavior.

If you are not acting as a ONEPIECE Framework CORE developer, do not casually edit CORE areas.
Do not add application-specific features to CORE or to existing framework UNIT packages.
When an OP application needs project-specific behavior, create a dedicated UNIT or MODULE for that application.
When you find a framework bug, prepare a pull request for the responsible package instead of hiding the fix inside application customization.

## Boundary Summary

| Area | Ownership | Safe use | Avoid by default |
| --- | --- | --- | --- |
| `asset/config/*.php` | Application skeleton | Shared application defaults, feature flags, layout choice, HTML metadata, unit mapping, webpack options. | Hardcoded machine-local secrets or environment-specific values. |
| `asset/config/_*.php` | Local developer or deployment | Machine-local overrides loaded after `name.php` by `Config::Get()`. These are ignored by `.gitignore`. | Committing local credentials, admin IPs, hostnames, or per-machine runtime paths. |
| `index.php` | Application skeleton | Top-level route-to-template decisions for this application. | Removing the fallback `app.php` handoff or mixing template rendering with bootstrap concerns. |
| `asset/layout/<site-layout>/` | Application or layout package | Site framing, headers, footers, menus, shared page structure. Prefer creating a site-specific layout for project-owned design. | Editing a framework-provided layout package as if it were local app code unless the layout package itself is the intended target. |
| `asset/layout/<layout>/template/` | Layout-specific templates | Shared templates that should override skeleton defaults for one layout. This path is searched before `asset/template/`. | Duplicating common layout markup into page templates. |
| `asset/template/` | Framework default template package | Skeleton/default templates and generic fallback pages. | Placing developer-specific or end-user-specific page templates here unless that ownership tradeoff is intentional. |
| New page templates | Application | Add project-specific pages in the active layout's template area when they belong to the site's design; use `asset/template/` only for skeleton-level defaults. | Changing core routing or bootstrap to make a simple page work. |
| `asset/unit/<unit>/` | Unit package | Unit implementation only when the task is to change that unit package, or when creating a dedicated project-owned unit. | Adding app-specific features to existing framework units, or editing units for app-level configuration, route, or view changes. Prefer `asset/config/unit.php` mappings, normal unit APIs, or a dedicated project unit. |
| `asset/module/<module>/` | Module package | Module implementation only when the task is to change that module package, or when creating a dedicated project-owned module. | Adding app-specific behavior to shared framework modules when it belongs in config, templates, layout, a project-owned unit, or a project-owned module. |
| `asset/core/` | Framework core | Core fixes, core feature work, or upstream framework changes. | Application behavior, project design, local environment config, or one-off routing changes. |
| `asset/bootstrap/` | Framework bootstrap package | Startup contract changes only after reviewing `app.php`, bootstrap include order, and required pre-core assumptions. | Normal app behavior, templates, layout, routing, or feature configuration. |
| `app.php` | High-impact entry point | Entry-point changes only when the application launch contract changes. | Bypassing bootstrap, changing `APP_ROOT` casually, or replacing `OP()->Unit()->App()->Auto()`. |
| `.htaccess` | Web server routing contract | Rewrite changes only when URL dispatch behavior must change. | Template or page changes. |
| `asset/init/` | Skeleton initialization tooling | Submodule setup, update, and local initialization helper changes. | Runtime application behavior. |
| `asset/config/submodule/**` | Skeleton dependency map | Configure which submodule repositories/paths initialization should fetch. | Treating these as application runtime feature flags. |
| `public_html/` | Deployment/public document-root package | Public web root content when that submodule is present and intentionally targeted. | Changing skeleton runtime behavior without checking rewrite and entry-point flow. |

## Decision Rules

1. If the change is configuration, start in `asset/config/`.
2. If the value is local to one machine or deployment, use `asset/config/_name.php`, not `asset/config/name.php`.
3. If the change is page output, identify the template actually used by the route before editing.
4. If the change is shared page framing, prefer the active layout under `asset/layout/`.
5. If the change is route selection, inspect `index.php`, `.htaccess`, and router behavior together.
6. If the change seems to require `asset/core/`, `asset/bootstrap/`, `asset/unit/`, or `asset/module/`, first confirm it is not solvable through config, layout, template, a project-owned unit, or a project-owned module.
7. If the change is an application-specific feature, do not add it to CORE or an existing framework UNIT. Create a dedicated UNIT or MODULE.
8. If the change is a framework bug fix, make it as a pull request against the responsible framework package.

## Safe Customization Points

### Application Config

Use `asset/config/` for shared defaults:

- `asset/config/app.php`: app title, copyright, and app-level switches.
- `asset/config/layout.php`: active layout and layout-related settings.
- `asset/config/html.php`: language, charset, canonical URL, and page title defaults.
- `asset/config/unit.php`: unit name mapping.
- `asset/config/webpack.php`: JavaScript and CSS build/runtime settings.
- `asset/config/admin.php`, `asset/config/php.php`, `asset/config/develop.php`: environment-aware behavior when it is meant to be shared by the project.

Use underscore-prefixed files for local-only values:

- `asset/config/_app.php`
- `asset/config/_admin.php`
- `asset/config/_php.php`
- `asset/config/_webpack.php`

These files are ignored by `.gitignore` and should remain local unless the project intentionally changes that policy.

### Routing And Pages

Use `index.php` for application-level route selection. Keep its startup fallback intact:

- Preserve the `defined('_OP_APP_START_') === false` fallback.
- Keep route selection separate from bootstrap setup.
- Keep template rendering separate from web-server rewrite rules.

For templates:

- Prefer layout-specific shared templates in `asset/layout/<layout-name>/template/`.
- Use `asset/template/` for skeleton-level defaults and fallback templates.
- Avoid placing project-specific page ownership into framework default template packages unless that is the explicit design choice.

### Layout

Use a project-owned layout when changing the site's visual shell:

- Create or select a layout through `asset/config/layout.php`.
- Put shared header, footer, menu, and common structure in the layout.
- Let page templates focus on page-specific output.

The bundled `flexbox` layout is a package. Editing it changes that layout package, not only one application page.

### Units And Modules

Units and modules are extension packages, not the first stop for app-specific changes.

Use them when:

- The task is to change that package's reusable behavior.
- The application needs a new project-owned unit or module.
- The change should be available through a unit or module API.

Avoid them when:

- A config value can select the behavior.
- A route or template change is enough.
- The change only belongs to one site's presentation.
- The intended change is application-specific but would modify an existing framework-owned unit or module.

For app-specific behavior, create a dedicated UNIT or MODULE and wire it into the application through configuration or normal framework extension points.

## Framework Core Zones

Treat these as framework-managed unless the task is explicitly framework work:

- `asset/core/`
- `asset/bootstrap/`
- `asset/unit/`
- `asset/module/`
- `asset/layout/<framework-layout>/`
- `asset/template/`
- `asset/webpack/`

These directories are submodule-managed in normal setup. Local edits inside them can be useful for framework development, but they should not be the default way to customize one application.

If you are not working in the role of a CORE developer, treat `asset/core/` as read-mostly reference material.
Application-specific features do not belong in CORE.
They also do not belong in existing framework UNIT packages just because the feature is convenient to call from there.

When a core-zone change is necessary:

1. State which package owns the behavior.
2. Check whether the package should be changed upstream, forked, or pinned.
3. Keep the skeleton change separate from the package change.
4. For framework bugs, prepare a pull request for the responsible package.
5. Re-run startup, route, and template checks affected by that package.

## High-Impact Files

Change these only with a clear reason:

- `app.php`: request entry point, `APP_ROOT`, bootstrap handoff, app auto-launch.
- `.htaccess`: Apache rewrite and pass-through behavior.
- `asset/bootstrap/index.php`: bootstrap include order and pre-core startup.
- `asset/config/submodule/**`: repository/path selection for initialized packages.
- `asset/init/**`: initialization and update tooling.

## Common Tasks

| Task | Start here | Do not start here |
| --- | --- | --- |
| Change app title | `asset/config/app.php` or local `_app.php` | `asset/core/` |
| Change active layout | `asset/config/layout.php` | `asset/bootstrap/` |
| Add a local admin IP | `asset/config/_admin.php` | Committed shared config |
| Add a page | `index.php` plus the responsible template path | `.htaccess` or `asset/core/` |
| Change header/footer/menu | Active layout template directory | Every page template |
| Change default 404 output | Responsible fallback template | Router core |
| Change route parsing rules | Router unit, after checking config and `index.php` | Page template |
| Change framework startup | `app.php` and `asset/bootstrap/`, with full startup review | Layout or template files |
| Change a unit API | Responsible `asset/unit/<unit>/` package | Skeleton config only |
| Change a reusable module | Responsible `asset/module/<module>/` package | One page template |
| Add app-specific behavior | Dedicated project UNIT or MODULE | CORE or an existing framework UNIT |
| Fix a framework bug | Pull request to the responsible framework package | Hidden app-local workaround in CORE |

## Review Checklist

Before editing a framework-managed area, answer these questions:

- Can this be handled by `asset/config/` or a local `_*.php` override?
- Is this page output, shared layout, route selection, package behavior, or startup behavior?
- Am I acting as a CORE developer for this change, or should CORE remain untouched?
- Is this app-specific behavior that should become a dedicated UNIT or MODULE?
- Is this a framework bug that should be fixed through a pull request?
- Will the change survive `php asset/init/update.php` without being overwritten or creating submodule drift?
- Is the target file tracked by this skeleton repo, or by a submodule/package?
- Does the change need a matching document update in `CODEX.md` or a package-level `docs/` directory?
