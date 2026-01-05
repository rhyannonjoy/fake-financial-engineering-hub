# Invoicing Reference Guide

This documentation is a comprehensive operational guide for the DFR - developer first responder -
managing the MIP - monthly invoicing process. Details include detailing monitoring, remediation,
and communication procedures throughout the invoice generation cycle:

- **Pre-MIP Preparation**: deployment scheduling, facilitating database access
- **Continuous Monitoring**: checking via the Monitor App and Datadog
dashboards to track invoice creation speed, DLQs - dead letter queues -, errors
- **Investigation Techniques**: specific database queries for blocked messages, duplications,
failed requests, uninvoiced records
- **Remediation Steps**: redriving messages from DLQs using AWS CLI, retrying failed invoice
requests, potential resource tuning
- **Post-MIP Communication Protocol**: when to notify stakeholders that invoice creation is complete

_All proprietary information and links have been removed._

---

## Expectations

Invoicing engingeers must monitor, remediate, and communicate throughout the MIP. The
following tasks don't have to be individually completed by the DFR. If you’re DFR, consider
reporting and or delegating tasks in Slack channel `#invoicing-dev`. Tuning resources
may not be necessary, but required manual processes include:

- Redriving messages from the dead letter queues, visit Remediate section, “AWS SQS Redrive Policy”
- Retrying failed pending invoice requests, visit Remediate section, “Retry Failed Pending Invoice
Requests”

---

## Pre-MIP

Discuss deployment scheduling to ensure a smooth MIP. Discuss DFR coverage, especially if MIP falls
on a weekend and or federal holiday. Ensure that everyone has access to the team's internal
databases:

- **QA**: `invoicingdbtest`
- **PROD**: `invoicingdb`

For any data changes, visit “How to Maintain invoicingdb”

---

## Monitor

### Monitor App

All Invoicing engineers should have access to the Monitor App, otherwise ask in `#ops-eom-supports`
and tag senior management. This is the same view that finance has, but isn't the absolute source of
truth, as discussed in the Slack channel `#invoicing-dev`.

### Datadog

#### Assorted Invoicing Services

- Any remaining invoices on our MIP Dashboard
- Invoice creation speed on the Invoice Service Usage Dashboard
    - Generally expect speed to slow down during American business hrs
    - Current benchmark is **24.55 inv/s** and Intacct sync is **~10/s**
    - If it falls below 10 inv/s, that's bad and worth investigating
    - Compare with Intacct: Indeed Invoices Created vs. Invoice Key Received
    - Errors on the Revenue Service Dashboard

#### Invoice Creator App Only

- Streamlined dashboard: ICA At-A-Glance
- Further investigation:  Invoice Creator Dashboard
- All the dead letter queues:
    - Invoice Groupings DLQ
    - V2 DLQ with Messages Waiting widget
    - V3 DLQ with Messages Waiting widget
- Errors / Exception counts by method
- Ensure that the queue drains before 3 AM Central Time
- Messages waiting in queue:

### Logs

- Datadog logs
- Basic demo and FAQ from this Slack `#help-datadog` thread
- Visit blog “Logging Is Dead; Long Live Logging! - Migrating From Kibana To Datadog”
- Invoice Creator deployment group
- Check database product volume

---

## Replication Lag 

- Invoicing use to monitor and point analytical questions to the corp replica, but corp
replicas are expensive to maintain, so Invoicing decommissioned it, work tracked in `TICKET-3690`
- Most of the database tables used for analytical work and reporting are available in the Datalake
- _Note_: tables listed as `moneydb`, not `invoicingdb` yet
- `financedb` is moving away from using replication, but hasn't decommissioned their replica yet
    - replica-cluster On-Prem to AWS Aurora Dashboard
    - Any questions about anything `financedb` use Slack `#help-db-cluster`

---

## Verify Zuora Invoices

1. Check the Postpay Invoices Remaining graph in the MIP Datadog dashboard
   - This graph should stabilize at a low number when ZIP-Zuora has finished the main processing
   - This graph stabilized at 1 last MIP
   - Visit this Slack discussion in `#invoicing-dev`
2. Check the ZIP - Zuora Invoice Processor - Datadog dashboard
   - Once the Postpay Invoices Remaining graph looks stable and the ZIP dashboard no longer looks
   like it’s processing a large number of records, proceed to the next step
3. Redrive messages in the ZIP DLQ using credentials from the AWS account
`indeed-zuora-inv-processor-prod` in Kion
   - Redrive with the AWS CLI, an attempt to redrive from the SQS dashboard might return:
`Failed to create redrive task. Error code: AccessDeniedException`
   - ICA and DRS have SQS console access roles available via Kion that allow redriving from the
SQS dashboard, if Invoicing wants a ZIP console access role, we would need to file CFG -
Cloud Account Platform -  ticket, visit DRS-182
   - To redrive using AWS CLI/API, generate short-term access keys in the Terminal:

    ```bash
    aws sqs start-message-move-task \
    --source-arn arn:aws:sqs:...:<my-dlq> \
    --destination-arn arn:aws:sqs:...:<my-queue>
    ```

   - Ensure completion of the redrive, monitor Datadog DLQ metrics
   - Document and or communicate any issues in Slack `#invoicing-dev`
   - Once the DLQ has been empty for some minutes, proceed to step 4
4. Check for failed pending invoice requests
   - Don't expect the retry mechanism to resolve Zuora-specific failures with error messages such
as `FAIL: Sync Customer attempt failed for invoice ...`; handling requires moving failing accounts
to Billing Platform 1 and retry them through legacy
   - Make an ICA ticket with the pending invoice request IDs
   - Communicate with OTR in Slack `#invoicing-dev`
   - Once the removal is complete, retry the failed invoice requests
   - Check if the accounts moved to Billing Platform 1 had an active promotion by running the below
in `moneydb`, and notify financial systems and support and the promotion teams that the promotions
for these accounts were possibly double-used, visit recent example `TICKET-3278`:

    ```sql
    SELECT * FROM tblpromotion_items where account_id in (...);
    ```

5. Let `#ops-eom-supports` know Zuora invoicing has completed

---

## Investigate

### Endpoints for Replication Lag

Invoicing has 2 endpoints as database replication lag workarounds, refer to the "MIP Endpoint"
documentation and “How to Retry Failed Pending Invoice Requests” - Step 2, Option 1

```java
GET request /manual/run/monthlyPendingRequestsCount?yearMonth=2025-02 - should return all
PENDING requests from tblpending_invoice_request for current month (invoice_date >= "2025-02-01" )
```

```java
GET request /manual/run/monthlyFailedRequests?yearMonth=2024-02 - should return all 'FAILED'
and 'REQUEST_FAILED' requests from tblpending_invoice_request?yearMonth=2025-02 for current
month (invoice_date >= "2025-02-01" )
```

```java
# takes optional limit parameter, limits the output
GET request /manual/run/monthlyFailedRequests?yearMonth=2024-02&limit=100
```

### Queries

#### Blocked Messages

View `pending_invoice_request_id` and `unique_id` side-by-side:

```sql
select id as pending_invoice_request_id,
    JSON_EXTRACT(payload, '$.uniqueId') as unique_id
from tblpending_invoice_request
where status = 'SENT'
  AND modified_at <= DATE_SUB(NOW(), INTERVAL 10 MINUTE)
  AND invoice_date <= :today’s date
order by unique_id;
```

#### Duplication

- Check for duplicate invoices from AutoRevenueService and the 5 test markets
- Look for ARS invoices from the migrated ICA accounts, visit ICA-306
- _Note_: Invoicing decommissioned ARS in 11/2024

```sql
select *
from tblinvoices i
        inner join tblsummary_advertiser 
        sa on i.account_id = sa.account_id
        join moneydb.tblinvoice_unique_ids iu 
        on i.id = iu.invoice_id
where sa.billing_country in ('IN', 'IT', 'MX', 'BR', 'AU')
 and iu.tool = 'InvoiceService-ARService'
 and iu.date_created >= '2023-03-01'
 and invoice_type in ('POSTPAY_CC', 'POSTPAY_INVOICE');
```

```sql
select i.id, i.account_id
from tblinvoices i
         join tblinvoice_products 
         ip on i.id = ip.invoice_id
         join tblsummary_advertiser tACa 
         on i.account_id = tACa.account_id
where ip.product_id = 9
  and i.invoice_date = '2023-01-31'
  and i.date_created > '2023-02-01'
  and i.invoice_type != 'PREPAY_INVOICE'
  and tACa.billing_country 
in ('IN', 'IT', 'MX', 'BR', 'AU');
```

The query below selects all accounts that have many MIP invoices for `product_id`
5, 7, 9, 22 - what automatic MIP processes control. Take each row and check if there
are many tools that created an invoice using `tblinvoice_unique_ids`:

```sql
select i.account_id,
       ip.product_id,
       ip.loc_id,
       ip.po_number,
       count(DISTINCT i.id) as num_invoices,
       group_concat(i.id)   as EOM_invoices
from tblinvoices i
         join tblinvoice_products ip on i.id = ip.invoice_id
where i.invoice_date < '2023-05-01'
  and i.date_created > '2023-05-01'
  and ip.product_id in (5, 7, 9, 22)
group by i.account_id, ip.product_id, ip.loc_id, ip.po_number
having num_invoices > 1;
```

#### Failed Invoice Requests 

```sql
select *
from tblpending_invoice_request
where status != 'COMPLETED' 
and created_at >= '2022-12-01'
order by id desc;
```

#### Select Specific Error Messages

```sql
select *
from tblpending_invoice_request
where status in ('FAILED', 'REQUEST_FAILED')
and invoice_date >= '2023-08-01'
and error_messages not like '%amount_local must be pos%'
and error_messages not like '%Currency mismatch%';
```

It’s possible that a currency mismatch is the result of a currency migration mid-month,
which Invoicing can't control. Verify and quantify the amount uninvoiced and report it by
creating a `SUPPORT` ticket, here is any example: `SUPPORT-17727`

#### Garbled Text

**Source**: Slack `#ops-eom-supports`, Event Remediation for `EVNT-4711` and resolved by
`ICA-398` and `DRS-62`

```sql
SELECT * FROM tblinvoice_lines il
join tblinvoices i ON il.invoice_id = i.id
WHERE il.line_description LIKE '%�%' AND il.date_modified 
>= '2023-07-01'
group by il.line_product;
```

#### Multiple Invoice Dates

**Source**: Slack `#ops-eom-supports`, Event Remediation for `EVNT-4711`

```sql
select * from  tblinvoices
where date_created > '2023-07-01 00:00:00'
and invoice_date > '2023-06-01 00:00:00'
and invoice_date < '2023-06-30 00:00:00';
```

#### Uninvoiced Records

```sql
select *
from tblproduct_revenue
where state = 'UNINVOICED'
 and start_time between '2022-07-01' 
 and '2022-07-31 23:59:59';
```

---

## Remediate

### AWS SQS Redrive Policy

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

## Communicate

### When

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

### Where

Post in Slack channel `#ops-eom-supports` and usual contacts include billing, finance,
and recon

### What

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
