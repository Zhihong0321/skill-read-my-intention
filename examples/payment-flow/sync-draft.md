# RMINGI Sync Draft (Air-Gapped)

> **RMINGI v0.4** — Worked Example

- Repo: `acme-billing`
- Draft opened: 2026-03-15
- RMINGI version: v0.4
- Evidence layers accessed: `README.md`, `code`, `commit messages`
- Evidence layers missing: `ADRs`, `product notes`, `ops runbooks`, `.agents/ memory files`

> ⛔ **WARNING TO AI AGENTS:**
> This file is NOT canonical truth. Do NOT use as authority.

---

## Review Packet 1: Payment State Machine

- Risk level: `CRITICAL`
- Why this packet matters: misunderstanding payment states causes double charges, lost revenue, or blocked reconciliation

### [Entry: Meaning of "Pending" Status]

- State: `OPEN`
- Likely intention: pending means waiting for an external event to resolve the payment
- Risk if misunderstood: duplicate charges via retry cron, or blocked manual approvals

**What the code is doing:**
The payment state machine transitions `draft` → `pending` → `paid`.
A cron job retries any payment in `pending` for more than 6 hours
by re-calling `payment.submit()`.

**Evidence found:**
- Source: `code` (payment_model.py) — state transition exists with no inline comment
- Source: `code` (cron/retry_payments.py) — retry job added, commit says "fix stale payments"
- Source: `commit history` — AI-2 added retry in a "performance optimization" commit
- Gap: no ADR, product note, or README section explains what `pending` means
- Gap: no webhook handler is explicitly linked to the `pending` → `paid` transition

**Why this is OPEN:**
Critical ambiguity. The retry cron **assumes** `pending` means "gateway timeout."
But if `pending` means "waiting for manual accounting approval," the cron
is silently re-charging cards without human review. The two interpretations
have opposite safety implications and no evidence resolves the conflict.

**Needs human review:**
1. What does `pending` mean in this system — gateway wait or manual approval?
2. Should the retry cron exist at all?
3. If retry is valid, what is the maximum number of attempts?
4. Who should be notified when a payment is stuck in `pending` for >24 hours?

---

### [Entry: Retry Cron Scope]

- State: `OPEN`
- Likely intention: the cron exists to handle transient gateway failures
- Risk if misunderstood: re-charging customers who are awaiting manual review

**What the code is doing:**
The cron runs every 6 hours, selects all `pending` payments older than 6 hours,
and re-submits them to the payment gateway.

**Evidence found:**
- Source: `code` — no retry limit or attempt counter
- Source: `commit history` — commit message: "fix stale payments" (AI-2)
- Gap: no documentation on whether retry is sanctioned or experimental

**Why this is OPEN:**
The cron has no retry limit. A payment stuck in `pending` (e.g., awaiting
manual approval that never comes) will be re-charged every 6 hours indefinitely.

**Needs human review:**
1. Should this cron exist?
2. If yes, what is the max retry count?
3. Should it alert someone instead of silently retrying?

---

## Review Packet 2: Customer Data Lifecycle

- Risk level: `CRITICAL`
- Why this packet matters: deletion and retention rules often hide legal obligations

### [Entry: Soft Deletion — Retention Purpose]

- State: `EVIDENCED`
- Likely intention: data is retained for compliance, not just UX convenience
- Risk if misunderstood: premature data purge causing legal exposure

**What the code is doing:**
`deleted_at` is set on account deletion. User cannot log in. Data remains
in the database. No archival job. No hard-delete job. No retention period defined.

**Evidence found:**
- Source: `code` — standard soft-delete pattern with no expiry logic
- Source: `code` — deleted records are still queryable by admin endpoints
- Source: `README.md` — mentions "We comply with GDPR data retention standards" (vague)
- Gap: no specific retention period documented anywhere

**Why this is EVIDENCED (not CONFIRMED):**
The README mentions GDPR compliance, and the code retains data indefinitely,
which together suggest legal retention is the intent. But no human has
confirmed the specific rule, the required retention period, or when (if ever)
hard deletion should occur.

**Needs human review:**
1. Is this retention for legal compliance, account recovery, or both?
2. What is the minimum retention period?
3. Should there be a hard-delete job after the retention period expires?
4. Does admin queryability of deleted records serve a specific business need?

---

### [Entry: Refund Authorization Model]

- State: `EVIDENCED`
- Likely intention: refunds require manual admin approval as a permanent business rule
- Risk if misunderstood: building automated refund paths without approval

**What the code is doing:**
The refund endpoint is gated behind `role = admin`. No automated refund
triggers exist. No self-service refund flow for customers.

**Evidence found:**
- Source: `code` — `@require_role('admin')` decorator
- Source: `commit history` — "add manual refund for support team" (AI-1)
- Source: `code` — no event-triggered or cron-triggered refund logic

**Why this is EVIDENCED (not CONFIRMED):**
The commit message and code strongly suggest manual-only is intentional.
But it's unclear if this is a permanent policy or if automation is planned.

**Needs human review:**
1. Are refunds permanently manual-only?
2. Should customers ever be able to request self-service refunds?
3. Are there scenarios where automated refunds should trigger (e.g., duplicate charges)?

---

## Conflict Log

### [Conflict: Retry Cron vs. Pending Semantics]

- Source A: `code` (payment_model.py) — `pending` has no retry-related comment
- Source B: `code` (cron/retry_payments.py) — treats `pending` as "safe to retry"
- Source C: `README.md` — no mention of retry policy or `pending` semantics
- Resolution: `UNRESOLVED` — logged as `OPEN`, requires human disambiguation

---

## Unresolved Summary

| Entry | State | Packet | Cycles Open |
|---|---|---|---|
| Meaning of "Pending" Status | `OPEN` | Payment State Machine | 1 |
| Retry Cron Scope | `OPEN` | Payment State Machine | 1 |
| Soft Deletion — Retention Purpose | `EVIDENCED` | Customer Data Lifecycle | 1 |
| Refund Authorization Model | `EVIDENCED` | Customer Data Lifecycle | 1 |
