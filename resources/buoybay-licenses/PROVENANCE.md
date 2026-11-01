# LoggingClass source study

Source: [mikecotrone/LoggingClass](https://github.com/mikecotrone/LoggingClass),
commit `84904cf05045c588bf2c1788953d028e9541a13b` inspected on 2026-10-31.
The README, `LogModule.xojo_code` and BSD-3-Clause LICENSE were read.
The upstream LICENSE is included verbatim as `UPSTREAM-LICENSE.txt`.
LogKit is a fresh Buoy implementation; no upstream Xojo function bodies were
translated or included.

| Upstream behavior | Buoy study |
| --- | --- |
| `formatWriteLogResult` timestamp/level/subsystem/message | `FormatLogRecord`, with escaped tab-separated framing |
| `systemLog` facility dispatch and platform-specific handling | Existing `System.Log`; not duplicated or automatically invoked |
| `writeLogFile` custom application-owned text destination | `FileLogger.Write`, explicit Result and bounded one-backup rotation |
| `formatReadLogResult` spaced display formatting | Not implemented; presentation belongs to the app |
| Demo GUI and configurable constants | Console example and constructor configuration |

Upstream names its numbered levels facilities and filters against a configured
maximum. LogKit instead uses Buoy's increasing severity ordering and does not
claim syslog wire-format compatibility.
The selected gaps filled are explicit durable-file destination choice, single-line
escaping, filtering and predictable bounded retention. OS diagnostics already
exist in Buoy and remain separate. Rotation is best effort, synchronous and
single-writer; robust concurrent/crash-safe logging remains future package work.

A verified runtime edge: on the installed 0.72.0 Linux compiler/runtime,
`String.Len(String.Chr(0))` is zero and `x + String.Chr(0) + y` produces `xy`.
LogKit therefore promises escaped text records, not binary/NUL preservation.
