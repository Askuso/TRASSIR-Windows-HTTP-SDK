# Template App TRASSIR Windows HTTP SDK

![Zabbix](https://img.shields.io/badge/Zabbix-7.2%2B-red)
![TRASSIR](https://img.shields.io/badge/TRASSIR-Windows%20Server-blue)
![Release](https://img.shields.io/badge/Release-v4.1.0-brightgreen)
![Status](https://img.shields.io/badge/Status-Stable-success)
![License](https://img.shields.io/badge/License-MIT-green)

English | [Русский](README_RU.md)

## Overview

**Template App TRASSIR Windows HTTP SDK** is a Zabbix template for monitoring TRASSIR Server installations running on Windows through the built-in HTTP SDK. It does not require external scripts, SNMP, or a Zabbix agent on the monitored server.

The template monitors TRASSIR health, local cameras, archive retention, archive disks, recording state, stream performance, and system status. It includes low-level discovery, triggers, graph prototypes, calculated archive-baseline items, and a ready-to-use dashboard.

This repository is for TRASSIR Server on Windows only. Hardware TRASSIR recorders use a separate template because their HTTP SDK health semantics and available system fields differ.

## Features

- Monitoring through the built-in TRASSIR HTTP SDK.
- No external scripts or Zabbix agent required.
- Monitoring through Zabbix Server or Zabbix Proxy.
- Automatic discovery of local cameras.
- Automatic discovery of archive disks.
- Direct camera online/offline state from the TRASSIR signal flag.
- Per-camera FPS, bitrate, recording, archive, and diagnostic metrics.
- Archive-retention baseline based on the rolling seven-day average.
- Human-readable camera names in visible names and tags.
- Optional comma-separated disk trigger exclusion list.
- Built-in dashboard.

## Compatibility

| Component | Support |
|---|---|
| Zabbix | 7.2 or newer |
| TRASSIR Server on Windows | Supported |
| HTTP SDK over HTTPS | Supported |
| HTTP SDK over HTTP | Supported |
| Monitoring through Zabbix Proxy | Supported |
| Local TRASSIR channels | Supported |
| Remote NetworkNode channels | Excluded from local LLD |
| Hardware TRASSIR NVR | Not supported by this template |

The HTTP SDK field set can differ between TRASSIR versions. Unsupported optional fields remain empty or display `Not supported` without stopping the main poll.

## Monitored data

### Server

- HTTP SDK availability.
- CPU utilization.
- Uptime.
- Database state.
- Network subsystem state.
- Automation subsystem state.
- Overall disk subsystem state.
- Aggregated polling errors.
- Total, online, and offline local cameras.
- Camera availability percentage.
- Overall main, private, and substream archive retention.
- Complete main archive days.
- Seven-day rolling archive average.
- Current archive retention relative to the seven-day average.

All visible subsystem status values use the normalized format:

```text
-1 = No data
 0 = Error
 1 = OK
```

The Windows-specific network value is normalized during preprocessing so that network, database, automation, and disk status use the same logic.

### Cameras

Low-level discovery creates items for every local channel:

- Online/offline state.
- Video signal state.
- Main-stream FPS.
- Substream FPS.
- Main-stream bitrate.
- Substream bitrate.
- Recording bitrate.
- Local recording state.
- Hardware recording state.
- Recording debt.
- Last recording error.
- Grabber error.
- Bitrate exceeded flag.
- Slow archive merge flag.
- Main archive retention.
- Substream archive retention.
- Daily archive consumption.
- Forecast archive consumption.

The camera GUID is used as the stable technical identifier. The camera name is used in visible item names, trigger names, graph names, and tags.

The camera offline trigger has `DISASTER` severity. The video-signal trigger depends on the offline trigger to avoid duplicate problems.

### Archive disks

- Disk identifier.
- Model and serial number.
- Capacity.
- Archive retention.
- Enabled state.
- Read-only state.
- Unavailable state.
- Unusable state.
- Formatting-required state.
- Current speed.
- Read speed.
- Write speed.
- Error counter.
- Last SDK error.

## Authentication

Set the HTTP SDK password in the host macro:

```text
{$TRASSIR.SDK.PASSWORD}
```

The SDK password is configured in TRASSIR WebServer settings and may differ from an operator account password.

The template uses the host interface address as the SDK destination. The Agent interface may be used only as an address holder; no agent is installed on the TRASSIR server.

## Requirements

- Zabbix 7.2 or newer.
- TRASSIR Server running on Windows.
- HTTP SDK enabled.
- SDK settings read permission.
- SDK object-tree read permission.
- Network access from the assigned Zabbix Server or Proxy to the SDK port.
- Correct SDK password.

## Installation

1. Download the XML template from the latest GitHub release.
2. In Zabbix, open **Data collection → Templates → Import**.
3. Import the XML file.
4. Create a host for the TRASSIR Windows server.
5. Add an Agent interface containing the server IP address or DNS name. The interface is used only as the HTTP SDK destination.
6. Link `Template App TRASSIR Windows HTTP SDK`.
7. Configure the required host macros.
8. Wait for the aggregated master items and low-level discovery rules to run.

Detailed instructions: [Installation](docs/INSTALL.md) and [Configuration](docs/CONFIGURATION.md).

## Macros

| Macro | Default | Description |
|---|---:|---|
| `{$TRASSIR.SCHEME}` | `https` | HTTP SDK scheme. |
| `{$TRASSIR.PORT}` | `8080` | HTTP SDK port. Override it per host. |
| `{$TRASSIR.SDK.PASSWORD}` | empty | Required HTTP SDK password. |
| `{$TRASSIR.POLL.INTERVAL}` | `2m` | Health and object-tree polling interval. |
| `{$TRASSIR.CHANNEL.POLL.INTERVAL}` | `5m` | Per-channel polling interval. |
| `{$TRASSIR.DISK.POLL.INTERVAL}` | `5m` | Archive-disk polling interval. |
| `{$TRASSIR.CPU.WARN}` | `85` | CPU warning threshold, percent. |
| `{$TRASSIR.RECORD.DEBT.WARN}` | `5` | Recording-debt threshold, seconds. |
| `{$TRASSIR.ARCHIVE.MIN.DAYS}` | `7` | Minimum overall main archive retention. |
| `{$TRASSIR.CHANNEL.ARCHIVE.MIN.DAYS}` | `7` | Minimum per-camera archive retention. |
| `{$TRASSIR.DISK.EXCLUDE}` | empty | Comma-separated disk IDs whose trigger problems are suppressed. |

## Disk exclusions

Disks that are not used for archive recording can be excluded from trigger problems:

```text
{$TRASSIR.DISK.EXCLUDE}=C,D,R
```

Spaces and letter case are ignored. Data collection and graphs remain enabled for excluded disks.

After changing the macro, execute the archive-disk discovery rule or wait for the next discovery cycle.

## Main triggers

- High CPU utilization.
- Database status error.
- Overall disk subsystem error.
- Network status error.
- Automation subsystem error.
- No aggregate data from the SDK.
- Aggregated polling errors.
- One or more local cameras offline.
- Discovered camera offline.
- Camera video-signal loss.
- Recording delay or recording error.
- Bitrate limit exceeded.
- Slow archive merge.
- Overall archive retention below the configured threshold.
- Per-camera archive retention below the configured threshold.
- Archive retention below 90% or 80% of the rolling seven-day average.
- Archive disk disabled, unavailable, unusable, read-only, or requiring formatting.
- New archive-disk errors.

## Archive baseline

Archive retention depends on disk capacity, camera count, bitrate, recording mode, and scene activity. The dashboard therefore compares current main archive retention with its rolling seven-day average.

The template calculates:

- Current main archive retention.
- Complete archive days.
- Rolling seven-day average.
- Current retention as a percentage of the seven-day average.

Dashboard interpretation:

```text
90–100%   Normal
80–89.99% Warning
Below 80% Critical decrease
```

A representative baseline requires sufficient collected history.

## Dashboard

The included dashboard provides:

- Server and subsystem status.
- Online and total camera summary.
- Camera availability gauge.
- CPU utilization.
- Current archive retention and complete archive days.
- Seven-day archive average.
- Archive-retention baseline gauge.
- Active problems.
- Archive, camera, and disk graphs.

The dashboard is available in the host/template context. It is not created as a global dashboard under **Monitoring → Dashboards**.

See [Dashboard documentation](docs/DASHBOARD.md).

## Troubleshooting

- Check the latest value of `System: RAW - health and local channels`.
- Check `System: RAW - aggregate polling errors`.
- Check discovered `Camera: ...: RAW` items.
- Verify `{$TRASSIR.SCHEME}`, `{$TRASSIR.PORT}`, and `{$TRASSIR.SDK.PASSWORD}`.
- Verify that the host is monitored by the correct Zabbix Proxy.
- After changing `{$TRASSIR.DISK.EXCLUDE}`, execute archive-disk discovery.
- If TRASSIR activates `sessionbomb protection`, remove duplicate polling templates or increase intervals.

See [FAQ](docs/FAQ.md).

## Known limitations

- This template is not intended for hardware TRASSIR recorders.
- Only local channels are discovered.
- Hardware recording state is not returned by every camera or connection method.
- Memory utilization is not included because it was not consistently available in the tested Windows HTTP SDK responses.
- The archive baseline becomes representative only after sufficient history is collected.
- Self-signed HTTPS certificates are common; TLS handling depends on the Zabbix Server or Proxy configuration.

## Upgrade

Import the new XML over the existing template. Existing host links and host-level macro overrides are preserved. Run the master items and discovery rules after import and review active problems.

## Changelog

See [CHANGELOG.md](CHANGELOG.md).

## Author

Askuso

## License

MIT
