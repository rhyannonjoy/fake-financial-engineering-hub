---
layout: default
title: Historical Context
parent: invoicingdb Reference Guide
nav_order: 1
---

# Historical Context

Our tables from `moneydb` were migrated to our own `prodinvoicing` cluster, but keeping the name `moneydb` limited our use of our internal privilege management system. We have migrated these tables to `invoicingdb`, so that we can manage permissions and use our new internal DML and DDL tools.

Our data team is reworking their documentation, but if a doc has a **Verified** badge, it should be safe to follow. Reference the documentation in the MySQL documentation hub before heading to the Slack channel `#help-mysql`.

During the second migration, existing permissions were copied from `moneydb`, so your individual permissions should not have been interrupted to `moneydb`. You'll notice from the MySQL documentation hub that using Terraform is the recommended path to managing new databases, but it is more complex to retroactively implement to an existing environment. We can use AWS CLI for our new databases for select circumstances until advised otherwise.

Once all of our internal databases are onboarded onto our internal permissions system, individuals should be able to self-serve grants. We should aim to limit traffic to the PROD database. Engineers should only be interacting with QA while PROD should only be for services. We have turned-off our corp replica. If for any reason you need admin credentials to make changes not possible through our tools, see the How-to: Making Database Changes section below.

| db name | cluster | grants | aka |
|---------|---------|--------|-----|
| invoicingdbtest | qainvoivingdb | engineers & services | "QA" |
| invoicingdb | prodinvoicingco *turned-off 03/25 | engineers | "corp replica" |
| invoicingdb | prodinvoicingdb | services only | "PROD" |

---
