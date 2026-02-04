---
layout: default
title: Remediation Steps
parent: MIP Reference Guide
nav_order: 5
---

# Remediation Steps

After identifying issues through monitoring and investigation,
take action to resolve them. Common remediation tasks include
redriving stuck messages from DLQs, retrying
failed invoice requests, tuning system resources for performance,
and deleting duplicate or incorrect invoices. Document all
remediation work in the current DFR-tracking ticket and
communicate actions in `#invoicing-dev`.

---

## Table of Contents

- [AWS SQS Redrive Policy](#aws-sqs-redrive-policy)
- [AWS SQS-S3 Regenerate DRs Policy](#aws-sqs-s3-regenerate-drs-policy)
- [General Tuning](#general-tuning)
- [Identify Large Accounts](#identify-large-accounts)
- [Invoice Deletion](#invoice-deletion)
- [Retry Failed Pending Invoice Requests](#retry-failed-pending-invoice-requests)

---

## AWS SQS Redrive Policy

Historically Invoicing used the AWS SQS console to redrive messages,
but as described in COE-15213, company policy favors AWS CLI; both
remain available.

- **Never used AWS CLI before?** Visit “How-to: Setup AWS CLI”;
instructions for redriving are in the ICA Runbook.
- **Monitor the DRS DLQ**: redriving directions are the same,
except for a different AWS admin account, visit the DRS Runbook
and/or the DRS Datadog dashboard for more information
- **Monitor the ZIP DLQ**: use AWS CLI to redrive messages,
view [Zuora Invoice Verification](zuora-verification.md)
for instructions
- **For additional information or questions**: use Slack
channel `#help-aws`

---

## AWS SQS-S3 Regenerate DRs Policy

Invoicing can also use the AWS console to regenerate DRs,
_detailed reports_. Depending on volume, consider using the regen script
in the DRS util package, which the DRS Runbook references.
More details described in “How to Regenerate Detailed Reports.”

---

## General Tuning

It’s possible that Invoicing resources require changes throughout
this process. Check the following to determine any needed adjustments:

- Batch size (max ~ 35k)
- Database connection pool
- Publishing pool
- Usage consumer threads
- ICA instances - if facing connectivity issues, adding more may add stress
- Does Invoicing need to manually run the MIP identifier for any specific products?
- Visit Slack channel `#invoicing-dev` for details

Keep track of any adjustments and discuss if they need to be reset
after invoice generation is complete. Document the work in the current
DFR-tracking ticket and/or a Post-MIP checklist, reference
“ICA Config Properties - December 2025 MIP” as an example.

---

## Identify Large Accounts

Currently Invoicing's working on ACCT-OPS-1524: improvements to handle
accounts with a large amount of usage events, a large accounts index doc,
and there's discussion in Slack channel `#invoicing-dev` about accounts
that had over 1M usage events in November.

---

## Invoice Deletion

Identify which invoices are candidates for deletion. Deletion itself may
require a DATA-OPS ticket, depending on the context. Gather account
information, any/all if applicable, identify which market group they
should be in, and which tool produced invoices
(ARS, DRS, ICA or ZIP).

Questions to consider:

- Did ICA invoices ended up applying mismatched rules?
- Did ICA apply April rules to March revenue?
- What's the invoice's billing country?

Create a CURRENCY-OPS ticket to request access to history of currency
migration history or billing country migration to investigate the root
cause.

---

## Retry Failed Pending Invoice Requests

Use the ICA `/failed-requests/retry` endpoint as described in the ICA
`api.md` or query the database directly. More information in
“How to Retry Failed Pending Invoice Requests.”
