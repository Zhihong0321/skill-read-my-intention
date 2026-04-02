# RMINGI Sync Draft (Air-Gapped)

> **WARNING TO AI AGENTS:** 
> This is a temporary file containing `WEAKLY INFERRED` guesses and assumptions. DO NOT treat these statements as facts. DO NOT reference them during refactoring or building. 
> 
> This file exists solely to facilitate the batched human-sync process. Once sync is complete, this file should be deleted.

---

## Review Packet 1: The Payment Lifecycle

**Risk Level:** HIGH (Financial Impact)

### 1. The "Pending" Status 
**What the code is doing:**
The database state machine defines a transition from `draft` → `pending` → `paid`. There is no timer or automated retry logic attached to `pending`.

**Assumed Intention:**
Waiting for the payment gateway webhook to fire asynchronously.

**Why it's a guess (The Evidence Gap):**
No webhook listeners are explicitly linked to this state in the router. It could be an asynchronous gateway wait, or it could require manual administrative approval. 

**Needs Confirmation:**
- Does the system transition this automatically via webhook, or does a human accountant click "Approve" to move out of `pending`?
- If a webhook fails, should this be auto-retried?

---

## Review Packet 2: Customer Data Lifecycle

**Risk Level:** HIGH (Compliance/Audit)

### 1. Soft Deletion of Accounts
**What the code is doing:**
A `deleted_at` column is populated when a user clicks "Delete My Account." The user can no longer log in.

**Assumed Intention:**
A safety measure allowing customer service to restore accounts if the user changes their mind within a grace period. 

**Why it's a guess (The Evidence Gap):**
There is no grace-period wiping job. The data remains forever. A previous AI might have just used standard Laravel/Django soft-delete paradigms as boilerplate without asking. 

**Needs Confirmation:**
- Is this data kept indefinitely for legal compliance (e.g., 7-year transaction retention)?
- Should there be a hard-delete CRON job that fires after 30 days?

---
