# Intentions Map (Canonical)

> **RMINGI v0.4** — Canonical Intention Map

- Repo: `[repo-name]`
- Last synced: `YYYY-MM-DD`
- Last reviewed by: `[human name or role]`
- RMINGI version: `v0.4`

> ⛔ **WARNING TO AI AGENTS:**
>
> This file contains **human-confirmed business intention only**.
> Do not add guesses, inferred meaning, or draft assumptions here.
> If intention is not confirmed by a human, keep it in `.agents/sync-draft.md`.
>
> Before modifying, refactoring, or cleaning any business flow,
> you **MUST** read the corresponding section here to ensure
> your changes do not violate human intent.

---

## How To Use This File

- **Before coding:** Read the relevant business flow section
- **Treat every entry as protected intent** — do not override without human approval
- **If behavior contradicts this file:** Trigger a mini-sync, do not silently resolve
- **If an entry is >12 months old:** Flag it for re-verification

---

## 1. Authentication & Identity

### [Capability: Agent Self-Registration]

- Status: `CONFIRMED`
- Confirmed by: `[Human Name]` / `YYYY-MM-DD`
- Risk if misunderstood: `CRITICAL` — identity collision and broken onboarding
- Last reviewed: `YYYY-MM-DD`

**Why it exists:**
The agent code field exists so that administrators can physically distribute
pre-approved codes at seminars. Upon web registration, this code links the
newly created account to their physical seminar record.

**Critical constraints:**
- The field is optional — internal agents are registered manually without a code
- `agent_code` must NEVER act as a primary key or unique identifier
- Future search/index tools must not assume every user has an `agent_code`
- Downstream dashboards and reports must handle `null` agent codes gracefully

---

## 2. Background Jobs & Data Lifecycle

### [Capability: Trial User Inactivity Pruning]

- Status: `CONFIRMED`
- Confirmed by: `[Human Name]` / `YYYY-MM-DD`
- Risk if misunderstood: `CRITICAL` — wrongful deletion, compliance risk, contract breach
- Last reviewed: `YYYY-MM-DD`

**Why it exists:**
This daily cron job exists solely for GDPR right-to-be-forgotten compliance
for trial users. It is **not** a general database cleanup or performance tool.

**Critical constraints:**
- Must ONLY delete users with `role = trial`
- Paid users, enterprise users, and retained accounts must NEVER be touched
- The scope of this job must NEVER be expanded without explicit human approval
- Any AI optimizing query performance must not reuse or broaden this job

---

## 3. Payments & Financial Flows

### [Capability: Payment State Machine]

*(Add confirmed payment flow entries here...)*

---

## 4. Integrations & External Systems

### [Capability: Webhook Handlers]

*(Add confirmed integration entries here...)*

---

## 5. Operations & Support

*(Add confirmed operations entries here...)*

---

## Sync Stats

| Metric | Value |
|---|---|
| Total confirmed entries | `0` |
| Last full sync | `YYYY-MM-DD` |
| Open items remaining in `sync-draft.md` | `0` |
| Oldest confirmed entry | `N/A` |
| Entries due for re-verification (>12 months) | `0` |

---

## End of Map
