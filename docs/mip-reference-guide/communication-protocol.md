---
layout: default
title: Communication Protocol
parent: MIP Reference Guide
nav_order: 6
---

# Communication Protocol

Once invoice creation completes, notify Finance and stakeholders
following this protocol. Clear, timely communication ensures
downstream teams can proceed with billing reconciliation and
month-end close processes.

---

## Table of Contents

- [When to Communicate](#when-to-communicate)
- [Where to Post](#where-to-post)
- [What to Include](#what-to-include)

---

## When to Communicate

Post updates on the **first business day during North American Central Time business hours**,
unless advised otherwise.

**Indicators that MIP is complete:**

- Intacct-sync successful
- MIP queues and DLQs are empty (_note_: Zuora sync may never reach zero)
- ICA queues show minimal activity:
    - Invoice request outbox: `inflightInvoiceRequest` and `blockedInvoiceRequest` are clear
    - Outbox and outbox recorder queues are empty
- No major issues detected:
    - Check for [pending invoice request errors](investigation-techniques.md#failed-invoice-requests)
    - Verify no [ICA/DRS redrive](remediation-steps.md#aws-sqs-redrive-policy)
    or [ZIP redrive](zuora-verification.md) is needed

**If teammates ask for status updates during MIP**, direct them to the
Datadog MIP Dashboard rather than providing manual updates.

---

## Where to Post

**Slack channel**: `#ops-mip-supports`

**Tag**: Billing, Finance, and Recon team leads

---

## What to Include

Run this query to gather completion metrics:

```sql
select count(*) as num_created, 
       max(date_created) as finished_at 
from tblinvoices
where billing_platform_version = 4
  and month(invoice_date) < month(now())
  and date_created >= DATE_FORMAT(NOW(), '%Y-%m-01');
```

**Post this message template:**

```shell
Internal invoice creation has been completed for MIP.
We have created <num_created> 
Intacct invoices and completed at <finished_at> CT.
cc: @senior_mgmt
```

Include any relevant analysis or issues encountered if necessary.

---

**Next steps**: See [Post-MIP Tasks](post-mip-tasks.md) for cleanup and follow-up activities.
