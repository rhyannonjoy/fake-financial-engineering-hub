---
layout: default
title: DFR Checklist
nav_order: 2
---

# Developer First Responder (DFR) Checklist

The Developer First Responder (DFR) is a rotating on-call
role responsible for monitoring system health, triaging issues,
and serving as the first point of contact for operational concerns
related to the Invoicing Platform Service (IPS). This rotation
typically lasts one week.

**Primary Responsibilities:**

- Monitor system alerts and health checks
- Triage and investigate production issues
- Facilitate team standups
- Respond to internal support requests
- Document incidents and resolutions

---

## Getting Started

### Week Start Setup

1. **Create a DFR tracking ticket**
   - **Project:** Financial Systems
   - **Component:** IPS
   - **Labels:** DFR, IPS, Invoicing, NO-QA
   - **Workflow:** In-Progress
   - **Tip:** Clone the previous week's DFR ticket as a template

2. **Attend handoff meeting**
   - Schedule: Monday morning, typically called "IPS Ops Handoff"
   - Discuss open issues and incomplete work with outgoing DFR
   - If not on your calendar, request access from team lead
   - _Note: This meeting is optional - coordinate with outgoing DFR_

3. **Document your work**
   - Add comments to your ticket as you complete tasks
   - Use judgment on what's worth documenting to reduce noise
   - Include support channel Q&A to share knowledge with the team

### Week End Wrap-up

- Close your DFR ticket
- Update any incomplete investigations for incoming DFR
- Prepare handoff notes for Monday meeting

---

## Daily Responsibilities

### Standup Facilitation

- Run the board during daily standup
- Present any overnight or ongoing issues
- Discuss parking lot items from support channels

### System Monitoring

> **Important:** All alerts should route to Slack channels;
email monitoring (e.g., PagerDuty) shouldn't be necessary.

**Primary Monitoring Channels:**

| Channel | Purpose | Action Required |
| --------- |--------- |----------------- |
| `#ips-signals` | System health alerts and anomalies | Investigate, document findings, escalate if needed |
| `#finsys-alerts` | Cross-system financial alerts | Monitor for IPS-related issues |
| `#support-ips` | Internal user support requests | Respond or route to subject matter expert (SME) |
| `#support-general` | General technical support | Monitor for IPS-related questions |
| `#incident-alerts` | New incident tickets | Triage issues tagged with IPS components |

**Secondary Monitoring:**

More `#finsys-*` channels may be relevant depending on integrations and dependencies. Refer to the IPS Onboarding documentation for a complete list.

### Dashboard Checks (Optional)

While Slack alerts should cover critical issues, occasional dashboard checks can help identify trends:

- **Observability Dashboard:** Check for irregular patterns in service metrics
- **SLO Dashboard:** Review daily service availability (not required, but helpful)

### Investigation Guidelines

**Priority:** Focus on work items that improve operational stability and reduce future incidents.

**Health Check Outages:**

- Services may experience brief outages due to network issues or infrastructure changes
- Check audit logs first when investigating outages
- Outages recovering in <10 minutes typically don't require deep investigation
- Document patterns if you observe repeated short outages

**Before Restarting Services:**

> **⚠️ Warning:** Always communicate before manually restarting any service.

- Post a note in `#ips-signals` or `#ips-dev` before proceeding
- For production restarts, communication is **strongly encouraged**
- For QA environments, notify QA team to avoid disrupting active testing

**Escalation Path:**

1. Document investigation in `#ips-signals`
2. Tag/ping relevant team members for additional perspective
3. If unresolved or urgent, escalate to `#ips-dev`
4. Add open questions to standup parking lot

---

## Time Tracking

**All team members** should log time in Jira against the current week's DFR ticket for any DFR-related work.

**Log time for:**

- Facilitating or delegating tasks to others
- Addressing production issues
- Identifying, asking, or answering questions
- Investigations, reporting, and verification
- Incident remediation

---

## Mid-Month Tasks

### Code Freshness Review

Monitor code freshness emails (usually titled "*[Action Required] Some of your applications/libraries are stale or at-risk of being stale*")

**Actions:**
- Review flagged services and libraries
- Approve and merge outstanding library updates
- Coordinate with team if major updates are needed

### Known Issues

> **📊 Expected Behavior:** Mid-month spike in queued messages metric is a known issue related to database and IO Poller performance. This typically resolves within an hour and will be addressed in the database migration.

---

## End-of-Month (EOM) Tasks

### Heightened Monitoring Period

During the last 2-3 days of the month:
- Increase monitoring frequency (hourly checks instead of daily)
- Prioritize invoice processing and financial close activities
- Be available for urgent fixes

### EOM Checklist

Refer to the detailed [EOM Runbook](#) for specific tasks. Key points:

- **Code Freeze Protocol:** Hot fixes for EOM issues are exempt from standard code freeze
- **Approval Process:** Hot fixes don't require pre-approval, but **must be tracked**
- **Communication:** Post all EOM deployments in `#finsys-freeze-approvals`
- Coordinate with broader Financial Systems team as needed

---

## Related Documentation

### Essential Resources
- [How to Create and Maintain API Keys](#)
- [How to Retry Failed Invoice Requests](#)
- [Developer First Responder Process](#)
- [Getting Ready for On-Call (PagerDuty Setup)](#)

### Reference Materials
- [IPS Onboarding Guide](#)
- [IPS Architecture Overview](#)
- [Incident Response Playbook](#)
- [IPS Runbook](#)

### Calendars & Schedules
- [IPS DFR Rotation Calendar](#)

---

## Quick Reference

**Got paged?** → Check `#ips-signals` → Review observability dashboard → Escalate to `#ips-dev` if needed

**Service down?** → Check audit logs → Post in `#ips-signals` before restarting → Document incident

**Support question?** → Answer if you can → Tag SME if unsure → Add to standup parking lot if unresolved

**End of rotation?** → Close ticket → Update handoff notes → Brief incoming DFR on Monday

---

*Last updated: [Date] | Maintained by: IPS Team @ Fake Company, Inc.*
