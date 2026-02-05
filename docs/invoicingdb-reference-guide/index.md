---
layout: default
title: invoicingdb Reference Guide
nav_order: 3
has_children: true
---

# `invoicingdb` Reference Guide

Welcome to the `invoicingdb` Reference Guide.

This documentation guides the team through database access and maintenance following the shutdown of the corp replica. We're transitioning to a new model where analytical data comes from the data lake and operational data comes from primary instances via dedicated service accounts. To access production data now, users must temporarily enable self-service grants in our schema management tool, request permissions, use the read-only endpoint for queries, then disable self-service grants. Making database changes requires MySQL 8, AWS CLI, credentials from Kion and AWS, and critically, creating snapshots before changes and communicating all work in the team Slack channel. The goal is to restrict production access to services only while engineers work primarily in the QA environment. 

*All proprietary information and links have been removed.*

---
