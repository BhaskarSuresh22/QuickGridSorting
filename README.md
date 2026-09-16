# QuickGridSorting

This repository contains a sample application and test evidence used to validate
QuickGrid sorting through the URL for ASP.NET Core issue #69127. The sample lets
QuickGrid manage its own sort query state without custom URL generation or
query-string parsing.

## Repository Structure

### QuickGridSortingSample

A .NET 11 Blazor Web App containing one reusable QuickGrid over 60 deterministic
in-memory items. The grid has sortable `Name`, `Price`, and `ItemNumber` columns,
uses `Price` as its default sort column, and lets the `ItemNumber` column infer
its title because it has no explicit `Title`.

The sample provides three routes for testing the supported render modes:

| Render mode | Route |
|---|---|
| Static SSR | `/quickgrid/static` |
| Interactive Server | `/quickgrid/server` |
| Interactive WebAssembly | `/quickgrid/webassembly` |

### Evidence

Contains screenshots and recordings collected while validating:

- Default `Price` sorting and ascending/descending `Name` sorting.
- Sort state represented in copied URLs across all three render modes.
- Static SSR header markup and server-rendered navigation requests.
- Query-parameter removal and browser Back/Forward navigation.
- Middle-click and keyboard activation of sortable headers.
- Rendering and sorting behavior of the column without an explicit `Title`.
- Malformed sort query validation.

## Requirements

- .NET SDK `11.0.100-rc.1.26431.118`.
- Microsoft Edge or another browser with a private-browsing mode.
- The public .NET 11 package feed configured in the included `NuGet.config`.

## Run the Sample

Open PowerShell in the repository root, where `QuickGridSortingSample.slnx` is
located, and run:

```powershell
dotnet --version
dotnet restore .\QuickGridSortingSample.slnx --force --no-cache
dotnet build .\QuickGridSortingSample.slnx --no-restore
dotnet watch --project .\QuickGridSortingSample\QuickGridSortingSample.csproj run --launch-profile https --no-restore
```

`dotnet --version` must return `11.0.100-rc.1.26431.118`. The `https` launch
profile has `launchBrowser` enabled, so `dotnet watch` should start the app and
open the browser automatically. Keep this terminal running while testing.

If the browser does not open automatically, leave the app running and execute
this command in a second PowerShell terminal:

```powershell
Start-Process "https://localhost:7181"
```

To stop the application, return to the running terminal and press `Ctrl+C`.

## Expected Launch URL

The included HTTPS launch profile uses:

```text
https://localhost:7181
```

The application opens on the Home page. A development-certificate warning may
appear on the first launch; trust or accept the local certificate before
collecting browser evidence.

## Test Routes

| Render mode | URL |
|---|---|
| Static SSR | `https://localhost:7181/quickgrid/static` |
| Interactive Server | `https://localhost:7181/quickgrid/server` |
| Interactive WebAssembly | `https://localhost:7181/quickgrid/webassembly` |

On each route, verify the default `Price` sort; sort `Name` ascending and
descending; then sort the inferred-title `ItemNumber` column ascending and
descending. Confirm that the URL (`sort=ItemNumber`) and first three rows change
together when sorting by `ItemNumber`.

## Malformed URL Cases

Test the four malformed query combinations in all three render modes:

| # | Render mode | Case | Complete URL |
|---:|---|---|---|
| 1 | Static SSR | Unknown column | `https://localhost:7181/quickgrid/static?sort=Unknown&direction=asc` |
| 2 | Static SSR | Invalid direction | `https://localhost:7181/quickgrid/static?sort=Name&direction=invalid` |
| 3 | Static SSR | Empty direction | `https://localhost:7181/quickgrid/static?sort=Name&direction=` |
| 4 | Static SSR | Missing direction | `https://localhost:7181/quickgrid/static?sort=Name` |
| 5 | Interactive Server | Unknown column | `https://localhost:7181/quickgrid/server?sort=Unknown&direction=asc` |
| 6 | Interactive Server | Invalid direction | `https://localhost:7181/quickgrid/server?sort=Name&direction=invalid` |
| 7 | Interactive Server | Empty direction | `https://localhost:7181/quickgrid/server?sort=Name&direction=` |
| 8 | Interactive Server | Missing direction | `https://localhost:7181/quickgrid/server?sort=Name` |
| 9 | Interactive WebAssembly | Unknown column | `https://localhost:7181/quickgrid/webassembly?sort=Unknown&direction=asc` |
| 10 | Interactive WebAssembly | Invalid direction | `https://localhost:7181/quickgrid/webassembly?sort=Name&direction=invalid` |
| 11 | Interactive WebAssembly | Empty direction | `https://localhost:7181/quickgrid/webassembly?sort=Name&direction=` |
| 12 | Interactive WebAssembly | Missing direction | `https://localhost:7181/quickgrid/webassembly?sort=Name` |

### Where and How to Test

Use Microsoft Edge InPrivate for the fresh direct-load checks:

1. Keep the application terminal running.
2. Open a new InPrivate window with `Ctrl+Shift+N`.
3. Open DevTools with `F12`, select **Network**, enable **Preserve log**, and
	select the **Doc** filter.
4. Paste one complete malformed URL directly into the address bar and press
	`Enter`. Do not load the corresponding normal route first.
5. Select the document request and record its request URL, request method, HTTP
	status, and `content-type` response header.
6. Record the final address-bar URL, active sort indicator, and first three grid
	rows. Check the Console if an error occurs.
7. Repeat for all 12 URLs. A new InPrivate window for each URL provides the
	strongest isolation, but a new InPrivate tab with a direct URL load is
	sufficient when browser state is not retained by the sample.

For the unknown-column and invalid-direction cases, the required result is an
HTTP `200 OK` document response and fallback to the default ascending `Price`
sort. Its first three rows are expected to be:

| Name | Price | ItemNumber |
|---|---:|---:|
| Item 60 | $10.00 | 60 |
| Item 13 | $11.00 | 13 |
| Item 26 | $12.00 | 26 |

## Issue

[#69127: QuickGrid sorting through the URL](https://github.com/dotnet/aspnetcore/issues/69127).