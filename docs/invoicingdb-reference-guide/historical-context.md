---
layout: default
title: Historical Context
parent: invoicingdb Reference Guide
nav_order: 1
---

# Historical Context

This page explains the database migrations and naming changes
that led to the current `invoicingdb` structure. Understanding
this history helps clarify why certain configurations exist and
how access patterns have evolved.

---

## The Migration Journey

Our tables from `moneydb` were migrated to our own `prodinvoicing` cluster,
but keeping the name `moneydb` limited our use of our internal privilege
management system. We have migrated these tables to `invoicingdb`, so that
we can manage permissions and use our new internal SQL tools.

Our data team is reworking their documentation, but if a doc has a
**Verified** badge, it should be safe to follow. Reference the documentation
in the MySQL documentation hub before heading to the Slack channel `#help-mysql`.

During the second migration, existing permissions were copied from `moneydb`,
so your individual permissions should not have been interrupted to `moneydb`.
You'll notice from the MySQL documentation hub that using Terraform is the
recommended path to managing new databases, but it is more complex to
retroactively implement to an existing environment. We can use AWS CLI
for our new databases for select circumstances until advised otherwise.

Once all of our internal databases are onboarded onto our internal permissions
system, individuals should be able to self-serve grants. We should aim to
limit traffic to the PROD database. Engineers _should only be interacting
with QA while PROD should only be for services_. We have deprecated the
corp replica. If for any reason you need admin credentials to make changes
not possible through our internal tools, visit
[How To Make Database Changes](how-to-make-database-changes.md).

| Database Name | Cluster Name | Grants | Also Known As |
| --------- | --------- | -------- | ----- |
| `invoicingdbtest` | `qainvoicingdb` | engineers and services | "QA" |
| `invoicingdb` | `prodinvoicingco` *turned-off 02/26 | engineers | "corp replica" |
| `invoicingdb` | `prodinvoicingdb` | services only | "PROD" |

---
