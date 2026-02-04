---
layout: default
title: Pre-MIP Preparation
parent: MIP Reference Guide
nav_order: 1
---

# Pre-MIP Preparation

Complete these preparation steps 1-2 days before MIP begins to
minimize potential issues and ensure successful invoice generation.

## Table of Contents

- [Scheduling](#scheduling): DFR Coverage, Code Freeze, Kanban Board Management
- [Access Verification](#access-verification): Databases, AWS, Monitoring Tools
- [Pre-MIP Checklist](#pre-mip-checklist): Communicate, Enable Access, Review Docs

---

## Scheduling

1. **DFR Coverage**

   - **Verify Rotation**: Confirm DFR assignment in the developer support rotation
   - **Backup Plan**: Identify a secondary DFR in case primary is unavailable
   - **Weekend/Holiday Coverage**: If MIP falls on a weekend or federal holiday,
   confirm coverage early and coordinate handoff procedures

2. **Code Freeze**

   During every MIP, the team enacts a **code freeze** - no new feature deployments
   until monthly-invoice generation is complete. This allows the team to:

   - Focus entirely on MIP monitoring and remediation
   - Ship hot fixes immediately if critical issues arise
   - Avoid introducing new variables during a high-stakes process

3. **CI/CD Enforcement**

   - The pipeline will _automatically fail_ any build-deploy attempts in Production
   for new features during the freeze - QA verification work may continue without
   interruption
   - Expect GitLab failure notifications in the team's build Slack channel `#invoicing-builds`-
   _this is normal during MIP_
   - Hot fixes can still be deployed if approved by the team lead

4. **Kanban Board Management**

   - Tag work tickets with **`pre-MIP`** label for tasks that must be completed before
   MIP begins
   - Tag work tickets with **`post-MIP`** label for work that can wait until after
   invoice generation completes
   - Use these labels to communicate priority and manage team expectations during the
   freeze

5. **Action Items**

   - Announce code freeze in `#invoicing-dev` 24 hours before MIP
   - Review Invoicing's Kanban board and apply `pre-MIP`/`post-MIP` labels to active tickets
   - Confirm no pending deployments are scheduled during MIP window
   - Brief the team on hot fix procedures if needed

---

## Access Verification

Ensure the DFR has proper access to all systems needed for investigation and remediation:

1. **Internal Databases**

   Confirm access to the team's restricted databases -

   - **QA**: `invoicingdbtest`
   - **PROD**: `invoicingdb`

   For any data changes during MIP, refer to How to Maintain invoicingdb

2. **Manual Processes**

   Verify DFR can access -

   - **SQS dead letter queues**, DLQs for message redriving
   - **Datadog logs** for error investigation
   - **Internal endpoints** for retry operations

3. **Monitoring Tools**

   Confirm login credentials and dashboards are accessible -

   - **Datadog**: Invoice creation speed and system health dashboards
   - **Zuora ZIP**: Invoice verification dashboard
   - **Slack**: `#invoicing-dev` and `#ops-mip-supports` channels

---

## Pre-MIP Checklist

- [ ] DFR assigned and backup identified
- [ ] Code freeze announced and confirmed
- [ ] Database access verified (QA and PROD)
- [ ] AWS permissions confirmed
- [ ] Datadog dashboards reviewed and bookmarked
- [ ] Zuora ZIP access tested
- [ ] Communication channels confirmed
- [ ] Documentation reviewed: [Continuous Monitoring](continuous-monitoring.md),
[Remediation Steps](remediation-steps.md)

---

**Ready to begin?** Once MIP starts, proceed to [Continuous Monitoring](continuous-monitoring.md).

---
