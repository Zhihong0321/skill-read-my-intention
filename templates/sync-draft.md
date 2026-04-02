# RMINGI Sync Draft (Air-Gapped)

- Repo: [repo name]
- Draft opened: YYYY-MM-DD
- RMINGI version: v0.3

> WARNING TO AI AGENTS:
> This file is not canonical truth.
> It contains `EVIDENCED` and `OPEN` entries that still need human review.
> Do not use this file as authority during refactors, cleanup, or implementation.

---

## Review Packet 1: Payment Lifecycle

- Risk level: `HIGH`
- Why this packet matters: money movement can break even when tests still pass

### [Entry: Pending Payment Status]

- State: `OPEN`
- Likely intention: pending means the payment is waiting for an external confirmation step
- Risk if misunderstood: duplicate charges, wrong retry logic, or blocked accounting flow

**What the code is doing:**
The payment state machine moves from `draft` to `pending` to `paid`. No clear auto-retry or manual approval rule is visible in code.

**Evidence found:**
- the state exists in the payment flow
- no linked retry job was found
- no clear manual approval note was found

**Why this is still open:**
The state could mean gateway wait, webhook delay, or manual accounting approval. The code does not prove which one is correct.

**Needs human review:**
- what exactly does `pending` mean in this system?
- can it ever be retried automatically?
- who is allowed to move it out of `pending`?

---

## Review Packet 2: Customer Data Lifecycle

- Risk level: `HIGH`
- Why this packet matters: deletion and retention rules often hide legal intent

### [Entry: Soft Deletion of Accounts]

- State: `EVIDENCED`
- Likely intention: account data stays in place because retention matters more than immediate hard deletion
- Risk if misunderstood: legal exposure, audit failure, or destructive cleanup

**What the code is doing:**
A `deleted_at` field is populated and deleted users lose normal access, but the records remain in the database.

**Evidence found:**
- soft-delete behavior is implemented
- deleted records remain queryable in storage
- no timed hard-delete job was found

**Why this is not confirmed yet:**
The evidence points to a retention need, but no human-confirmed rule states whether this is for recovery, compliance, or another business reason.

**Needs human review:**
- why are deleted accounts retained?
- how long must they remain available?
- can they ever be archived or hard-deleted?

---

## Keep Unresolved Entries Here

Do not remove `OPEN` or `EVIDENCED` entries until a human confirms them.
