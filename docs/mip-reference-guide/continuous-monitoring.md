---
layout: default
title: Continuous Monitoring
parent: MIP Reference Guide
nav_order: 2
---

## Monitoring

**Monitor App**

All Invoicing engineers should have access to the Monitor App,
otherwise ask in `#ops-eom-supports` and tag senior management.
This is the same view that Finance has, but isn't the absolute
source of truth, as discussed in the Slack channel `#invoicing-dev`.
Audit the following metrics described below in order to
track MIP progress:

**Datadog Dashboards**

1. Assorted Invoicing Services

   - Any remaining invoices on the MIP Dashboard
   - Invoice creation speed on the Invoice Service Usage Dashboard
     - Generally expect speed to slow down during American business hrs
     - Current benchmark is `24.55 inv/s` and Intacct sync is `~10/s`
     - If it falls below `10 inv/s`, that's _bad_ and worth investigating
     - Compare with Intacct: Indeed Invoices Created vs. Invoice Key Received
     - Errors on the Revenue Service Dashboard

2. Invoice Creator App Only

   - Streamlined dashboard: ICA At-A-Glance
   - Further investigation:  Invoice Creator Dashboard
   - All the dead letter queues:
      - Invoice Groupings DLQ
      - V2 DLQ with Messages Waiting widget
      - V3 DLQ with Messages Waiting widget
   - Errors / Exception counts by method
   - Ensure that the queue drains before 3 AM Central Time
   - Messages waiting in queue:

**Datadog Logs**

Check the Invoice Creator deployment group and database product volume.
For more information about how to access these logs, visit this
demo and FAQ from the Slack channel `#help-datadog` and blog
“Logging Is Dead; Long Live Logging! - Migrating From Kibana To Datadog.”

**Replication Lag**

Invoicing use to monitor and point analytical questions to the corp replica,
but corp replicas are expensive to maintain, so Invoicing decommissioned it,
work tracked in `TICKET-3690`. Most of the database tables used for analytical
work and reporting are available in the Datalake, and remain listed as
as `moneydb`, not `invoicingdb`.

`financedb` is also moving away from using replication, but this team hasn't
decommissioned their replica yet. Monitor the replica-cluster On-Prem to
AWS Aurora Dashboard if needed. Direct questions about anything `financedb`
to Slack channel `#help-db-cluster`.

---
