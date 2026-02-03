---
layout: default
title: Invoicing Reference Guide
nav_order: 3
has_children: true
---

# Invoicing Reference Guide

Welcome to the comprehensive operational guide for the
DFR, _developer first responder_, managing the MIP, _monthly
invoicing process_. Details include monitoring, remediation,
and communication procedures throughout the monthly invoice
generation cycle.

The following tasks don't have to be individually completed by
the DFR; consider reporting and or delegating tasks in Slack
channel `#invoicing-dev`. Tuning individual resources may not be
necessary, but required manual processes include redriving messages
from the dead letter queues and retrying failed pending invoice
requests.

## MIP Workflow Overview

<div class="mermaid">
flowchart TD
    Prep[Pre-MIP Prep<br/>Database Access, Deployment Scheduling]
    Prep --> Start([MIP Begins])
    Start --> Monitor[Continuously Monitor<br/>Datadog Dashboards, DLQs]
    Monitor --> Check{Issues?}
    Check -->|Yes| Investigate[Investigate<br/>Database Queries, Error Logs]
    Check -->|No| Verify[Verify Zuora Invoices<br/>Check ZIP dashboard]
    Investigate --> Remediate[Remediate<br/>Redrive DLQs, Retry Requests]
    Remediate --> Monitor
    Verify --> Complete{Invoice<br/>creation<br/>complete?}
    Complete -->|No| Monitor
    Complete -->|Yes| Communicate[Post-MIP Communication<br/>Notify Stakeholders]
    Communicate --> End([MIP Complete])

    linkStyle default stroke:#7dd3fc,stroke-width:2px
    classDef decision fill:#2d2d2d,stroke:#7dd3fc,stroke-width:3px,color:#fff
    class Check,Complete decision
</div>

## Quick Reference

| Scenario | Action |
| ---------- | -------- |
| **MIP starting soon?** | Review [Pre-MIP Preparation](pre-mip-preparation.md) → Verify database access → Discuss deployment schedule |
| **Invoice creation running slow?** | Check [Continuous Monitoring](continuous-monitoring.md) → Review Datadog Dashboards → Verify creation speed `>10 inv/s` |
| **Messages stuck in DLQ?** | See [Remediation Steps](remediation-steps.md) → Use AWS CLI to redrive messages → Monitor completion |
| **Failed invoice requests?** | Check [Investigation Techniques](investigation-techniques.md) → Query failed requests → Use retry endpoint |
| **Zuora sync issues?** | Follow [Zuora Invoice Verification](zuora-verification.md) → Check ZIP dashboard → Redrive ZIP DLQ |
| **MIP complete?** | Use [Communication Protocol](communication-protocol.md) → Post in `#ops-eom-supports` → Include invoice count |

---
