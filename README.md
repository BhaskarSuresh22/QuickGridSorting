# QuickGridSorting

This repository contains a sample application and test evidence used to validate
QuickGrid sorting through the URL for ASP.NET Core issue #69127. The sample lets
QuickGrid manage its own sort query state without custom URL generation or
query-string parsing.

## Repository Structure

### QuickGridSortingSample

A .NET 11 Blazor Web App containing one reusable QuickGrid over 60 deterministic
in-memory items. The grid has sortable `Name` and `Price` columns, uses `Price`
as its default sort column, and includes a sortable column without an explicit
`Title`.

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
- A browser with a normal and private window, or two separate browser profiles

## Run the Sample

From the `QuickGridSortingSample` directory:

```powershell
dotnet restore QuickGridSortingSample.slnx
dotnet run --project QuickGridSortingSample
```

## Issue

[#69127: QuickGrid sorting through the URL](https://github.com/dotnet/aspnetcore/issues/69127).