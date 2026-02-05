---
layout: default
title: FAQs
parent: invoicingdb Reference Guide
nav_order: 2
---

# FAQs

## Analytical vs Operational Data and Corp Replica vs Primary Instance

Financial Systems is moving towards the analytical-operational permissions structure currently being enforced for centraldb. Analytical data, like that used for financial reporting, should be sourced from our data lake. Previously requests for analytical data were being routed to our corp replica, but we've turned-off our corp replica.

Generally, corp replicas:

- Are read-only clusters that replicate themselves from a primary cluster using low-level binlog replication
- Are dedicated to read-only operations by humans for analytical purposes
- They should not be used by non-humans, such as daemons or cronjobs
- No production application and or business critical solution should rely on a corp replica
- Read/write operations should not be performed against them, as this will break the replication and require the replica to be rebuilt
- Due to the large number of user grants, restoring grants takes a long time and rebuilding can take hours
- Are only supported by us as "best effort" and do not come with SLOs or any other guarantees. We expect them to break and have issues often, and they do
- We do not recommend or support any important work to be done using them. If a team's capability or process requires a guarantee of availability, it eventually needs to be migrated to a reliable data source, the primary instance, or our data lake

Operational data should be sourced from the primary instance through service accounts. We want to move away from shared service accounts. Service accounts should not be used by multiple applications. For example, we eventually want to remove the money user. 

Service account requests should include:

- Application or service
- Permissions requested (read or read/write)
- Tables it needs to access along with the permissions (select, insert, update, delete)
- For restricted tables, follow the steps from article "Requesting Access to Restricted Tables"

---

### How do we access our data in PROD now that we don't have the corp replica?

Temporarily set our invoicingdb schema to accept self-service grants, grant yourself access, reset your password, then turn-off self-service grants.

1. Navigate to Schema Management and add `invoicingdb` to the Schema Name bar
2. Check **Allow Self-Service Grants** and Save
3. Expect to see "The schema details have been saved"
4. Navigate to **Permissions → Request (MySQL)**, add `invoicingdb` to the Schema Name bar
5. Select **Request Permissions**
   - Notice that the host field lists the database cluster write endpoint
   - The only endpoint onboarded is the write endpoint
   - Use KION → AWS if you require additional details about this internal database's endpoints
6. Expect to see "Permissions successfully granted"
7. Navigate to **Credentials → Reset Password**
   - You may choose to set your own or allow our tools to generate one for you
   - Select **Reset Password**
8. Use your new password to set-up a new data source in your query tool of choice

**When setting your datasource properties (in DataGrip or otherwise), please use the read-only endpoint:**

- "ro" for read-only
- `prodinvoicingdb.cluster-ro-cr2gqaomp8yh.us-east-2.rds.amazonaws.com`
- Write access is for services or specific database changes (like adjusting grants)
- In addition, binary logs (binlogs) are only accessible with specific grants and the write endpoint 
- Read access is for some services and Invoicing engineers
- Previously, the corp-replica was for financial systems analysts and reporters

**Do not forget to turn off self-service grants:**

9. Return to Schema Management
10. Uncheck **Allow Self-Service Grants**
11. Select **Save**

**If you attempt to run a query in PROD and see:**

```
Connections using insecure transport are prohibited while --require_secure_transport=ON
```

1. Navigate to your data source, right-click and select **Properties**
2. Navigate to the **SSH/SSL** tab
3. Check **Use SSL** box
4. Select **Test Connection**
5. If successful, select **Apply** then **OK**

---

### How do we verify the retention period for our database transaction logs (binary logs/"binlogs")?

Binlogs are not enabled by default for clusters. This is something that each database owner should configure based on their specific requirements. For example, we currently do not have retention periods set for our QA database `invoicingdbtest` binlogs. 

**Prerequisites:**
- You must have credentials to access the PROD database `invoicingdb` (if you don't, see the above section "How do we access...")
- You must have the proper permissions to view binlogs, otherwise you may see ACCESS DENIED
- If you're conducting a task that requires viewing binlogs, you'll need cluster admin credentials and the write endpoint must be in use
- Service users require REPLICATION CLIENT privilege to view binlogs, while individual users have not been granted binlog access at this time

Observe that we have enabled binlogs by setting cluster parameters in our Terraform `invoicingdb.tf` file and in particular these variables:

```hcl
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

With the correct permissions while using the write endpoint, you should be able to see that with `SHOW VARIABLES LIKE 'log_bin';` the `binlog_backup` is set ON. 

In addition, you should be able to see a list of logs with the command `show binary logs;` in your DataGrip query console (or the tool of your choice). 

The retention period is configured in the AWS CLI. Verify the configuration with the following:

```sql
CALL mysql.rds_show_configuration;
```

You may set the retention periods with the following:

```sql
CALL mysql.rds_set_configuration('binlog retention hours', <some integer like 24>);
```

---