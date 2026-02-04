---
layout: default
title: Post-MIP Tasks
parent: MIP Reference Guide
nav_order: 7
---
# Post-MIP Tasks

After communicating MIP completion to Finance, complete these
cleanup and documentation tasks to ensure systems return to
normal operation and lessons learned are captured for future MIPs.

---

## Table of Contents

- [Adminstrative Tasks](#administrative-tasks)
- [Process Improvement](#process-improvement)
- [Post-MIP Checklist](#post-mip-checklist)

---

## Administrative Tasks

**Time Tracking**:

- Log your hours on the current DFR-tracking ticket
- Create service-specific tickets if issues arose during MIP (ARS, ICA, DRS, ZIP)
- Document any escalations or incidents

**Resource Cleanup**:

- **Revert tuning changes** back to pre-MIP configurations (unless advised otherwise):
    - Batch size
    - Database connection pool settings
    - Publishing pool
    - Usage consumer threads
    - ICA instances
- Document pre-MIP vs. post-MIP configurations in the DFR-tracking ticket
- Reference example: "ICA Config Properties - December 2025 MIP"

**Lift the Code Freeze**

- Announce in `#invoicing-dev` that the code freeze is lifted
- Verify CI/CD pipeline is building and releasing deployments again
- Review Kanban board and remove `pre-MIP` and `post-MIP` labels from completed tickets

---

## Process Improvement

Capture lessons learned; document any new insights from this MIP:

**Bugs discovered?**

- File tickets with detailed reproduction steps
- Tag with appropriate priority based on impact
- Link to the DFR-tracking ticket for context

**Improvement opportunities identified?**

- Automation opportunities - manual steps that could be scripted
- Monitoring gaps - metrics that would have helped catch issues earlier
- Documentation updates - missing or outdated information in this guide
- Performance optimizations - bottlenecks that slowed invoice creation

**Communicate New Work**

- Share findings with management for triage
- Prepare tickets and context for the biweekly refinement ceremony
- Prepare discussion questions and context for the biweekly retrospective ceremony
- Update this reference guide if procedures changed during MIP

---

## Post-MIP Checklist

- [ ] Time logged on DFR-tracking ticket
- [ ] Service-specific tickets created, if needed
- [ ] System resources reverted to normal
- [ ] Configuration changes documented
- [ ] Code freeze lifted and announced
- [ ] Kanban labels updated
- [ ] Bugs filed with priority
- [ ] Improvement opportunities documented
- [ ] New work items communicated to management
- [ ] Reference guide updated, if needed

---

**MIP complete!** Review the [MIP Reference Guide](index.md) index for future cycles.
