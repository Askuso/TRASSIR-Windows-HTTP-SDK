# Changelog

All notable changes to **Template App TRASSIR Windows HTTP SDK** are documented in this file.

## 4.1.0 — Initial public release

### Added

- Monitoring of TRASSIR Server on Windows through the built-in HTTP SDK.
- Aggregated health and object-tree master poll.
- Local camera low-level discovery.
- Archive disk low-level discovery.
- Direct camera online/offline status from the TRASSIR signal flag.
- Per-camera FPS, bitrate, recording, archive, and diagnostic metrics.
- Per-disk inventory, state, performance, archive, and error metrics.
- Camera availability counters and percentage.
- Complete archive-day calculation.
- Rolling seven-day archive average.
- Archive retention relative to the seven-day baseline.
- Built-in host/template dashboard.
- Human-readable camera tags.
- Comma-separated disk trigger exclusion macro.
- English and Russian documentation.

### Changed

- System status values normalized to `1 = OK`, `0 = Error`, `-1 = No data`.
- Windows network status normalized during preprocessing.
- Camera polling interval set to 5 minutes.
- Camera offline trigger severity set to `DISASTER`.
- Video-signal trigger made dependent on the camera offline trigger.
- Lost LLD resources disabled after 1 hour and deleted after 30 days.

### Fixed

- Incorrect network status interpretation.
- Incorrect overall disk status value mapping.
- Hardware recording handling for cameras that do not support the field.
- Disk exclusion logic for existing discovered trigger prototypes.
- Camera names in tags and visible object names.
- Dashboard status-card value maps and threshold colors.
