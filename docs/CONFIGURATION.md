# Configuration

## Required host macros

| Macro | Example | Purpose |
|---|---|---|
| `{$TRASSIR.SCHEME}` | `https` | HTTP SDK scheme. |
| `{$TRASSIR.PORT}` | `8080` | HTTP SDK port for this server. |
| `{$TRASSIR.SDK.PASSWORD}` | `********` | Password configured in TRASSIR WebServer SDK settings. |

## Polling macros

| Macro | Default | Purpose |
|---|---:|---|
| `{$TRASSIR.POLL.INTERVAL}` | `2m` | Health and object-tree master poll. |
| `{$TRASSIR.CHANNEL.POLL.INTERVAL}` | `5m` | Per-camera poll. |
| `{$TRASSIR.DISK.POLL.INTERVAL}` | `5m` | Archive-disk poll. |

Avoid unnecessarily short intervals. Duplicate polling can activate TRASSIR session protection.

## Threshold macros

| Macro | Default | Purpose |
|---|---:|---|
| `{$TRASSIR.CPU.WARN}` | `85` | CPU warning threshold, percent. |
| `{$TRASSIR.RECORD.DEBT.WARN}` | `5` | Per-camera recording-debt threshold, seconds. |
| `{$TRASSIR.ARCHIVE.MIN.DAYS}` | `7` | Minimum overall main archive retention. |
| `{$TRASSIR.CHANNEL.ARCHIVE.MIN.DAYS}` | `7` | Minimum per-camera archive retention. |

## Disk exclusion macro

`{$TRASSIR.DISK.EXCLUDE}` accepts comma-separated disk IDs:

```text
C
C,D
C,D,R
```

Spaces and letter case are ignored. Excluded disks continue collecting metrics and graphs; disk trigger problems are suppressed.

After changing the macro, execute archive-disk discovery or wait for the next discovery cycle.

## Status normalization

Visible subsystem status items use:

```text
-1 = No data
 0 = Error
 1 = OK
```

TRASSIR Windows can return inverse semantics for the network field. The network item is normalized during preprocessing so that all status triggers use the same expression logic.

## Camera discovery

Only local channels of the current TRASSIR server are discovered. Remote NetworkNode channels are excluded.

The channel GUID is the stable technical identifier. The camera name is used in visible names and tags.

Default lost-resource behavior:

- Disable after 1 hour.
- Delete after 30 days.

## Camera status and dependencies

Camera availability is taken directly from:

```text
/settings/channels/{GUID}/flags/signal
```

The offline trigger uses `DISASTER` severity. The video-signal trigger depends on the offline trigger to prevent duplicate problems.

## Hardware recording

Not every camera returns the hardware recording flag:

```text
-1 = Not supported
 0 = Inactive
 1 = Active
```

`Not supported` is informational and does not create a trigger.

## Archive baseline

The template calculates:

- Current main archive retention.
- Complete archive days.
- Rolling seven-day average.
- Current retention as a percentage of the seven-day average, capped at 100%.

The relative archive triggers require the value to remain below the threshold for 30 minutes.
