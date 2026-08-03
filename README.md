# Template App TRASSIR Windows HTTP SDK

[Русская версия](README_RU.md)

Zabbix template for monitoring **TRASSIR Server running on Windows** through the built-in TRASSIR Web SDK API.

The template uses Zabbix Script items, JavaScript `HttpRequest`, dependent items, low-level discovery, calculated items, triggers, graphs, and a template dashboard. It does not require Zabbix Agent, SNMP, or external scripts.

> This template is intended for TRASSIR Server on Windows. Hardware TRASSIR recorders can return different health values and require a separate template.

## Features

- CPU, uptime, database, network, automation, and disk state.
- Automatic discovery of local cameras and archive disks.
- Direct camera online/offline state from TRASSIR.
- Per-camera FPS, bitrate, recording, recording debt, errors, archive retention, and archive usage.
- Per-disk capacity, model, serial number, performance, archive retention, errors, availability, read-only state, and formatting state.
- Camera availability summary.
- Archive retention baseline based on the rolling 7-day average.
- Built-in TRASSIR-inspired dashboard.
- Comma-separated disk exclusion list.

## Quick start

1. Import `template/Template_App_TRASSIR_Windows_HTTP_SDK_v4.1.0.xml`.
2. Link `Template App TRASSIR Windows HTTP SDK` to the TRASSIR Windows host.
3. Set `{$TRASSIR.SCHEME}`, `{$TRASSIR.PORT}`, and `{$TRASSIR.SDK.PASSWORD}`.
4. Wait for the master items and LLD, or execute them manually.
5. Open the host dashboard.

See [Installation](docs/INSTALL.md) and [Configuration](docs/CONFIGURATION.md).

## Default polling

- Health and local objects: 2 minutes.
- Cameras: 5 minutes.
- Archive disks: 5 minutes.
- Archive calculated items: 10 minutes.

## Disk exclusions

```text
{$TRASSIR.DISK.EXCLUDE}=C,D,R
```

Metrics and graphs remain available; trigger problems are suppressed.

## Limitations

- Local channels only.
- Hardware recording is not supported by every camera.
- RAM is not included in this Windows template.
- A stable archive baseline requires seven days.
- The dashboard is a host/template dashboard, not a global dashboard.

## License

MIT. See [LICENSE](LICENSE).

## Disclaimer

TRASSIR is a trademark of its respective owner. This is an independent community template.
