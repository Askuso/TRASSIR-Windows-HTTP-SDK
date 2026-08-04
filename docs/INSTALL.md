# Installation

## 1. Configure TRASSIR

1. Open TRASSIR WebServer/SDK settings.
2. Enable HTTP SDK.
3. Enable SDK settings read access.
4. Enable SDK object-tree read access.
5. Note the SDK port and SDK password.
6. Confirm that the Windows server is reachable from the Zabbix Server or assigned Proxy.

The SDK password may differ from an operator account password.

## 2. Import the template

1. Open **Data collection → Templates → Import**.
2. Select `templates/7.2/template_app_trassir_windows_http_sdk.xml` or the XML attached to the latest release.
3. Import all template objects.
4. Confirm that `Template App TRASSIR Windows HTTP SDK` appears in the template list.

## 3. Create the host

1. Create one Zabbix host per TRASSIR Windows server.
2. Add an Agent interface containing the TRASSIR server IP address or DNS name.
3. Select the Zabbix Proxy when required.
4. Link `Template App TRASSIR Windows HTTP SDK`.
5. Set the required host macros:

```text
{$TRASSIR.SCHEME}=https
{$TRASSIR.PORT}=8080
{$TRASSIR.SDK.PASSWORD}=your_sdk_password
```

The Agent interface is used only as the destination address. No Zabbix agent is installed on the TRASSIR server.

## 4. Optional disk exclusions

For disks that are not used for archive recording:

```text
{$TRASSIR.DISK.EXCLUDE}=C,D,R
```

Metrics and graphs remain enabled; only disk trigger problems are suppressed.

## 5. Validate collection

Check these items first:

- `System: RAW - health and local channels`
- `Storage: RAW - archive disks`
- `System: RAW - aggregate polling errors`

Then verify that channel and archive-disk discovery rules create resources.

If immediate discovery is required, execute the RAW items and both LLD rules manually.

## 6. Validate the dashboard

Open the host/template dashboard and verify:

- System status cards show `OK`.
- Camera total and online values are populated.
- CPU and archive gauges receive values.
- Camera and disk graphs are created.
- Excluded disks do not generate problems after disk LLD refreshes.

## Upgrade

Import the new XML over the existing template. Host links and host-level macro overrides are preserved. Execute the master items and discovery rules after import, then review active problems and the dashboard.
