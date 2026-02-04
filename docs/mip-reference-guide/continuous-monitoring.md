---
layout: default
title: Continuous Monitoring
parent: MIP Reference Guide
nav_order: 2
---

# Continuous Monitoring

Once MIP begins, the DFR's primary responsibility is monitoring
system health as invoices are generated. This involves checking
Datadog dashboards for performance metrics, watching DLQs for stuck
messages, and verifying invoice creation speed stays above critical
thresholds. Monitor continuously throughout MIP; early detection of
issues prevents downstream problems in the Zuora-sync and Finance
reporting.

## Table of Contents

- [Monitor App](#monitor-app): check assorted metrics across the
Invoicing system
- [Datadog Logs](#datadog-logs): support for Datadog access
- [Replication Lag](#replication-lag): historical note about corp replica use

---

## Monitor App

All Invoicing engineers should have access to the Monitor App.
Tag senior management in Slack channel `#ops-eom-supports` to
enable any required access. This is the same view that Finance
has, but isn't the absolute source of truth, as discussed in
the Slack channel `#invoicing-dev`. Track MIP progress by
auditing the following metrics:

**Datadog Dashboards**

1. **Assorted Invoicing Services**

   - Any remaining invoices on the MIP Dashboard
   - Invoice creation speed on the Invoice Service Usage Dashboard
     - Generally expect speed to slow down during North American business hours;
     current speed benchmark is 24.55 inv/s and Intacct sync is ~10/s,
     if it falls below 10 inv/s, that's _bad_ and worth investigating
     - Compare with Intacct: Indeed Invoices Created vs. Invoice Key Received
     - Errors on the Revenue Service Dashboard

2. **Invoice Creator App Only**

   - Streamlined dashboard: ICA At-A-Glance
   - Further investigation:  Invoice Creator Dashboard
   - All of the dead letter queues:
      - Invoice Groupings DLQ
      - V2 DLQ with Messages Waiting
      - V3 DLQ with Messages Waiting
   - Errors / Exception counts by method
   - Ensure that the queues drain before 3 AM Central Time

---

## Datadog Logs

Check the Invoice Creator deployment group and database product volume.
For more information about how to access metrics accessible via Datadog
logs, review the demos and FAQ from the Slack channel `#help-datadog` and
blog “Logging Is Dead; Long Live Logging! - Migrating From Kibana To Datadog.”

---

## Replication Lag

Invoicing used to monitor and point analytical questions to the corp replica,
but corp replicas are expensive to maintain, so Invoicing decommissioned it
(tracked in ICA-3690). Most of the database tables used for analytical
work and reporting are available in the Datalake, and remain listed as
as `moneydb`, not `invoicingdb`.

`financedb` is also moving away from using replication, but this team hasn't
decommissioned their replica yet. Monitor the replica-cluster On-Prem to
AWS Aurora Dashboard if needed. Direct questions about anything `financedb`
to Slack channel `#help-db-cluster`.

---
