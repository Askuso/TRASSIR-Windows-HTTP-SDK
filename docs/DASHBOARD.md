# Dashboard

The template includes a ready-to-use dashboard for one TRASSIR Windows server.

## Main sections

- Server and subsystem status.
- Online and total camera summary.
- Camera availability gauge.
- CPU utilization.
- Current main archive retention.
- Complete archive days.
- Seven-day archive average.
- Archive retention relative to the seven-day baseline.
- Active problems.
- Camera, archive, and disk graphs.

## Interpretation

### Camera availability

The gauge is calculated from online and total local-camera counters.

- 100% means all discovered local cameras are online.
- A lower value means one or more cameras are unavailable.

### Archive baseline

Archive indication uses inverse operational logic: a lower percentage is worse.

```text
90–100%   Normal
80–89.99% Warning
Below 80% Critical decrease
```

The percentage compares current main archive retention with the rolling seven-day average. Review the percentage together with current complete archive days and the seven-day average.

### Status cards

Subsystem status cards use normalized values:

```text
0 = Error
1 = OK
```

`No data` means the required SDK value has not been received.

## Host dashboard location

The dashboard belongs to the template and host context. It is not created as a global dashboard under **Monitoring → Dashboards**.

Open the monitored host and select its dashboard.

## Missing or empty widgets

A widget can remain empty when:

- the first master poll has not completed;
- the relevant SDK field is absent;
- the calculated item has insufficient history;
- a discovered resource has been marked lost by LLD;
- the host is monitored through a different Proxy than expected.

Check the RAW master items and Latest data before changing the dashboard configuration.
