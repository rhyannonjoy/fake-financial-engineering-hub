---
layout: default
title: On-Call Guide
parent: Developer Support
nav_order: 1
---

# On-Call Guide

## What is Developer First Responder (DFR)?

[Brief definition: rotating on-call role, first line of defense, etc.]

## Why DFR Matters

- **System Reliability**: Ensures 24/7 coverage for production issues
- **Shared Ownership**: Distributes operational burden across the team
- **Learning Opportunity**: Deepens understanding of system behavior
- **Incident Response**: Enables fast triage and escalation

## Core Responsibilities

### 1. Monitor System Health

[Brief overview - link to DFR Checklist for details]

### 2. Triage Issues

[Decision framework: urgent vs. non-urgent]

### 3. Escalate When Needed

[When to involve others, escalation paths]

### 4. Document Everything

[Why documentation matters for on-call]

## DFR Rotation Model

### Rotation Schedule

- **Duration**: One week (Monday-Monday)
- **Handoff**: Monday morning sync with outgoing DFR
- **Coverage**: Business hours + on-call availability

### Support Levels

- **Primary**: Current DFR (you)
- **Secondary**: Previous week's DFR (backup)
- **Escalation**: Team lead / senior engineers

## Preparing for Your Rotation

### Before Your Week Starts

- [ ] Review open incidents and ongoing investigations
- [ ] Verify access to monitoring tools (Datadog, PagerDuty)
- [ ] Check database access (QA and PROD)
- [ ] Review recent alerts in `#ips-signals`

### Mental Preparation

- **Expect interruptions**: DFR work is reactive by nature
- **Don't panic**: You have support and runbooks
- **Ask questions**: Better to clarify than assume

## Handling Common Scenarios

### Being Paged

[Quick overview - link to DFR Checklist Quick Reference]

### Service Outages

[Brief guidance - link to Investigation Best Practices]

### Unclear Issues

[When to investigate vs. escalate immediately]

## DFR Best Practices

### During Your Rotation

1. **Bias toward communication**: Over-communicate in Slack channels
2. **Document as you go**: Add notes to your DFR ticket in real-time
3. **Close feedback loops**: Always follow up on questions/requests
4. **Protect your focus time**: Block calendar for deep work when possible

### After Your Rotation

- **Handoff thoroughly**: Brief incoming DFR on open items
- **Log your time**: Track DFR work for visibility
- **Share learnings**: Document new issues for the runbook

## Balancing DFR and Project Work

- **Time allocation**: Expect 20-40% of your time on DFR duties
- **Project planning**: Avoid scheduling major deliverables during DFR week
- **Team support**: Delegate tasks when overwhelmed

## Resources

- [DFR Checklist](dfr-checklist.md) - Operational procedures and quick reference
- [Invoicing Reference Guide](../invoicing-reference-guide/) - MIP-specific procedures
- [IPS Runbook](#) - Detailed troubleshooting guides
- [PagerDuty Setup Guide](#) - Getting ready for on-call

---

## Further Reading

- "The Missing README" by Riccomini & Ryaboy, Chapter 9: Going on-Call
- [Google SRE Book: Being On-Call](https://sre.google/sre-book/being-on-call/)
- [Incident Response Best Practices](#)

---

_Last updated: [2026-02-02] | Maintained by: IPS Team @ Fake Company, Inc._

---
