# FAQ

## Does the template require Zabbix Agent?

No. Script items send HTTP SDK requests from Zabbix Server or Zabbix Proxy. The Agent interface is used only as the destination IP or DNS address.

## Why is the ZBX availability icon grey?

The icon represents Zabbix Agent availability, not HTTP SDK availability. Use the dashboard and RAW items to verify TRASSIR monitoring.

## Why are remote NetworkNode cameras missing?

The template intentionally discovers only local channels of the current TRASSIR server. Remote connected servers are excluded from local LLD.

## Why does hardware recording show `Not supported`?

Not every camera or connection method returns `recording_hw`.

```text
-1 = Not supported
 0 = Inactive
 1 = Active
```

This value is informational and does not create a trigger.

## Why does an old camera remain after removal?

LLD does not delete lost resources immediately. By default they are disabled after 1 hour and deleted after 30 days. This protects history from temporary SDK failures.

## How do I suppress problems for non-archive disks?

Set the host macro:

```text
{$TRASSIR.DISK.EXCLUDE}=C,D,R
```

Then execute archive-disk discovery or wait for the next cycle.

## Why is the archive percentage unstable after installation?

The indicator uses a rolling seven-day average. It becomes representative after sufficient history is collected.

## Why is RAM not monitored?

Memory utilization was not consistently available in the tested TRASSIR Windows HTTP SDK responses. Hardware recorders use a separate template with their own system metrics.

## Why does TRASSIR report `sessionbomb protection`?

The server is receiving too many concurrent or repeated SDK requests. Remove duplicate templates and research items, or increase polling intervals.

## Where is the dashboard?

It is a host/template dashboard. Open it from the monitored host. It is not created as a global dashboard under **Monitoring → Dashboards**.
