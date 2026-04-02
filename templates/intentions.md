# Intentions Map

- repo: [repo name]
- last synced: YYYY-MM-DD
- synced by: [AI model name]
- status: DRAFT | IN-SYNC | CONFIRMED

> This file captures WHY each feature exists — organized by what users see, not by code structure.
> Any AI agent must read the relevant section here before modifying a feature area.
> CONFIRMED entries are human-verified. GUESSED entries are pending sync. DEFERRED entries are known unknowns.

---

## How To Read This File

- `✅ CONFIRMED` — intention verified by the human. Treat as ground truth.
- `⚠️ GUESSED` — AI inferred this. Human review required before acting on it.
- `⏸ DEFERRED` — known open question. Do not resolve without human direction.

---

## 1. Authentication & Access

### [Feature: Login]

**✅ Intent: CONFIRMED** — [date]
[State the confirmed intention plainly.]
[Any constraints that follow from this intention.]

---

### [Feature: Registration]

#### [Element: Agent Code Field]

**⚠️ Intent: GUESSED**

**What the code shows:**
Optional text input on the registration form. No validation. Not used in login or search queries. Not marked as required anywhere.

**Assumed intention:**
Allows admins to pre-assign a code to an agent before the agent self-registers. The code is informational only — not a unique identifier.

**Why this is a guess:**
The instruction only said: *"add agent code field, optional."* No downstream usage found in the codebase. No validation rules exist to infer constraints.

**Needs confirmation:**
- Is agent code assigned by admins before registration, or chosen by agents themselves?
- Can it be changed after registration?
- Is it ever used as an identifier or lookup key?
- What happens to an agent who registers without a code?

---

## 2. Dashboard

### [Feature: Overview Metrics]

**✅ Intent: CONFIRMED** — [date]
[Confirmed intention.]

---

## 3. [Domain Feature — e.g. Invoice Management]

### [Feature: Invoice List]

**✅ Intent: CONFIRMED** — [date]
[Confirmed intention.]

---

### [Feature: Invoice Status — "Pending"]

**⚠️ Intent: GUESSED**

**What the code shows:**
A `pending` state in the invoice/payment state machine. Invoices move from `draft` → `pending` → `paid` or `rejected`.

**Assumed intention:**
Pending = submitted and awaiting payment confirmation. System may retry or follow up automatically.

**Why this is a guess:**
No comment or documentation on what triggers the transition out of `pending`. No retry logic found, but the state machine allows it.

**Needs confirmation:**
- Does `pending` require a manual approval step, or is it automated?
- Can a `pending` invoice be auto-retried or auto-escalated?
- Who can move an invoice out of `pending` — the system, an admin, or the customer?

---

## 4. [Domain Feature — e.g. Customer Management]

### [Feature: Customer Soft Delete]

**⚠️ Intent: GUESSED**

**What the code shows:**
Customers have a `deleted_at` timestamp. Soft-deleted customers are excluded from normal queries but remain in the database.

**Assumed intention:**
Safety net to prevent accidental permanent deletion. Standard soft-delete hygiene.

**Why this is a guess:**
No comment on retention period. No compliance reference found in the codebase. Could be hygiene practice or a regulatory requirement — impossible to tell from code alone.

**Needs confirmation:**
- Is this soft-delete for operational safety (recoverable for X days), or for regulatory compliance (must retain for Y years)?
- Can deleted customer records ever be archived or moved to cold storage?
- Must deleted records remain queryable by admins indefinitely?

---

## 5. Admin & Management

*(Add sections as needed)*

---

## Pending Sync — Guessed Entries Requiring Human Review

*(Auto-populated during discovery — list all GUESSED entries here for easy scanning)*

| Feature area | Element | Risk if wrong |
|-------------|---------|--------------|
| Registration | Agent Code Field | Could be wrongly used as UID or removed during cleanup |
| Payments | Pending Status | Could trigger wrong automation (retry, escalation) |
| Customers | Soft Delete | Could be archived in violation of compliance rules |

---

## Deferred

*(Entries where human direction is pending — do not act on these)*

| Feature | Question | Opened |
|---------|---------|--------|
| [feature] | [open question] | YYYY-MM-DD |
