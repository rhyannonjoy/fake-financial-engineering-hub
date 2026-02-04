---
layout: default
title: Zuora Invoice Verification
parent: MIP Reference Guide
nav_order: 3
---

# Zuora Invoice Verification

Verify that Zuora invoices, processed through ZIP, _Zuora Integration Platform_,
are being created successfully. This involves monitoring ZIP dashboards,
manually redriving stuck messages from the DLQ, and handling Zuora-specific
failures by moving problematic accounts to the legacy path for retry.

---

1. **Check Postpay Invoices Remaining in the MIP Datadog dashboard**

   - This graph should stabilize at a low number when Zuora-ZIP has finished main processing
   - For example, this graph stabilized at 1 last MIP
   - Review discussions in Slack channel `#invoicing-dev` tagged `Zuora-ZIP`

2. **Check the ZIP Datadog dashboard**

   - Once the Postpay Invoices Remaining graph looks stable and the ZIP dashboard
   is no longer processing a large number of records, proceed to the next step

3. **Redrive Messages in the ZIP DLQ**

   - Generate shorterm-credentials from the AWS account `zuora-zip-prod`
   - Redrive with the AWS CLI, an attempt to redrive from the SQS dashboard might return:
`Failed to create redrive task. Error code: AccessDeniedException`
   - ICA and DRS have SQS console access roles that allow redriving from the
   SQS dashboard, if Invoicing wants a ZIP console access role, file a
   Cloud Account Platform ticket, see DRS-182 for additional context
   - To redrive using AWS CLI/API, use the following in the Terminal:

    ```bash
    aws sqs start-message-move-task \
    --source-arn arn:aws:sqs:...:<my-dlq> \
    --destination-arn arn:aws:sqs:...:<my-queue>
    ```

   - Ensure completion of the redrive, monitor Datadog DLQ metrics
   - Document and or communicate any issues in Slack `#invoicing-dev`
   - Once the DLQ has been empty for some minutes, proceed to step 4

4. Check for failed pending invoice requests
   - The retry mechanism won't resolve Zuora-specific failures with error
   messages such as `FAIL: Sync Customer attempt failed for invoice ...`; handling
   requires moving failing accounts to Billing Platform 3 and retry them through the
   legacy path
   - Make an ICA ticket with the pending invoice request IDs
   - Communicate with the team lead in Slack `#invoicing-dev`
   - Once the removal is complete, retry the failed invoice requests
   - Check if the accounts moved to Billing Platform 3 had an active promotion by
   running the query below in `moneydb`:

    ```sql
    SELECT * FROM tblpromotion_items where account_id in (...);
    ```

   Notify financial systems and support and the promotion teams that the promotions
   for these accounts were possibly double-used, see recent example PROMO-3278

5. Let `#ops-eom-supports` know Zuora invoicing has completed

---
