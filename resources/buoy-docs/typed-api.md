---
title: "logkit typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["FileLogger", "LogSeverity"]
dependencies: []
symbols: ["FileLogger", "LogSeverity"]
related: ["api.md", "getting-started.md", "index.md"]
---

# logkit typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## LogSeverity

`Public Enum LogSeverity As Integer`

Severities follow Buoy System.LogLevels in increasing importance.

### Values

| Name | Type | Value / Default | Description |
| --- | --- | --- | --- |
| `Debug` | `LogSeverity` | `0` | — |
| `Info` | `LogSeverity` | `1` | — |
| `Warning` | `LogSeverity` | `2` | — |
| `Error` | `LogSeverity` | `3` | — |
| `Critical` | `LogSeverity` | `4` | — |

## LogKit functions

### FormatLogRecord

`Public Function FormatLogRecord(timestamp As String, level As LogSeverity, subsystem As String, message As String) As String`

Return exactly one LF-terminated tab-separated record, escaping field controls.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `timestamp` | `String` | `ByVal` | Required |
| `level` | `LogSeverity` | `ByVal` | Required |
| `subsystem` | `String` | `ByVal` | Required |
| `message` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `String` |

## FileLogger

`Public Class FileLogger`

Synchronous single-writer bounded file logger with one .1 backup.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `FilePath` | `String` | Yes | No | — | — |
| `MaxBytes` | `Integer` | Yes | No | — | — |
| `MinimumLevel` | `LogSeverity` | No | No | — | — |

### Constructor

`Public Sub Constructor(filePath As String, maxBytes As Integer = 1048576, minimumLevel As LogSeverity = LogSeverity.Info)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `filePath` | `String` | `ByVal` | Required |
| `maxBytes` | `Integer` | `ByVal` | `1048576` |
| `minimumLevel` | `LogSeverity` | `ByVal` | `LogSeverity.Info` |

Returns: no value.

### Write

`Public Function Write(timestamp As String, level As LogSeverity, subsystem As String, message As String) As Result Of Boolean, String`

Ok(True) written, Ok(False) filtered, Err(message) filesystem/size failure.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `timestamp` | `String` | `ByVal` | Required |
| `level` | `LogSeverity` | `ByVal` | Required |
| `subsystem` | `String` | `ByVal` | Required |
| `message` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Result Of Boolean, String` |
