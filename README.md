# Rotating file logs

A source-only file-logging package inspired by [Mike Cotrone's LoggingClass](https://github.com/mikecotrone/LoggingClass).
`Import logkit` provides escaped records, severity filtering and one bounded backup
file. The built-in `System.Log` already sends diagnostics to the OS and stderr;
use it directly when you do not need an application-owned log file.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add logkit --git https://github.com/BuoyBay/logkit.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## Example

```basic
Import logkit

Sub Main(args As Array Of String)
    If Array.Count(args) <> 2 Then
        Print FormatLogRecord("2026-10-31T12:00:00Z", LogSeverity.Info, "demo", "Formatted example only")
        Return
    End If
    Dim logger As New FileLogger(args(1), 1048576, LogSeverity.Info)
    Dim written As Result Of Boolean, String = logger.Write("2026-10-31T12:00:00Z", LogSeverity.Info, "demo", "Started\nwith escaped newline")
    If written.IsErr Then
        Print written.Error
    Else
        Print "Written: " + String.From(written.Value)
    End If
End Sub
```

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer logkit
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```


## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
