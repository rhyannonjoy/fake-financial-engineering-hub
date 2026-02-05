---
layout: default
title: How To Make Database Changes
parent: invoicingdb Reference Guide
nav_order: 3
---

# How To Make Database Changes

This guide walks through the required tools, credentials, and safety
procedures for making changes to `invoicingdb`. Always test changes
in Stage and/or QA before applying them to PROD, communicate with
the team, and create snapshots before potentially destructive
operations.

**Prerequisites**: MySQL 8, AWS CLI, and access to the appropriate
AWS project in Cloudguard.

---

## Table of Contents

- [Communicating and Documenting](#communicating-and-documenting)
- [Before You Begin Checklist](#before-you-begin-checklist)
- [Tools and Credentials](#tools-and-credentials)
- [Cluster Snapshots](#cluster-snapshots)
- [Executing Changes](#executing-changes)

---

## Communicating and Documenting

- Depending on the task, consider discussing with
the team about scheduling the database changes
- Create a work ticket to track your work
- Depending on the task, consider creating separate tickets
for each database environment that you plan on making changes to
- Execute the changes in stages
- Document and communicate any issues
- Test your changes in Stage and/or QA before moving to PROD
- Request that a teammate verify your changes in each environment
you make changes to
- Communicate with the team in Slack `#invoicing-dev` anytime you're
making an unscheduled change so as to not accidentally implement a
scream test (turning something off to see who screams)

---

## Before You Begin Checklist

- [ ] Do you have MySQL 8 installed?
- [ ] Do you have AWS CLI configured?
- [ ] Have you notified the team in `#invoicing-dev`?
- [ ] Have you created a work ticket?
- [ ] Are you starting in Stage and/or QA, _not_ PROD?

---

## Tools and Credentials

**MySQL 8**:

- Not all of our databases have been upgraded yet
- The dev-env-setup script does not enforce MySQL 8
- If you need to upgrade, reference article
"How to Upgrade MySQL 5.7 to 8.0 on Mac"

**AWS CLI**:

- Reference article "How-to: Setup AWS CLI"
- Should already be installed and up to date if you're using the
dev-env-setup script
- Check with Terminal command `brew install awscli`
- In order to verify your installation (if you installed), or test your
connection you're going to need credentials -

**Short-term AWS Credentials For Individual Use**:

Reference article "How do I generate short-lived AWS credentials to use?"

1. Log into AWS using Cloudguard
2. Select the appropriate project, either `invoicing-systems-qa` or
`invoicing-systems-prod`
3. Set your profile to `[default]`
4. No profile or timezone info in your local `~/.aws/credentials` file
required
5. Verify that your access to AWS works:

```bash
aws ec2 describe-vpcs --dry-run
```

>_Note: Short-term credentials expire after a certain number of hours,
make sure they don't stick around somewhere on your local machine._

**Generate AWS Credentials For Cluster Use**:

Reference article "How do I retrieve the root credentials of my cluster?"

- Generate cluster credentials with this AWS CLI command:

```bash
aws secretsmanager get-secret-value --region ${us-east-2} --secret-id rds-admin-credentials-${qainvoicingdb}
```

- Change the cluster to reflect the appropriate environment:
    - QA cluster: `qainvoicingdb`
    - PROD cluster: `prodinvoicingdb`

>_Note: The corp replica cluster was `prodinvoicingco`- don't attempt to
make changes on the replica, it was turned-off 02/26; if for some reason
Invoicing restores the corp replica, reference article "How do I rebuild
a corp replica of a cluster?"_

**Remember that these credentials DO NOT EXPIRE by default:**

- Set your password to expire
- Or, delete your admin console when you've completed your database task
- For more information, reference "How do I access my cluster?" and/or
"How do I connect using DataGrip?"

---

## Cluster Snapshots

If there is any chance that your changes could be destructive in PROD,
restore a snapshot of the cluster before you begin your maintenance window -

- Reference article "How do I restore a snapshot of my cluster?"
- Reference AWS documentation "Restore-db-cluster-from-snapshot"

**Snapshot Commands From the Second Migration**:

```bash
aws rds create-db-cluster-snapshot --region us-east-2 --db-cluster-identifier prodinvoicingdb --db-cluster-snapshot-identifier corp-replica-backup-20241021
```

---

## Executing Changes

1. **In Stage and/or QA first**:
   - Connect using credentials, review
   [Tools & Credentials](#tools-and-credentials) if needed
   - Execute your changes
   - Verify the results
   - Request teammate verification

2. **Create PROD snapshot** if changes are potentially destructive

3. **In PROD**:
   - Announce in `#invoicing-dev`
   - Execute the same changes
   - Verify the results
   - Request teammate verification
   - Document any issues in your ticket
