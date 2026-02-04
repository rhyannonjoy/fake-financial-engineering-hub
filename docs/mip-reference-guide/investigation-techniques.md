---
layout: default
title: Investigation Techniques
parent: MIP Reference Guide
nav_order: 4
---

## Investigate

## Endpoints for Replication Lag

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
