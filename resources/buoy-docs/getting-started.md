---
title: "LogKit getting started"
summary: "Configure and use LogKit with explicit input and ownership rules."
category: "Guides"
order: 10
keywords: ["logging", "file", "severity", "rotation"]
related: ["index.md", "api.md", "typed-api.md"]
---

# LogKit getting started

Copy logkit into packages and import it. This example accepts an explicit application-owned destination path. With no path it formats a record only. Supplying a path writes one record and can append/rotate an existing log, so select a dedicated destination with an existing parent folder.

```buoy
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

## Filtering and rotation

Severities increase from Debug through Critical. A level below MinimumLevel returns Ok(False) without creating a file. A successful write returns Ok(True). A size/filesystem failure returns Err(String); invalid severity values throw InvalidArgumentException.

MaxBytes counts the complete UTF-8 record, including its final LF. A too-large record rejects before file mutation. Otherwise, when the next record would exceed the bound, the current file is copied to path.1 and the active file starts with the new record. Further rotations replace that single backup.

## Ownership and recovery

The logger opens/closes per write and has no Close method. An existing active file above the configured bound rejects without mutation and needs an explicit recovery decision. Inputs are formatted in memory before the byte bound is enforced; separately bound large/untrusted messages.

## Read next

- [Overview](index.md)
- [Getting started](getting-started.md)
- [API reference](api.md)
