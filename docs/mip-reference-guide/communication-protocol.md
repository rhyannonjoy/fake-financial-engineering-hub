---
layout: default
title: Communication Protocol
parent: MIP Reference Guide
nav_order: 6
---

# Communication Protocol

## When

Communicate on the first business day during business hours, unless advised otherwise. If prompted
for updates throughout the process, encourage external teammates to check the Datadog MIP
Dashboard. Assorted indications that invoice creation is complete or near completion:

- Ensure syncing with Intacct
- Check that certain components are empty
    - MIP queue, including DLQ
    - Check both visible and not visible
    - Keep in mind that Zuora sync may never be zero
    - Invoice service queue, could look at Invoice request outbox - inflight and blocked
    - It could show false positives if a request was processed and invoice was created but ICA
didn’t process the response for some reason
    - Outbox and or outbox recorder queue
- Determine if it’s the right time to start verifying that there are no major issues
    - Check the Invoice Request Outbox: `inflightInvoiceRequest`, `blockedInvoiceRequest`
    - Check for pending invoice request errors, visit “Retry Failed Pending Invoice Requests”
    - Start the ICA and or DRS redrive process if required, visit “AWS SQS Redrive Policy”

## Where

Post in Slack channel `#ops-mip-supports` and usual contacts include billing, finance,
and recon

## What

Produce an analysis if necessary. Use the query below to complete the following script:

```sql
select count(*) as num_created, max(date_created) as finished_at from tblinvoices
where billing_platform_version = 1
 and month(invoice_date) < month(now())
 and date_created >= DATE_FORMAT(NOW(), '%Y-%m-01');
```

```shell
Internal invoice creation has been completed for MIP. We have created <num_created>
Intacct invoices and completed at <finished_at> CT. cc: @senior_mgmt
```

---

## Post-MIP

- Log your time on the current DFR ticket and or create service-specific tickets if prompted
- Revert any resource tuning back to pre-MIP states, unless advised otherwise
- Discuss any new work related to MIP processes
- Discover any bugs?
- Identify any possible improvements?
- Be sure to communicate new work items with management so that they're triaged and prepared
for the biweekly refinement ceremony

---
