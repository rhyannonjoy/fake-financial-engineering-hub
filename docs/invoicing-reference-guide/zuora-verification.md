---
layout: default
title: Zuora Invoice Verification
parent: Invoicing Reference Guide
nav_order: 3
---

## Zuora Invoice Verification

1. Check the Postpay Invoices Remaining graph in the MIP Datadog dashboard
   - This graph should stabilize at a low number when ZIP-Zuora has finished the main processing
   - This graph stabilized at 1 last MIP
   - Visit this Slack discussion in `#invoicing-dev`
2. Check the ZIP - Zuora Invoice Processor - Datadog dashboard
   - Once the Postpay Invoices Remaining graph looks stable and the ZIP dashboard no longer looks
   like it’s processing a large number of records, proceed to the next step
3. Redrive messages in the ZIP DLQ using credentials from the AWS account
`indeed-zuora-inv-processor-prod` in Kion
   - Redrive with the AWS CLI, an attempt to redrive from the SQS dashboard might return:
`Failed to create redrive task. Error code: AccessDeniedException`
   - ICA and DRS have SQS console access roles available via Kion that allow redriving from the
SQS dashboard, if Invoicing wants a ZIP console access role, we would need to file CFG -
Cloud Account Platform -  ticket, visit DRS-182
   - To redrive using AWS CLI/API, generate short-term access keys in the Terminal:

    ```bash
    aws sqs start-message-move-task \
    --source-arn arn:aws:sqs:...:<my-dlq> \
    --destination-arn arn:aws:sqs:...:<my-queue>
    ```

   - Ensure completion of the redrive, monitor Datadog DLQ metrics
   - Document and or communicate any issues in Slack `#invoicing-dev`
   - Once the DLQ has been empty for some minutes, proceed to step 4
4. Check for failed pending invoice requests
   - Don't expect the retry mechanism to resolve Zuora-specific failures with error messages such
as `FAIL: Sync Customer attempt failed for invoice ...`; handling requires moving failing accounts
to Billing Platform 1 and retry them through legacy
   - Make an ICA ticket with the pending invoice request IDs
   - Communicate with OTR in Slack `#invoicing-dev`
   - Once the removal is complete, retry the failed invoice requests
   - Check if the accounts moved to Billing Platform 1 had an active promotion by running the below
in `moneydb`, and notify financial systems and support and the promotion teams that the promotions
for these accounts were possibly double-used, visit recent example `TICKET-3278`:

    ```sql
    SELECT * FROM tblpromotion_items where account_id in (...);
    ```

5. Let `#ops-eom-supports` know Zuora invoicing has completed

---
