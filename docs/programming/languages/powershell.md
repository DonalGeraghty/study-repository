# PowerShell

PowerShell is a cross-platform automation shell and scripting language built around structured objects rather than plain-text pipelines. It is useful for repository checks, administration, CI tasks, and Windows-oriented workflows.

## Object Pipeline

Commands emit objects whose properties can be filtered, grouped, sorted, and passed to later commands:

```powershell
Get-ChildItem -File |
    Where-Object Extension -eq '.csv' |
    Select-Object Name, Length
```

Prefer object properties to parsing formatted display text. Formatting commands belong at the output boundary because they turn objects into presentation data.

## Reliable Scripts

- Use `[CmdletBinding()]` and a `param` block for an explicit interface.
- Set terminating-error behaviour intentionally and use `try`, `catch`, and `finally` where cleanup matters.
- Resolve paths with `Join-Path` and `$PSScriptRoot` instead of assuming the current directory.
- Use `-LiteralPath` when a path should not interpret wildcard characters.
- Write useful pipeline output and keep diagnostic chatter separate.
- Make repeated execution safe when the script may run locally and in CI.

## Data Validation

`Import-Csv` turns rows into objects, which makes schema and value checks readable. Validate headers before relying on properties, report file and row context, and fail the process with a non-zero exit code when CI must stop.

## Project Connections

`health-os` uses a PowerShell script and GitHub Actions to validate yearly CSV filenames, schemas, dates, and duplicate rows.

## Related Guides

- [GitHub Actions](../../platform-engineering/ci-cd/github-actions.md)
- [Testing](../../quality-engineering/testing.md)

Return to [Programming Languages](./README.md).
