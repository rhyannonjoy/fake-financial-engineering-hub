---
layout: default
title: invoicingdb Reference Guide
nav_order: 3
has_children: true
---

# `invoicingdb` Reference Guide

This guide covers why `invoicingdb` exists and how we got here, database
access, permissions, and maintenance for `invoicingdb` following the
deprecation of the corp replica.

---

## Key Changes

Invoicing's transitioned to a new access model:

- **Analytical Data** → Data lake
- **Operational Data** → Primary instances via dedicated service accounts
- **Production Access** → Services only
- **Corp Replica** → Deprecated as of 02/26

---

## Quick Start

- **Need to access PROD data?** Reference [FAQs: How do we access PROD data?](faqs.md#the-corp-replica-is-deprecated-how-do-we-access-prod-data)
- **Making database changes?** Review [How To Make Database Changes](how-to-make-database-changes.md)
- **Looking for background info?** Visit [Historical Context](historical-context.md)
