# Runtime Lifecycle Flow

This chart shows the normal ONEPIECE Framework application lifecycle from request entry to PHP shutdown.

It focuses on the skeleton-level flow. Package-internal details are intentionally kept in the responsible package documents.

```mermaid
flowchart TD
    Request([Request starts]):::edge
    Server[Web server dispatch]:::server
    Static{"Physical static file?"}:::decision
    StaticOut[Serve file directly]:::output
    AppPhp[app.php]:::entry
    Constants[Set startup constants and APP_ROOT]:::entry
    BootstrapExists{"asset/bootstrap/index.php exists?"}:::decision
    Guidance[Show submodule initialization guidance]:::fallback
    Bootstrap[Bootstrap include chain]:::startup
    Ready[Framework runtime is ready]:::startup
    AppAuto["OP()->Unit()->App()->Auto()"]:::app
    Router[Router resolves endpoint and args]:::router
    Endpoint{"Endpoint found?"}:::decision
    Mime{"Endpoint MIME is text/*?"}:::decision
    Binary[Read and emit file content]:::output
    Shell{"Shell execution?"}:::decision
    ShellTemplate["Run endpoint through OP()->Template()"]:::template
    Buffer[Start output buffer]:::app
    EndpointTemplate["Run endpoint through OP()->Template()"]:::template
    EndpointWork[index.php or selected endpoint handles route logic]:::template
    Capture[Capture endpoint output as App content]:::app
    Html{"MIME is text/html?"}:::decision
    LayoutAuto["OP()->Unit()->Layout()->Auto()"]:::layout
    LayoutExec{"Layout execute enabled?"}:::decision
    LayoutTemplate[Run layout controller template]:::layout
    Content["OP()->Unit()->App()->Content()"]:::output
    DirectContent[Emit captured content directly]:::output
    Response[Response body has been emitted]:::output
    Shutdown[PHP shutdown callbacks]:::shutdown
    ErrorNotice[Collect fatal errors and show notices when available]:::shutdown
    AdminTools[Admin-only memory or notice helpers may append output]:::shutdown
    End([Process ends]):::edge

    Request --> Server
    Server --> Static
    Static -- yes --> StaticOut --> End
    Static -- no, or pass-through rule --> AppPhp
    AppPhp --> Constants --> BootstrapExists
    BootstrapExists -- no --> Guidance --> Shutdown
    BootstrapExists -- yes --> Bootstrap --> Ready --> AppAuto
    AppAuto --> Router --> Endpoint
    Endpoint -- no --> Shutdown
    Endpoint -- yes --> Mime
    Mime -- no --> Binary --> Shutdown
    Mime -- yes --> Shell
    Shell -- yes --> ShellTemplate --> Shutdown
    Shell -- no --> Buffer --> EndpointTemplate --> EndpointWork --> Capture --> Html
    Html -- yes --> LayoutAuto --> LayoutExec
    LayoutExec -- yes --> LayoutTemplate --> Content --> Response
    LayoutExec -- no --> Content --> Response
    Html -- no --> DirectContent --> Response
    Response --> Shutdown
    Shutdown --> ErrorNotice --> AdminTools --> End

    classDef edge fill:#111827,stroke:#111827,color:#fff;
    classDef server fill:#1d4ed8,stroke:#1e3a8a,color:#fff;
    classDef entry fill:#0f766e,stroke:#134e4a,color:#fff;
    classDef startup fill:#0e7490,stroke:#155e75,color:#fff;
    classDef app fill:#7c3aed,stroke:#5b21b6,color:#fff;
    classDef router fill:#ea580c,stroke:#9a3412,color:#fff;
    classDef template fill:#f59e0b,stroke:#b45309,color:#111827;
    classDef layout fill:#be123c,stroke:#881337,color:#fff;
    classDef output fill:#16a34a,stroke:#166534,color:#fff;
    classDef shutdown fill:#475569,stroke:#334155,color:#fff;
    classDef decision fill:#f8fafc,stroke:#334155,color:#111827;
    classDef fallback fill:#fee2e2,stroke:#991b1b,color:#7f1d1d;
```

## Reading Guide

- `app.php` owns startup. It sets baseline values, loads bootstrap, and then delegates to `OP()->Unit()->App()->Auto()`.
- Bootstrap is still before the normal framework runtime is fully available.
- The App unit owns the outer application-side lifecycle after startup.
- The Router unit resolves the endpoint and route arguments.
- Text endpoints are executed through `OP()->Template()`. Non-text endpoints are emitted directly.
- For HTTP text responses, endpoint output is captured first. HTML output can then be expanded through Layout before final emission.
- Shutdown callbacks can still collect fatal errors, show framework notices, or append admin-only helper output.

## Related Documents

- `asset/docs/skeleton/entry-point.md`
- `asset/docs/new-world/new-world.md`
- `asset/docs/unit/app-unit.md`
- `asset/docs/core/error-handling.md`
