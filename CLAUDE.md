# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build & Run

The solution is `PlainFiles.slnx` (the new XML solution format) with two projects targeting **.NET 10**:

- `Core/Core.csproj` — class library, depends on `CsvHelper` 33.1.0
- `UI/UI.csproj` — console `Exe`, references `Core`

```bash
dotnet build PlainFiles.slnx
dotnet run --project UI
```

There is no test project. There is no linter beyond the compiler (`Nullable` and `ImplicitUsings` are enabled in both csprojs).

## Architecture

This is a teaching codebase. `UI/Program.cs` is a top-level-statements file that contains **three numbered examples** (`EXAMPLE 1`, `EXAMPLE 2`, `EXAMPLE 3`) — all but one are commented out. To demonstrate a different example, comment out the active one and uncomment the target. The currently active example is the one that compiles; do not assume the commented blocks are dead code to delete.

The progression maps directly to the commit history and is the point of the repo:

1. `SimpleTextFile` + `LogWriter` — raw line-by-line file IO with `File.ReadAllLines` / `File.WriteAllLines`, plus an `IDisposable` append-mode log writer.
2. `ManualCsvHelper` — hand-rolled CSV using `string.Join(",")` and `string.Split(',')` over `List<string[]>`. Note: in the global namespace (no `namespace Core;`), unlike its siblings.
3. `CsvHelperExample` + `Person` — same task using the `CsvHelper` NuGet package with `WriteRecords` / `GetRecords<T>` against a strongly-typed POCO.

The examples assume `c:\tmp\` exists for output files (`animals.txt`, `app.log`, `people.csv`, `people2.csv`). `SimpleTextFile` creates missing directories on its own; the CSV helpers do not.

### Known issue on the current branch

The branch `fix-bug-with-CsvHelper` exists because `CsvHelperExample.Read` returns `cr.GetRecords<Person>()` while the `StreamReader`/`CsvReader` are inside a `using` — `GetRecords` is lazy, so the reader is disposed before enumeration. Materialize with `.ToList()` (or restructure) when touching that method.
