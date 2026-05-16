# Runtime Lifecycle Flow

この図は、ONEPIECE Framework のアプリケーションが request を受けてから PHP shutdown に到達するまでの通常の流れを示します。

ここでは skeleton-level の全体像に絞ります。各 package 内部の詳細は、その package が持つ document に置きます。

```mermaid
flowchart TD
    Request([Request 開始]):::edge
    Server[Web server dispatch]:::server
    Static{"物理 static file?"}:::decision
    StaticOut[File を直接返す]:::output
    AppPhp[app.php]:::entry
    Constants[Startup constant と APP_ROOT を設定]:::entry
    BootstrapExists{"asset/bootstrap/index.php が存在する?"}:::decision
    Guidance[Submodule 初期化 guidance を表示]:::fallback
    Bootstrap[Bootstrap include chain]:::startup
    Ready[Framework runtime が利用可能になる]:::startup
    AppAuto["OP()->Unit()->App()->Auto()"]:::app
    Router[Router が endpoint と args を解決]:::router
    Endpoint{"Endpoint がある?"}:::decision
    Mime{"Endpoint MIME は text/*?"}:::decision
    Binary[File content を読んで出力]:::output
    Shell{"Shell 実行?"}:::decision
    ShellTemplate["Endpoint を OP()->Template() で実行"]:::template
    Buffer[Output buffer を開始]:::app
    EndpointTemplate["Endpoint を OP()->Template() で実行"]:::template
    EndpointWork[index.php または選択された endpoint が route logic を処理]:::template
    Capture[Endpoint output を App content として保持]:::app
    Html{"MIME は text/html?"}:::decision
    LayoutAuto["OP()->Unit()->Layout()->Auto()"]:::layout
    LayoutExec{"Layout execute が有効?"}:::decision
    LayoutTemplate[Layout controller template を実行]:::layout
    Content["OP()->Unit()->App()->Content()"]:::output
    DirectContent[保持した content を直接出力]:::output
    Response[Response body の出力完了]:::output
    Shutdown[PHP shutdown callback]:::shutdown
    ErrorNotice[Fatal error を回収し notice を表示]:::shutdown
    AdminTools[Admin-only memory / notice helper が出力を追加する場合がある]:::shutdown
    End([Process 終了]):::edge

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

## 読み方

- `app.php` は startup を担当します。基準値を設定し、bootstrap を読み込み、その後 `OP()->Unit()->App()->Auto()` に処理を委ねます。
- Bootstrap は、通常の framework runtime が fully available になる前の段階です。
- App unit は startup 後の application-side lifecycle の外枠を担当します。
- Router unit は endpoint と route argument を解決します。
- Text endpoint は `OP()->Template()` で実行します。Non-text endpoint は file content を直接出力します。
- HTTP の text response では、endpoint output を先に保持します。HTML の場合は、その後 Layout によって最終出力へ展開できます。
- Shutdown callback では fatal error の回収、framework notice の表示、admin-only helper output の追加が行われる場合があります。

## 関連 documents

- `asset/docs/skeleton/entry-point.ja.md`
- `asset/docs/new-world/new-world.ja.md`
- `asset/docs/unit/app-unit.ja.md`
- `asset/docs/core/error-handling.ja.md`
