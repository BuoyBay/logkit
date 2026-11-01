---
title: "LogKit API"
summary: "Public types, functions, validation, and ownership rules for LogKit."
category: "Reference"
order: 20
keywords: ["logging", "file", "severity", "rotation"]
symbols: ["LogSeverity", "FormatLogRecord", "FileLogger", "FileLogger.Write", "FileLogger.MinimumLevel", "FileLogger.FilePath", "FileLogger.MaxBytes"]
related: ["index.md", "getting-started.md", "typed-api.md"]
---

# LogKit API

| API | Contract |
| --- | --- |
| LogSeverity | Debug 0, Info 1, Warning 2, Error 3, Critical 4; matches increasing System.LogLevels order. |
| FormatLogRecord(timestamp, level, subsystem, message) | Escaped tab-separated text with one terminating LF; caller supplies timestamp text. |
| FileLogger(filePath, maxBytes, minimumLevel) | Inert configuration; defaults 1048576 bytes and Info. |
| FilePath, MaxBytes | Get-only destination and size bound. |
| MinimumLevel | Validated writable severity threshold. |
| Write(timestamp, level, subsystem, message) | Result Of Boolean, String: written, filtered, or size/filesystem error. |

## Bounds and framing

MaxBytes is 64 through 1073741824. Empty paths and invalid severities throw InvalidArgumentException. Writes reject directories and non-regular active/backup targets. Neither construction nor import deletes existing data.

The formatter escapes backslash, CR, LF, tab, C0 controls U+0001–U+001F, and DEL. Numeric escapes use decimal backslash-u-brace notation rather than JSON syntax. Timestamp text is escaped but is not date-validated and no clock is read.

Embedded NUL is outside the Buoy 0.72 String contract tested for this package. Do not use the formatter for binary data or binary paths. Successful generations are individually bounded, while pre-existing external files are never automatically trimmed.

## Read next

- [Overview](index.md)
- [Getting started](getting-started.md)
- [API reference](api.md)


[Full typed API: properties, parameters, defaults, and returns](typed-api.md)
