---
layout: default
title: FAQs
parent: invoicingdb Reference Guide
nav_order: 2
---

# FAQs

This page answers common questions about accessing and
maintaining the `invoicingdb` database. For step-by-step
procedures, review [How To Make Database Changes](how-to-make-database-changes.md).
For background on the database migration, visit
[Historical Context](historical-context.md).

---

## Table of Contents

- [Analytical vs Operational Data and Corp Replica vs Primary Instance](#analytical-vs-operational-data-and-corp-replica-vs-primary-instance)
- [The corp replica is deprecated, how do we access PROD data?](#the-corp-replica-is-deprecated-how-do-we-access-prod-data)
- [Verify the Database Transaction Logs Retention Period](#verify-the-database-transaction-logs-retention-period)

---

## Analytical vs Operational Data and Corp Replica vs Primary Instance

Invoicing is moving towards the analytical-operational permissions structure
currently being enforced for `centraldb`. Analytical data, like that used for
financial reporting, should be sourced from the data lake. Requests
for analytical data were being routed to the corp replica until recently,
but we've deprecated the corp replica.

### Corp Replicas

Corp replicas are read-only clusters that replicate from a primary cluster
using low-level binlog replication. They are dedicated to read-only operations
by humans for analytical purposes.

**Do NOT use corp replicas for**:

- Non-human operations - daemons, cronjobs
- Production applications or business-critical solutions
- Read/write operations - this breaks replication and requires a rebuild

**Limitations**:

- Slow to rebuild due to large number of user grants
- Supported as "best effort" only - no SLOs or guarantees
- Expected to break frequently

>**Important:** If the process requires guaranteed availability,
migrate to a reliable data source - the primary instance or the data lake.

### Operational Data and Service Accounts

Operational data should be sourced from the primary instance through service
accounts. Invoicing should move away from shared service accounts - each
application should have its own dedicated account. For example, the `money` user
should be removed.

**Service Account Request Requirements**:

- Application or service name
- Permissions requested - read or read/write
- Tables it needs to access along with the permissions -
`SELECT`, `INSERT`, `UPDATE`, `DELETE`
- For restricted tables, follow the steps from the wiki
"Requesting Access to Restricted Tables"

---

## The corp replica is deprecated, how do we access PROD data?

Temporarily set the `invoicingdb` schema to accept self-service grants,
grant yourself access, reset your password, then turn-off self-service grants.

**Part I**: Set the schema to accept self-service grants, generate credentials -

1. Navigate to Schema Management and add `invoicingdb` to the Schema Name bar
2. Check _Allow Self-Service Grants_ and Save
3. Expect to see "The schema details have been saved"
4. Navigate to _Permissions → Request (MySQL)_, add `invoicingdb` to the Schema Name bar
5. Select _Request Permissions_
   - Notice that the host field lists the database cluster write endpoint
   - The only endpoint onboarded to this access tool is the write endpoint
   - Use AWS if you require additional details about this internal database's endpoints
6. Expect to see "Permissions successfully granted"
7. Navigate to _Credentials → Reset Password_
   - You may choose to set your own or allow our tools to generate one for you
   - Select _Reset Password_
8. Use your new password to set-up a new data source in your query tool of choice

---

**Part II**: Configure your datasource properties in your query tool of choice -

**Access Levels**:

- Most tasks only require read-access
- Write access is for services or specific database changes (like adjusting grants)
- Binary logs (binlogs) require specific grants and the write endpoint

**Who Has Access**:

- Read Access: Some services and Invoicing engineers
- Write Access: Services only
- Previously: corp replica was for financial analysts and reporters

---

**Part III**: Return to the access tool to turn-off self-service grants -

1. Return to Schema Management
2. Uncheck _Allow Self-Service Grants_
3. Select _Save_

---

**Troubleshooting**:

If you attempt to run a query in PROD and see:

  ```bash
  Connections using insecure transport are prohibited while --require_secure_transport=ON
  ```

1. Navigate to your data source, right-click and select _Properties_
2. Navigate to the _SSH/SSL_ tab
3. Check _Use SSL_ box
4. Select _Test Connection_
5. If successful, select _Apply_ then _OK_

---

## Verify the Database Transaction Logs Retention Period

Database transaction logs, aka binary logs / "binlogs" are not enabled by
default for clusters. This is something that each database owner should
configure based on their specific requirements. For example, Invoicing
currently doesn't have retention periods set for our QA database
`invoicingdbtest` binlogs.

**Prerequisites and Access**:

- Credentials to access the PROD database `invoicingdb` -
visit the previous section ["...how do we access PROD data?"](#the-corp-replica-is-deprecated-how-do-we-access-prod-data)
if needed
- Proper permissions to view binlogs, otherwise you may see
`ACCESS DENIED`
- For viewing specific binlogs - admin credentials and access
to the write endpoint
- While service users require `REPLICATION CLIENT` privilege to
view binlogs, individual users have not been granted binlog
access at this time

**Binlog Settings in Terraform**:

Observe that we have enabled binlogs by setting cluster
parameters in our Terraform `invoicingdb.tf` file and in
particular these variables:

```yaml
{
  name         = "binlog_format"
  value        = "ROW"
  apply_method = "pending-reboot"
},
{
  name         = "enforce_gtid_consistency"
  value        = "ON"
  apply_method = "pending-reboot"
},
{
  name         = "gtid-mode"
  value        = "ON"
  apply_method = "pending-reboot"
}
```

**Read and Set Binlog Retention Periods**

With the correct permissions while using the write endpoint,
you should be able to see that with `SHOW VARIABLES LIKE 'log_bin';`
the `binlog_backup` is set `ON`.

In addition, you should be able to see a list of logs with the command
`show binary logs;` in your query console.

The retention period is configured in the AWS CLI. Verify the
configuration with the following:

```SQL
CALL mysql.rds_show_configuration;
```

You may set the retention periods with the following:

```SQL
CALL mysql.rds_set_configuration('binlog retention hours', <some integer like 24>);
```
