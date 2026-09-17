# Validation — Virtualized QuickGrid scrolling and anchoring

Scenario: [dotnet/aspnetcore #69131](https://github.com/dotnet/aspnetcore/issues/69131)  
Manual: [dotnet/aspnetcore #68479](https://github.com/dotnet/aspnetcore/issues/68479)

## Build tested

```text
.NET SDK 11.0.100-rc.1.26425.128
QuickGrid 11.0.0-rc.1.26425.128
```

The SDK is pinned in `src/global.json`.

## Sample application

`src/VirtualizedQuickGridValidation/` — Blazor Web App with a shared QuickGrid validation page.

| Render mode | Route |
|---|---|
| Interactive Server | `/quickgrid/server` |
| Interactive WebAssembly | `/quickgrid/webassembly` |
| Interactive Auto | `/quickgrid/auto` |

The sample uses a virtualized QuickGrid with 100,000 generated rows and a delayed items provider. It supports:

- Initial positioning and programmatic scrolling
- Nearby and distant superseding jumps
- Prepending and appending rows
- Filtering the total row count
- Placeholder index validation
- Provider request logging
- Non-virtualized scrolling exception validation

Validation approach: This is a manual validation harness. Status messages describe completed actions but are not automated assertions. Results were determined from viewport measurements, rendered output, provider logs, browser console and host logs, and screen recordings.

## How to run

```powershell
cd src/VirtualizedQuickGridValidation
dotnet run --project VirtualizedQuickGridValidation
```

Open the HTTPS URL printed in the console and navigate to one of the routes above.

For Interactive Auto, use a fresh browser profile for the first Server-rendered visit. Revisit the page after the WebAssembly resources are cached to test the WebAssembly renderer.

## How to verify the build

```powershell
cd src/VirtualizedQuickGridValidation
dotnet build VirtualizedQuickGridValidation.slnx
```

Expected result:

```text
Build succeeded.
	0 Warning(s)
	0 Error(s)
```

`AnchorMode` and `ItemComparer` are experimental APIs and produce `ASP0030`. The Client project explicitly opts in to these APIs through its `NoWarn` property.

## Configurations tested

- Blazor Web App — Interactive Server
- Blazor Web App — Interactive WebAssembly
- Blazor Web App — Interactive Auto with Server renderer
- Blazor Web App — Interactive Auto with WebAssembly renderer

## Validation result

Initial positioning, programmatic scrolling, supersession, filtering, placeholders, append anchoring, resizing, zooming, and non-virtualized exception behavior worked as expected.

Three issues were observed:

- Server-rendered modes did not preserve the expected item after rows were prepended.
- Append and prepend operations produced duplicate provider requests in all tested renderers.
- The `ASP0030` help link redirected to a generic Bing page instead of diagnostic-specific guidance.

## Evidence

Captured screenshots are in `evidence/screenshots/`.

Screen recordings are in `evidence/videos/`.

Rendered virtualization markup is in `evidence/markup/`.

Build and host logs are stored directly under `evidence/`.

The complete validation report is in `test-report/ValidationReport.docx`.