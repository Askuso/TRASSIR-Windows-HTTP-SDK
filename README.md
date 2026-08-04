# TRASSIR Windows Server by HTTP SDK

## Documentation

- [English technical documentation](docs/TRASSIR_Windows_HTTP_SDK_Technical_Documentation_EN.docx)
- [Russian README](README_RU.md)

## Overview

This template monitors TRASSIR Server installations running on Windows through the built-in HTTP SDK without external scripts or a Zabbix agent.

It collects:

- TRASSIR health, CPU utilization, uptime, database, storage, network, and automation status;
- overall local-camera availability;
- per-camera signal state, main/substream FPS and bitrate, recording status, recording debt, archive depth, archive consumption, forecast consumption, and diagnostic errors;
- archive disk model, serial number, capacity, availability, read-only state, error counter, and read/write throughput;
- current archive depth, complete archive days, a rolling seven-day average, and archive depth relative to that average.

Low-level discovery creates an independent SCRIPT master item for every local channel. A timeout or error on one channel does not interrupt polling of the other channels.

The template includes triggers, graph prototypes, calculated archive-baseline items, and an overview dashboard.

## Requirements

- Zabbix server 7.2 or newer.
- TRASSIR Server running on Windows with HTTP SDK enabled.
- Network access from the Zabbix server or proxy to the TRASSIR HTTP SDK port.
- An SDK password with permission to read settings and the object tree.
- The host interface address must resolve to the TRASSIR server IP address or DNS name.

## Tested versions

The template was developed and validated with:

- Zabbix 7.2.2;
- TRASSIR Server on Windows;
- monitoring through the built-in TRASSIR HTTP SDK.

Other Windows Server and TRASSIR versions may expose different SDK fields. Feedback and test results are welcome.

## Setup

### TRASSIR configuration

1. Open the TRASSIR WebServer/SDK settings.
2. Enable the HTTP/Web SDK.
3. Create or select a technical monitoring account.
4. Grant read access to settings and the object tree.
5. Note the HTTP SDK port and SDK password.
6. Make sure the TRASSIR server is reachable from the Zabbix server or assigned proxy.

### Zabbix configuration

1. Import `templates/7.2/template_app_trassir_windows_http_sdk.xml`.
2. Create a host for the TRASSIR Windows server.
3. Set the host interface address to the TRASSIR server IP address or DNS name.
4. Link the template `Template App TRASSIR Windows HTTP SDK`.
5. Configure the required macros on the host.
6. Wait for the master items and low-level discovery rules to run.

## Macros

| Macro | Default | Description |
|---|---:|---|
| `{$TRASSIR.SCHEME}` | `https` | HTTP SDK connection scheme. |
| `{$TRASSIR.PORT}` | `8080` | HTTP SDK port. |
| `{$TRASSIR.SDK.PASSWORD}` | empty | Required SDK password. |
| `{$TRASSIR.POLL.INTERVAL}` | `2m` | Health and object-tree polling interval. |
| `{$TRASSIR.CHANNEL.POLL.INTERVAL}` | `5m` | Polling interval for every discovered channel. |
| `{$TRASSIR.DISK.POLL.INTERVAL}` | `5m` | Archive-disk polling interval. |
| `{$TRASSIR.CPU.WARN}` | `85` | CPU utilization warning threshold, percent. |
| `{$TRASSIR.RECORD.DEBT.WARN}` | `5` | Recording-debt threshold, seconds. |
| `{$TRASSIR.ARCHIVE.MIN.DAYS}` | `7` | Minimum overall main archive depth, days. |
| `{$TRASSIR.CHANNEL.ARCHIVE.MIN.DAYS}` | `7` | Minimum per-camera archive depth, days. |
| `{$TRASSIR.DISK.EXCLUDE}` | empty | Comma-separated disk IDs whose trigger problems must be suppressed. |

## Discovery rules

### Channel discovery

Discovers only local TRASSIR channels and creates a separate SCRIPT master item for every channel with dependent items for:

- direct online/signal state;
- main-stream and substream FPS;
- main-stream, substream, and recording bitrate;
- local and hardware recording state;
- recording debt and last recording error;
- main and substream archive depth;
- daily archive consumption and forecast consumption;
- grabber error, bitrate-limit state, and slow archive merge state.

The camera GUID is used as the stable technical identifier. The camera name is used in visible names and tags.

### Archive disk discovery

Creates items for disk inventory, state, capacity, archive depth, error counters, and throughput.

Disks not used for archive recording can be excluded from trigger problems with:

`{$TRASSIR.DISK.EXCLUDE}=C,D,R`

Data collection and graphs remain enabled for excluded disks.

## Main triggers

- High CPU utilization.
- Database status error.
- Disk subsystem status error.
- Network status error.
- Automation status error.
- No aggregate data from the SDK.
- Aggregate polling errors.
- Offline local cameras.
- Per-camera offline state and video-signal loss.
- Recording delay, recording error, bitrate excess, and slow archive merge.
- Archive depth below configured thresholds or below the rolling seven-day baseline.
- Archive disk disabled, unavailable, unusable, read-only, formatting required, or reporting new errors.

## Dashboard

The included dashboard provides:

- server and subsystem status cards;
- online/total camera summary;
- camera availability gauge;
- CPU gauge;
- current archive depth and complete archive days;
- seven-day archive average and archive-relative-to-average gauge;
- active problems;
- archive, camera, and disk graphs.

The dashboard is available in the host/template context. It is not created as a global dashboard under **Monitoring -> Dashboards**.

## Known limitations

- The template is intended for TRASSIR Server on Windows. Hardware NVRs require a separate template because their health semantics and available system metrics differ.
- Only local channels are discovered. Remote NetworkNode channels are excluded.
- Hardware recording state is not returned by every camera or connection method. Unsupported cameras show `Not supported`.
- Memory usage is not included because it was not consistently available in the tested Windows HTTP SDK response.
- The rolling archive baseline becomes representative after sufficient history has accumulated.
- Self-signed HTTPS certificates are common; TLS handling depends on the Zabbix server or proxy configuration.

## Troubleshooting

- Check the latest value of `System: RAW - health and local channels`.
- Check `System: RAW - aggregate polling errors`.
- Check the discovered `Camera: ...: RAW` items.
- Verify `{$TRASSIR.SCHEME}`, `{$TRASSIR.PORT}`, and `{$TRASSIR.SDK.PASSWORD}`.
- Verify that the host is monitored by the correct Zabbix proxy.
- If TRASSIR returns `sessionbomb protection`, remove duplicate templates or increase polling intervals.
- After changing `{$TRASSIR.DISK.EXCLUDE}`, execute the archive-disk discovery rule.

## Author

Askuso

## License

MIT
