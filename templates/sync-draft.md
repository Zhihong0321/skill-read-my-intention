# RMINGI Sync Draft (Air-Gapped)

> **RMINGI v0.4** — Temporary Review Draft

- Repo: `[repo-name]`
- Draft opened: `YYYY-MM-DD`
- RMINGI version: `v0.4`
- Evidence layers accessed: `[list which layers of the Evidence Ladder were available]`
- Evidence layers missing: `[list which layers were inaccessible or empty]`

> ⛔ **WARNING TO AI AGENTS:**
>
> This file is **NOT canonical truth**.
> It contains `EVIDENCED` and `OPEN` entries that still need human review.
>
> **Do NOT** use this file as authority during refactors, cleanup, or implementation.
> **Do NOT** copy entries from here into `intentions.md` without human confirmation.
>
> This file exists solely to facilitate the batched human-sync process.
> Delete it only when no `EVIDENCED` or `OPEN` entries remain.

---

## Review Packet 1: Payment Lifecycle

- Risk level: `CRITICAL`
- Why this packet matters: money movement can break silently while all tests still pass

### [Entry: Pending Payment Status]

- State: `OPEN`
- Likely intention: pending means the payment is waiting for an external confirmation step
- Risk if misunderstood: duplicate charges, wrong retry logic, or blocked accounting flow

**What the code is doing:**
The payment state machine moves from `draft` → `pending` → `paid`.
No clear auto-retry or manual approval rule is visible in code.

**Evidence found:**
- Source: `code` — the state exists in the payment flow model
- Source: `code` — no linked retry job was found in the scheduler
- Source: `docs` — no manual approval note was found in README or ADRs
- Gap: no webhook handler maps to this state transition

**Why this is OPEN (not EVIDENCED):**
The state could mean gateway wait, webhook delay, or manual accounting approval.
The code does not prove which one is correct. No documentary evidence was found
to disambiguate.

**Needs human review:**
- What exactly does `pending` mean in this system?
- Can it ever be retried automatically?
- Who is allowed to move it out of `pending`?
- What happens if it stays `pending` for more than 24 hours?

---

### [Entry: Refund Flow]

- State: `EVIDENCED`
- Likely intention: refunds are manual-only and require admin approval
- Risk if misunderstood: unauthorized automated refunds, revenue leakage

**What the code is doing:**
A `refund` endpoint exists but is gated behind `role = admin` middleware.
No automated refund triggers were found.

**Evidence found:**
- Source: `code` — endpoint requires admin role
- Source: `commit history` — commit message says "add manual refund for support team"
- Source: `code` — no cron or event trigger calls the refund endpoint

**Why this is EVIDENCED (not CONFIRMED):**
The commit message and code structure strongly suggest manual-only refunds,
but no human has explicitly confirmed this is the permanent business rule
(vs. a temporary limitation).

**Needs human review:**
- Are refunds permanently manual-only, or is automation planned?
- Should there be an automated refund path for specific scenarios (e.g., duplicate charges)?

---

## Review Packet 2: Customer Data Lifecycle

- Risk level: `CRITICAL`
- Why this packet matters: deletion and retention rules often hide legal intent that code cannot express

### [Entry: Soft Deletion of Accounts]

- State: `EVIDENCED`
- Likely intention: account data stays in place because retention matters more than immediate hard deletion
- Risk if misunderstood: legal exposure, audit failure, or destructive cleanup

**What the code is doing:**
A `deleted_at` field is populated and deleted users lose normal access,
but the records remain in the database indefinitely.

**Evidence found:**
- Source: `code` — soft-delete behavior is implemented via `deleted_at` column
- Source: `code` — deleted records remain queryable in storage
- Source: `code` — no timed hard-delete or archival job was found
- Gap: no compliance documentation mentions retention period

**Why this is EVIDENCED (not CONFIRMED):**
The evidence points to a retention need, but no human-confirmed rule states
whether this is for account recovery, legal compliance, audit trail,
or another business reason.

**Needs human review:**
- Why are deleted accounts retained?
- How long must they remain available?
- Can they ever be archived or hard-deleted?
- Is there a legal or regulatory retention requirement?

---

## Conflict Log

> Use this section to log evidence conflicts discovered during the scan.

### [Conflict: None found in this scan]

*(If evidence sources contradict each other, log each source's claim here
with its specific citation, mark the entry as `OPEN`, and surface it
in the review packet.)*

---

## Unresolved Summary

| Entry | State | Packet | Cycles Open |
|---|---|---|---|
| Pending Payment Status | `OPEN` | Payment Lifecycle | 1 |
| Refund Flow | `EVIDENCED` | Payment Lifecycle | 1 |
| Soft Deletion of Accounts | `EVIDENCED` | Customer Data Lifecycle | 1 |

> **Do not remove `OPEN` or `EVIDENCED` entries until a human confirms them.**
> **Do not delete this file until all entries are resolved.**
