---
layout: default
title: DFR Checklist
nav_order: 4
has_children: true
---

# DFR Checklist

The Developer First Responder, _DFR_, is a rotating on-call
role responsible for monitoring system health, triaging issues,
and serving as the first point of contact for operational concerns
related to the Invoicing Platform Service, _IPS_. This rotation
typically lasts one week. Additional responsibilities include
facilitating team standups, responding to internal support
requests, and documenting incidents and resolutions.

## Table of Contents

- [Quick Reference](#quick-reference) - Common scenarios
- [Get Started](#get-started) - Week setup and handoff
- [Daily Responsibilities](#daily-responsibilities) - Time-tracking,
standup, and monitoring
- [Investigation Best Practices](#investigation-best-practices) -
Handling outages, restarting systems, escalating
- [Mid-Month Tasks](#mid-month-tasks) - Code freshness reviews
- [MIP Tasks](#mip-tasks) - Monthly Invoicing Process monitoring
and checklist

---

## Quick Reference

| Scenario | Steps |
| ---------- | ------- |
| **Got paged?** | Check `#ips-signals` → Review observability dashboard → Escalate to `#ips-dev` if needed |
| **Service down?** | Check audit logs → Post in `#ips-signals` before restarting → Document incident |
| **Support question?** | Answer if possible → Tag SME if unsure → Add to standup parking lot if unresolved |
| **End of rotation?** | Close ticket → Update handoff notes → Brief incoming DFR on Monday |

---

## Get Started

Complete the following steps to setup for success:

1. **Create a DFR-tracking ticket**
   - `Project`: `Financial Systems`
   - `Component`: `IPS`
   - `Labels`: `DFR`, `IPS`, `Invoicing`, `NO-QA`
   - `Workflow`: `In-Progress`
   - _Tip: clone the previous week's DFR-tracking ticket
to use as a template_

2. **Attend Handoff meeting**
   - Usually Monday morning, Google calendar title - "IPS-Ops Handoff"
   - Discuss open issues and incomplete work with outgoing DFR
   - If not on the calendar, request access from team lead
   - _Note: This meeting is optional - coordinate with outgoing DFR_

3. **Document the work**
   - Add comments to the DFR-tracking ticket to record completed tasks
   - Use judgment on what's worth documenting to reduce noise
   - Include support channel Q&A to share knowledge with the team

4. **Wrap-up the week**
   - Close the DFR-tracking ticket
   - Update any incomplete investigations for the incoming DFR
   - Prepare notes for the next IPS-Ops Handoff meeting

---

## Daily Responsibilities

**Time Tracking**: all team members must log time in Jira
against the current week's DFR-tracking ticket for any
DFR-related work, log time for -

- Facilitating or delegating tasks to others
- Addressing production issues
- Identifying, asking, or answering questions
- Investigations, reporting, and verification
- Incident remediation

**Standup Facilitation**: run the team's Kanban board during
daily standups, present any overnight and/or updates for
ongoing issues, and discuss any issues allocated to
[the parking lot](https://scrumteam.pro/what-is-parking-lot-in-scrum-and-how-to-use-it-effectively/)

**System Monitoring**

> _Email monitoring is not necessary, as all Invoicing system alerts,
including those administered by Datadog and PagerDuty, should route
to the primary monitoring Slack channels_ -

| Channel | Purpose | Action Required |
| --------- | --------- | ----------------- |
| `#ips-signals` | System health alerts and anomalies | Investigate, document findings, escalate if needed |
| `#finsys-alerts` | Cross-system financial alerts | Monitor for IPS-related issues |
| `#support-ips` | Internal user support requests | Respond or route to the SME, _subject matter expert_ |
| `#support-general` | General technical support | Monitor for IPS-related questions |
| `#incident-alerts` | New incident tickets | Triage issues tagged with IPS components |

**Secondary Monitoring**: more `#finsys-*` channels may be relevant
depending on integrations and dependencies; refer to the IPS Onboarding
documentation for a complete list

**Dashboard Checks**: while Slack alerts should cover critical issues,
occasional dashboard checks can help identify trends; check the Observability
Dashboard for irregular patterns in service metrics and the SLO
Dashboard to review daily service availability

---

## Investigation Best Practices

Focus on work items that improve operational stability and reduce future
incidents. Triage tasks by impact - production issues first, quick wins
(answer any <5 minute questions), and set expectations for non-urgent
requests. _Communication is key_ -- close any feedback loops opened.

**Handling Health Check Outages**: services may experience brief outages
due to network issues or infrastructure changes and when investigating,
check the audit logs first - remember that any outages recovering
in _<10 minutes typically don't require deep investigation_, but
document any patterns of short, repetitive outages

**Before Restarting Services**:

> ⚠️ Always communicate before manually restarting any service -

- Post a _brief_ note in `#ips-signals` or `#ips-dev` before proceeding
- For QA environments, notify the QA team to avoid disrupting active testing
- For Production restarts, communication is <u>required</u>

**Escalation Path**:

1. Document investigation in `#ips-signals`
2. Tag/ping relevant team members for additional perspective
3. If urgent and/or unresolved, escalate to `#ips-dev`
4. Add open questions to standup parking lot

---

## Mid-Month Tasks

**Code Freshness Review**: monitor code freshness emails, usually titled
"_[Action Required] Some of your applications/libraries are stale or
at-risk of being stale"_, review flagged services and libraries,
approve and merge outstanding library updates, and coordinate with the
team if major updates are needed

**Known Issues**: mid-month spike in queued messages metric is expected
and related to database and IO-Poller performance; this typically resolves
within an hour and will be addressed in the database migration

---

## MIP Tasks

MIP, the last couple of days of each month, are a heightened
monitoring period, ensure to:

- Increase monitoring frequency, _hourly checks instead of daily_
- Prioritize invoice processing and financial close activities
- Be available for urgent fixes

Refer to the [Invoicing Reference Guide](invoicing-reference-guide.md)
for comprehensive information; key points include -

- **Code Freeze Protocol**: Hot fixes for MIP issues are exempt
from standard code freeze
- **Approval Process**: Hot fixes don't require pre-approval, but
_must be tracked_
- **Communicate**: Post all MIP deployments in `#finsys-freeze-approvals`
- **Coordinate**: Include broader Financial Systems teams as needed

---

## Related Documentation

> Note: FFEH is a practice documentation site created
for portfolio purposes; while the patterns and approaches
reflect real-world engineering practice, the onboarding
materials below are fictional

**Essential Resources**:

- How to Create and Maintain API Keys
- How to Retry Failed Invoice Requests
- Developer First Responder Process
- Getting Ready for On-Call (PagerDuty Setup)

**Reference Materials**:

- IPS Onboarding Guide
- IPS Architecture Overview
- Incident Response Playbook
- IPS Runbook

**Calendars & Schedules**:

- IPS DFR Rotation Calendar

---

_Last updated: [2026-02-02] | Maintained by: IPS Team @ Fake Company, Inc._
