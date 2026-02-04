---
layout: default
title: Investigation Techniques
parent: MIP Reference Guide
nav_order: 4
---

# Investigation Techniques

When issues arise during MIP, use these internal endpoints and
database queries to diagnose problems like blocked messages,
duplicate invoices, failed requests, and uninvoiced records.
Start with the endpoints for quick counts, then use targeted
SQL queries for deeper investigation and/or cross-team
financial data inquiries and accounting support requests.

---

## Table of Contents

- [Internal Endpoints](#internal-endpoints)
- [Queries - Blocked Messages](#blocked-messages)
- [Queries - Duplication](#duplication)
- [Queries - Failed Invoice Requests](#failed-invoice-requests)
- [Queries - Garbled Text](#garbled-text)
- [Queries - Multiple Invoice Dates](#multiple-invoice-dates)
- [Queries - Uninvoiced Records](#uninvoiced-records)

---

## Internal Endpoints

An alternative to directly querying invoice data, use the following
endpoints to report pending and/or failed invoice requests, only
accessible via admin credentials:

1. **MIP Pending Request Count**

   Fetch all pending requests for the current month -

   ```bash
   GET request /manual/run/monthlyPendingRequestsCount?yearMonth=2026-02
   ```

2. **MIP Failed Requests**

    Fetch all failed requets for the current month -

   ```bash
   GET request /manual/run/monthlyFailedRequests?yearMonth=2026-02
   ```

   ```bash
   # Use optional limit parameter to limit the output
   GET request /manual/run/monthlyFailedRequests?yearMonth=2026-02&limit=100
   ```

---

## Queries

Directly query invoicing data to investigate the following
common issues:

### Blocked Messages

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

---

### Duplication

- Check for duplicate invoices from ARS, _AutoRevenueService_ and the 5 test markets
- Look for ARS invoices from the migrated ICA accounts, documented in ICA-306

```sql
select *
from tblinvoices i
  inner join tblsummary_advertiser 
  sa on i.account_id = sa.account_id
  join moneydb.tblinvoice_unique_ids iu 
  on i.id = iu.invoice_id
where sa.billing_country in ('IN', 'IT', 'MX', 'BR', 'AU')
and iu.tool = 'InvoiceService-ARService'
and iu.date_created >= '2026-02-01'
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
  and i.invoice_date = '2026-01-31'
  and i.date_created > '2026-02-01'
  and i.invoice_type != 'PREPAY_INVOICE'
  and tACa.billing_country 
in ('IN', 'IT', 'MX', 'BR', 'AU');
```

The query below selects all accounts that have many MIP invoices
for `product_id` 3, 8, 17, 24 - what automatic MIP processes control.
Take each row and check if there are many tools that created an
invoice using `tblinvoice_unique_ids`:

```sql
select i.account_id,
  ip.product_id,
  ip.loc_id,
  ip.po_number,
  count(DISTINCT i.id) as num_invoices,
  group_concat(i.id)   as MIP_invoices
from tblinvoices i
  join tblinvoice_products ip on i.id = ip.invoice_id
where i.invoice_date < '2026-02-01'
  and i.date_created > '2026-02-01'
  and ip.product_id in (3, 8, 17, 24)
group by i.account_id, ip.product_id, ip.loc_id, ip.po_number
having num_invoices > 1;
```

---

### Failed Invoice Requests

Fetch for all request failures or specific error messages:

```sql
select *
from tblpending_invoice_request
where status != 'COMPLETED' 
and created_at >= '2026-02-01'
order by id desc;
```

```sql
select *
from tblpending_invoice_request
where status in ('FAILED', 'REQUEST_FAILED')
and invoice_date >= '2026-02-01'
and error_messages not like '%local_amount must be pos%'
and error_messages not like '%Currency mismatch%';
```

Currency mismatches are the result of a currency migration
request mid-month, which is outside Invoicing's domain.
Verify and quantify any amount uninvoiced; use CURRENCY-OPS-17727
as a template to create a `CURRENCY-OPS` ticket to report it.

---

### Garbled Text

**Source**: Slack `#ops-mip-supports`, Event Remediation for
`EVNT-4711` and resolved by `ICA-398` and `DRS-62`

```sql
SELECT * FROM tblinvoice_lines il
join tblinvoices i ON il.invoice_id = i.id
WHERE il.line_description LIKE '%�%' AND il.date_modified 
>= '2025-11-01'
group by il.line_product;
```

---

### Multiple Invoice Dates

**Source**: Slack `#ops-mip-supports`, Event Remediation for `EVNT-4711`

```sql
select * from  tblinvoices
where date_created > '2025-07-01 00:00:00'
and invoice_date > '2025-08-01 00:00:00'
and invoice_date < '2025-09-30 00:00:00';
```

---

### Uninvoiced Records

```sql
select *
from tblproduct_revenue
where state = 'UNINVOICED'
 and start_time between '2025-04-01' 
 and '2022-03-31 23:59:59';
```

---
