# LogKit 0.1.0

A pure Buoy file-logging package inspired by [Mike Cotrone's LoggingClass](https://github.com/mikecotrone/LoggingClass).
`Import logkit` provides escaped records, severity filtering and one bounded backup
file. Buoy's built-in `System.Log` already sends diagnostics to the OS and stderr;
use it directly when you do not need an application-owned log file.

## API

- `LogSeverity`: Debug (0), Info (1), Warning (2), Error (3), Critical (4), matching
  the ordering of Buoy's `System.LogLevels`.
- `FormatLogRecord(timestamp, level, subsystem, message) As String`: produces
  `timestamp<TAB>LEVEL<TAB>subsystem<TAB>message<LF>`. Backslashes, CR, LF, tabs,
  C0 controls U+0001 through U+001F and DEL are escaped. Other Unicode is preserved. Numeric control
  escapes use decimal `\u{n}`; this is a log format, not JSON. The timestamp is
  caller-supplied text; this function neither reads the clock nor validates dates.
- `New FileLogger(filePath, maxBytes = 1048576, minimumLevel = LogSeverity.Info)`:
  stores configuration without touching the filesystem. Bounds are 64 bytes to
  1 GiB. Empty paths and invalid severity values throw InvalidArgumentException.
- `logger.Write(timestamp, level, subsystem, message) As Result Of Boolean, String`:
  `Ok(True)` means one whole record written, `Ok(False)` means filtered out,
  `Err(message)` reports a size or filesystem failure. Invalid severities throw.
- `FilePath` and `MaxBytes` are read-only; `MinimumLevel` is validated and writable.

A filtered write does not create a file. Writes count UTF-8 bytes, including the
terminating LF, rather than characters. A record larger than the bound is rejected
before file mutation. Otherwise, records append until the next record would exceed
`maxBytes`. At that point the current file is copied to `<filePath>.1` and the
active file is replaced with the new record. Further rotations replace the same
backup. The logger opens and closes on each write and requires no Close method.

Embedded NUL is outside this package's String contract: Buoy 0.72.0's
`String.Chr(0)` produces an empty string on this host. Do not use this formatter
as a binary-data encoder or pass binary paths.

## Ownership and failure semantics

Give each logger an application-owned ordinary file path with an existing parent
folder. Paths and `.1` backups must belong exclusively to this single writer;
there is no thread/process locking, symlink defense or transactional rotation.
Multiple logger instances used sequentially read the current size each time.
Neither directories nor non-regular targets/backups are accepted. A pre-existing
active file larger than `maxBytes` is rejected without mutation, requiring an
explicit recovery decision. Neither import nor construction deletes existing data.

A backup copy can fail or leave a partial backup; a later file write can fail after
creating/truncating the active file. Errors are reported, but rollback, crash-safe
replacement and fsync durability are not promised. The two generations produced
by successful writes are individually bounded; pre-existing external files are
not automatically trimmed. Appending to an arbitrary non-log file is possible,
so the application is responsible for choosing the destination. Formatting builds
an entire record in memory before enforcing the file bound; bound caller inputs
separately when messages can be large or untrusted.

This intentionally does not implement remote syslog, facilities/priorities from
RFC 5424, asynchronous logging, structured JSON fields, date-based retention or a
viewer. The source is platform independent; runtime tests here execute on Linux.

## Build and verify

```sh
/usr/bin/buoy pkg seal . -o packages/logkit-0.1.0.buipkg
```

The runner checks all three sources on six desktop targets, seals a fresh package,
and executes the tests and console example in an isolated temporary source tree.
The test covers record escaping, Unicode, filtering, exact append/rotation,
reopening, UTF-8 byte bounds, oversized existing files and filesystem failures.
See the example (not included in this repository) and
[upstream mapping](../PROVENANCE.md). No network calls or system logging configuration
changes are made.

Test suites and fixtures are maintainer-local and are not part of this public repository.
