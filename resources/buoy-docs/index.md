---
title: "LogKit"
summary: "Format escaped text records and write severity-filtered bounded files with one backup generation."
category: "Overview"
order: 0
keywords: ["logging", "file", "severity", "rotation"]
related: ["getting-started.md", "api.md", "typed-api.md"]
---

# LogKit

LogKit is a pure Buoy synchronous file logger for applications that need an explicitly owned log destination. It provides escaped single-line records, increasing severity levels, filtering, and one numbered backup file. Import and construction do not touch the filesystem.

## Choosing a destination

Use the built-in System.Log for OS/stderr diagnostics when no application-owned file is needed. For FileLogger, select an ordinary file with an existing parent directory and give that file and its .1 backup exclusively to one writer.

There is no thread/process locking, symlink defense, transactional rotation, or fsync durability guarantee. Rotation may leave a partial backup or an active file that was created/truncated before an error. Errors are reported; rollback is a host policy.

## Record format and provenance

Records contain caller-supplied timestamp, severity, subsystem, and message separated by tabs with an LF terminator. Controls and backslashes are escaped; ordinary Unicode is preserved. This newly written implementation was informed by LoggingClass, but it does not implement syslog wire format, remote logging, asynchronous queues, date retention, or a viewer.

## Read next

- [Overview](index.md)
- [Getting started](getting-started.md)
- [API reference](api.md)
