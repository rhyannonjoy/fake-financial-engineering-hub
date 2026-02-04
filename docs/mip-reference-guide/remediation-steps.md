---
layout: default
title: Remediation Steps
parent: MIP Reference Guide
nav_order: 5
---

## Remediation Steps

## AWS SQS Redrive Policy

Historically Invoicing used the AWS SQS console to redrive messages, but as described in
`COE-15213`, company policy favors AWS CLI; both remain available. 

- **Never used AWS CLI before?** visit “How-to: Setup AWS CLI”. Instructions for redriving are in
the ICA Runbook
- **Monitor the DRS DLQ**: redriving directions are the same, except for a different AWS admin
account, visit the DRS Runbook and/or the Datadog monitor for more information
- **Monitor the ZIP DLQ**: use AWS CLI to redrive messages, visit “Verify Zuora Invoices”
for instructions
- **For additional information or questions**: use Slack channel `#help-aws`

### AWS SQS-S3 Regenerate DBRs Policy

Invoicing can also use the AWS console to regenerate DRs - detailed reports. Depending on volume,
consider using the regen script in the DRS util package, which the DRS Runbook references.
More details described in “How to Regenerate Detailed Billing Reports.”

### General Tuning

It’s possible that Invoicing resources require changes throughout this process. Check the
following to determine any needed adjustments:

- Batch size (max ~ 35k)
- Database pool / connections
- Publishing pool
- Usage consumer threads
- Add ICA instances - if facing connectivity issues, this may add stress
- Does Invoicing need to manually run the MIP identifier for any specific products?
- Visit Slack channel `#invoicing-dev` for details

Keep track of any adjustments and discuss if they need to be reset after invoice generation is
complete. Document the work in the current DFR ticket and/or a Post-MIP checklist, reference
“ICA Config Properties - June MIP” as an example.

### Identify Large Accounts

Currently Invoicing's working on `MONEY-1524`: running sum improvements to handle accounts with a
large amount of usage events, a Large Accounts index doc, and there's discussion in Slack
channel `#invoicing-dev` about accounts that had over 1M usage events in June, which were
all `postpay/io` customers.

### Invoice Deletion

Identify which invoices are candidates for deletion. Deletion itself may require a `SQL` ticket,
depending on the context. Gather account information, any/all if applicable and identify which
proctor group they should be in: active, inactive, which tool produced invoices: ICA or ZIP.
Questions to consider:

- ICA invoices ended up applying mismatched rules?
- April rules to March revenue?
- What's the billing country?

Request access to history of currency migration history or billing country migration to
investigate the root cause.

### Retry Failed Pending Invoice Requests

Use the ICA `/failed-requests/retry` endpoint as described in the ICA `api.md` or access the database
directly. More information in “How to Retry Failed Pending Invoice Requests.”

---
