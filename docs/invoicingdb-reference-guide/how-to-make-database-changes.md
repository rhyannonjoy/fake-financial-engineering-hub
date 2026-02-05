---
layout: default
title: How To Make Database Changes
parent: invoicingdb Reference Guide
nav_order: 3
---

# How To Make Database Changes

**Document your work on a JIRA ticket.** Communicate with the team anytime you're making a database change so as to not accidentally implement a scream test. Test your changes in QA before moving to PROD. Request that a teammate verifies your changes. Use Slack channel `#invoicing-engineers`.

### If you need to upgrade to MySQL 8

Not all of our databases have been upgraded yet and the system-setup script does not enforce MySQL 8.

- Reference article "How to Upgrade MySQL 5.7 to 8.0 on Mac"

### If you need to install AWS CLI

- Reference article "How-to: Setup AWS CLI"
- Should already be installed and up to date if you're using the system-setup script
- Check with `brew install awscli`
- In order to verify your installation (if you installed), or test your connection you're going to need the next step

### Generate short-term AWS Credentials

Reference article "How do I generate short-lived AWS credentials to use?"

1. Log into Kion using Okta
2. Select the appropriate project, either `financial-systems-qa` or `financial-systems-prod`

**To continue with "How-to: Setup AWS CLI" Step #4:**

- Set your profile to `[default]`
- You don't need any profile or timezone info in your local `~/.aws/credentials` file

**To verify that your access to AWS works, Step #7, remove the command's profile flag:**

```bash
aws ec2 describe-vpcs --dry-run
```

**Note:** Short-term credentials expire after a certain number of hours, make sure they don't stick around somewhere on your local machine.

### Generate AWS Credentials for our clusters

Reference article "How do I retrieve the root credentials of my cluster?"

Generate cluster credentials with this AWS CLI command:

```bash
aws secretsmanager get-secret-value --region ${us-east-2} --secret-id rds-admin-credentials-${qainvoicingdb}
```

**Change the cluster to reflect the appropriate environment:**

- QA cluster: `qainvoicingdb`
- PROD cluster: `prodinvoicingdb`
- Corp Replica cluster was `prodinvoicingco`
  - Do not attempt to make changes on the replica, it was turned-off 03/25
  - If we ever restore the corp replica, reference article "How do I rebuild a corp replica of a cluster?"

Reference articles "How do I access my cluster?" and or "How do I connect using DataGrip?"

**Remember that these credentials DO NOT EXPIRE by default:**

- Set your password to expire
- Or, delete your admin console when you've completed your database task

### If there is any chance that your changes could be destructive in PROD, restore a snapshot of the cluster before you begin your maintenance window:

- Reference article "How do I restore a snapshot of my cluster?"
- Reference AWS documentation "Restore-db-cluster-from-snapshot"

**Snapshot commands from the second migration:**

```bash
aws rds create-db-cluster-snapshot --region us-east-2 --db-cluster-identifier prodinvoicingdb --db-cluster-snapshot-identifier corp-replica-backup-20241021
```

### Execute your changes in stages, verify QA before moving to PROD

Document and communicate any issues.