# After RMINGI — The Confirmed Intention Map

This is the canonical intention map **after** a human completed the sync.
Every entry here is `CONFIRMED` — future AI agents can rely on this as truth.

---

## Intentions Map (Canonical)

- Repo: `acme-billing`
- Last synced: 2026-03-15
- Last reviewed by: Sarah Chen (Head of Finance)
- RMINGI version: v0.4

> ⛔ **WARNING TO AI AGENTS:**
> This file contains human-confirmed business intention only.
> Do not override without human approval.

---

## 1. Payment State Machine

### [Capability: Pending Payment Status]

- Status: `CONFIRMED`
- Confirmed by: Sarah Chen / 2026-03-15
- Risk if misunderstood: `CRITICAL` — duplicate charges, unauthorized retry
- Last reviewed: 2026-03-15

**Why it exists:**
`Pending` means the payment is **waiting for a webhook callback from Stripe**.
It does NOT mean manual approval. However, it is NOT safe to auto-retry
because Stripe may still be processing the charge.

**Critical constraints:**
- The retry cron must be REMOVED — it was incorrectly added by an AI
- If a payment stays `pending` for >24 hours, it should alert the finance team, not retry
- Only a Stripe webhook event (`payment_intent.succeeded` or `payment_intent.failed`) should move a payment out of `pending`
- Manual override is allowed only for the finance team via the admin panel

**Action taken after sync:**
The retry cron was disabled and replaced with a monitoring alert.

---

### [Capability: Refund Authorization]

- Status: `CONFIRMED`
- Confirmed by: Sarah Chen / 2026-03-15
- Risk if misunderstood: `HIGH` — unauthorized refunds, revenue leakage
- Last reviewed: 2026-03-15

**Why it exists:**
Refunds are **permanently manual-only** as a business policy.
All refunds require admin approval because they affect revenue reporting
and must be logged for audit purposes.

**Critical constraints:**
- Must NEVER add automated refund triggers without finance team approval
- Self-service customer refunds are explicitly out of scope — customers must contact support
- Every refund must be logged with the admin who approved it and the reason
- Future AI must not build "automated refund for duplicate charges" without human sign-off

---

## 2. Customer Data Lifecycle

### [Capability: Soft Deletion of Accounts]

- Status: `CONFIRMED`
- Confirmed by: Sarah Chen / 2026-03-15
- Risk if misunderstood: `CRITICAL` — legal exposure, audit failure
- Last reviewed: 2026-03-15

**Why it exists:**
Soft deletion exists for **legal compliance** — specifically, a 7-year
financial record retention requirement under local tax law.
It is NOT for UX account recovery.

**Critical constraints:**
- Deleted account records must be retained for a minimum of 7 years from deletion date
- No hard-delete, archival, or cleanup job may touch these records before the 7-year mark
- Admin queryability of deleted records serves the compliance audit team
- After 7 years, records MAY be hard-deleted but only via a dedicated, human-approved job
- Any "performance optimization" that moves or archives deleted records must preserve full queryability

**Action taken after sync:**
A note was added to the cron job documentation explicitly prohibiting
expansion of the trial-user cleanup job to include soft-deleted paid accounts.

---

## Sync Stats

| Metric | Value |
|---|---|
| Total confirmed entries | 3 |
| Last full sync | 2026-03-15 |
| Open items remaining in `sync-draft.md` | 0 |
| Oldest confirmed entry | 2026-03-15 |
| Entries due for re-verification (>12 months) | 0 |

---

## What Changed After RMINGI

| Finding | Before RMINGI | After RMINGI |
|---|---|---|
| Pending status meaning | Unknown — 3 AIs guessed differently | Confirmed: Stripe webhook wait, no auto-retry |
| Retry cron | Running every 6h, no limit, re-charging cards | **Removed** — replaced with monitoring alert |
| Soft delete purpose | Unknown — assumed UX safety net | Confirmed: 7-year legal retention, no hard delete |
| Refund model | Unclear if manual is temporary or permanent | Confirmed: permanently manual-only, audit requirement |

> **Total development time saved:** Prevented a production incident that would
> have caused duplicate charges across 2,400+ accounts when the retry cron
> hit its first real-world edge case.

---

## End of Map
