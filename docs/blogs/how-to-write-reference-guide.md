---
layout: default
title: "How to Write a Reference Guide for a Financial Process Responsible for Millions of Dollars"
parent: Blogs
nav_order: 3
---

# How to Write a Reference Guide for a Financial Process Responsible for Millions of Dollars

## Description and Objective

This piece reflects on creating operational documentation for the Monthly Invoicing Process (MIP) while working as a bootcamp graduate on a financial systems engineering team. The goal is to share lessons learned about consolidating scattered tacit knowledge into searchable, maintainable documentation that serves both current team members and future engineers.

---

## In This Post

- [The Problem: We Have Information, Just Not Where We Need It](#the-problem-we-have-information-just-not-where-we-need-it)
- [Why Documentation Isn't Optional](#why-documentation-isnt-optional)
- [The Process: Building a Living Document](#the-process-building-a-living-document)
- [Lessons Learned](#lessons-learned)

---

## The Problem: We Have Information, Just Not Where We Need It

Now that I've typed out "millions of dollars," it doesn't feel like very much, but when you're an intern fresh out of a bootcamp, you don't want to break stuff that costs the company millions of dollars. I was hired and plopped into a financial systems engineering team that spoke entirely in acronyms. Everything was new. The team swam in tickets, held [mob programming](https://en.wikipedia.org/wiki/Team_programming#Mob_programming)
sessions daily, and solved problems as a group. It was very dreamy — at first.

Don't get me wrong. I love mob programming, there are so many benefits. I'm too nosy to code in isolation.
I like building things, breaking things, chatting about things, breaking them again, fixing them, then breaking again, with others. Mob programming is a great way to prevent knowledge silos and speed-up feedback loops.
Your tickets won't get stuck in review or verification stages because everyone's already up-to-speed from whatever session was held that day.

**But here's the thing: they were mobbing, but they weren't documenting.**

This system fell apart during high-traffic periods of heightened monitoring and processing.
The information I needed to successfully support the org's MIP, _Monthly Invoicing Process_ was
scattered across:

- Years of Slack conversations in multiple channels, some of them archived
- Code repositories with READMEs of varying quality
- Google doc graveyard peppered with diagrams that aged like milk
- Outdated architectural and product overview documents
- Skeletal ticket descriptions and verification notes
- QA queries saved in random files
- Abandoned Datadog and Swagger dashboards
- Senior engineers' heads

Each MIP cycle meant rediscovering the same information. Senior engineers would drop links in Slack. Someone would remember a query from three months ago. We'd dig through old tickets to find that one remediation step. It worked, but it was exhausting and risky.

---

## Why Documentation Isn't Optional

1. **Future You Is Not Going to Remember**

   Three months feels like forever when you're learning a new system. The query you wrote to identify blocked messages? You won't remember why you filtered for messages modified more than 10 minutes ago. The specific AWS CLI command for redriving dead letter queue messages? Gone. That nuanced difference between when to use the ICA redrive versus the DRS redrive? Absolutely gone.

   This is the same cognitive load problem discussed in [Anti-pattern: Magic Numbers](antipattern-magic-numbers.md). If you're forcing your brain to hold context that should be documented, you're limiting the mental capacity available for solving _actual problems_.

2. **Async Remote Work Demands It**

   Our team worked across time zones. When an issue surfaced at 2 AM and the senior engineer who knew the fix was asleep in a different hemisphere, documentation was the only solution. The MIP didn't pause for business hours.

   Mob programming creates shared understanding in the moment, but that understanding evaporates without documentation. The engineer who joins the team six months later—or the original engineer who hasn't touched this code in six months—starts from zero.

3. **High-Stakes Processes Require Explicit Knowledge**

   When a process is responsible for millions of dollars in invoice generation, tacit knowledge isn't good enough. We needed:

   - **Reproducibility**: Any engineer following the runbook should get the same results
   - **Auditability**: Clear records of what was done and why
   - **Error prevention**: Checklists that catch common mistakes before they happen
   - **Onboarding efficiency**: New team members should ramp up in days, not months

---

## The Process: Building a Living Document

1. **Gather Abandoned Documentation**

   I started by collecting everything that already existed:

   - Old runbook drafts from previous DFRs
   - Product and architecture overviews
   - Onboarding documents that were 70% complete
   - Code comments that explained "why" better than docs did
   - Ticket templates that captured common remediation steps

   Most of this documentation was good, it just wasn't _discoverable_. A `README` buried in a subdirectory of a rarely-touched repository might as well not exist.

2. **Mine the Slack History**

   Months of conversations in `#invoicing-dev`, `#ops-eom-supports`, and `#help-datadog` contained gold:

   - Real problems engineers encountered during past MIPs
   - Solutions that worked, and ones that didn't
   - Edge cases that only surfaced once a year
   - Context about why certain procedures existed

   I searched for keywords like "MIP," "blocked messages," "DLQ," "replication lag," and "Zuora sync." Each relevant thread became a section in the guide.

3. **Consolidate Queries and Commands**

   Every MIP, someone would re-write the same SQL queries:

   - Check for blocked messages
   - Identify duplicate invoices
   - Find failed pending invoice requests
   - Count uninvoiced records

   These queries were sometimes in tickets, sometimes in Slack, sometimes in someone's local SQL client history. I gathered them all, documented what each one does, and organized them by use case.

   The same applied to AWS CLI commands, Datadog dashboard links, and database access procedures. If we used it more than once, it went in the guide.

4. **Structure for Discoverability**

   I organized the guide around the DFR's workflow:

   1. **Pre-MIP**: What to prepare before the process starts
   2. **Monitor**: Where to look and what normal looks like
   3. **Investigate**: Queries and techniques for diagnosing issues
   4. **Remediate**: Step-by-step instructions for common fixes
   5. **Communicate**: When and how to update stakeholders
   6. **Post-MIP**: Cleanup tasks and retrospective items

   Each section answered a specific question an engineer would have at that stage of the process. The table of contents became a troubleshooting flowchart.

5. **Treat It as a Living Document**

   The first version of the Invoicing Reference Guide went live in our documentation site. Then came the real work: updating it after every MIP.

   Each cycle taught us something new:

   - A new edge case that required investigation
   - A dashboard that was more useful than we thought
   - A remediation step that could be automated
   - A communication pattern that reduced stakeholder anxiety

   I added a note at the top of each section when information came from a specific incident or ticket. Future engineers could trace the provenance of our procedures and understand the context.

   After each MIP, the DFR added their learnings. We discussed improvements during retrospectives. The guide evolved from "what we think we should do" to "what we've proven works."

---

## Lessons Learned

1. **Documentation Is a Team Sport**

   The best documentation comes from the people doing the work. I could draft structure and gather information, but the senior engineers who had run twenty MIPs before me had the nuanced understanding.

   Collaborative writing sessions where we screenshared and edited together, captured knowledge that never would have made it into tickets or Slack, and the question _"wait, why do we do it that way?"_ often revealed assumptions that needed to be explicit.

2. **Optimize for Search, Not Elegance**

   I initially tried to write prose that read like a narrative. That wasn't what engineers needed during an incident at 3 AM. They needed:

   - Clear section headers that matched their mental model
   - Searchable keywords - DLQ, replication lag, blocked messages
   - Code blocks they could copy-paste directly
   - Links to dashboards and tools
   - Explicit "when to use this" guidance

   The guide became more utilitarian over time, and that made it more valuable.

3. **Examples Beat Abstractions**

   Abstract instructions like "check for anomalies in the invoice creation rate" meant nothing to a new engineer. Concrete examples helped:

   - "Current benchmark is `24.55 inv/s`. If it falls below `10 inv/s` during non-business hours, investigate."
   - "Generally expect speed to slow down during American business hours."
   - "Last MIP, the Postpay Invoices Remaining graph stabilized at 1."

   These specifics gave engineers calibration. They could look at a dashboard and know whether what they were seeing was normal.

4. **Document the Exceptions**

   The guide wasn't just about the happy path. It included:

   - What to do when the redrive fails
   - How to handle accounts that moved between billing platforms mid-month
   - Special procedures for the five test markets
   - When to escalate versus when to keep investigating

   These edge cases were exactly what got lost in Slack threads. Documenting them prevented the same fire drill from happening twice.

5. **Link, Don't Duplicate**

   We had detailed documentation about AWS CLI setup, database access procedures, and Datadog navigation in other repositories. Rather than duplicating that information, I linked to it with context:

   - "**Never used AWS CLI before?** visit 'How-to: Setup AWS CLI'"
   - "For additional information or questions: use Slack channel `#help-aws`"

   This kept the guide focused on MIP-specific procedures while ensuring engineers could find supporting information when they needed it.

---

## Summary

1. **Make Knowledge Explicit**

   Tacit knowledge works until the team member who knows the thing is asleep, on vacation, or has left the company. Business-critical financial processes can't depend on who's online.

   Documentation transforms implicit knowledge into explicit procedures that anyone can follow.

2. **Write for the Engineer with No Context**

   This guidance shows up in multiple places in our engineering standards, because it's universally applicable: _write for the engineer who has no context, which will include future-you, because future-you is not going to remember_.

   The MIP happens monthly. That's long enough to forget details but frequent enough that it feels wasteful to re-learn the same information every time.

3. **Iterate Based on Real Use**

   The first version of the Invoicing Reference Guide was about 60% complete. That was fine. We improved it every month based on what actually happened during the MIP.

   The guide became more valuable not because the initial draft was perfect, but because we treated it as a living document that evolved with our understanding.

4. **Documentation Enables Scale**

   When I first joined the team, only one or two senior engineers could confidently run the MIP. The
   [bus factor](https://en.wikipedia.org/wiki/Bus_factor) was terrifyingly low, which meant the risk was terrifyingly high.

   After several months of maintaining and improving the guide, any engineer on the team could run MIP. We had backups for our backups. New engineers ramped up in weeks instead of months.

   That's the real value of documentation: it transforms a process that lives in one person's head into a capability that belongs to the entire team.

---
