# Intentions Map (Canonical)

- Repo: [repo name]
- Last synced: YYYY-MM-DD
- Last reviewed by: [human name or role]
- RMINGI version: v0.3

> WARNING TO AI AGENTS:
> This file contains human-confirmed business intention only.
> Do not add guesses, inferred meaning, or draft assumptions here.
> If intention is not confirmed, keep it in `.agents/sync-draft.md`.

---

## How To Use This File

- read the relevant business flow before changing code
- treat every entry here as protected intent
- if behavior contradicts this file, trigger a mini-sync

---

## 1. Authentication & Identity

### [Capability: Agent Self-Registration]

- Status: `CONFIRMED`
- Confirmed by: [Human / YYYY-MM-DD]
- Risk if misunderstood: `HIGH` - identity collision and broken onboarding
- Last reviewed: YYYY-MM-DD

**Why it exists:**
The agent code exists to link a new web registration to a pre-approved seminar record created by administrators.

**Critical constraints:**
- the field is optional
- it must never be treated as a universal unique identifier
- future search and indexing logic must not assume every account has an agent code

---

## 2. Background Jobs & Data Lifecycle

### [Capability: Trial User Inactivity Pruning]

- Status: `CONFIRMED`
- Confirmed by: [Human / YYYY-MM-DD]
- Risk if misunderstood: `HIGH` - wrongful deletion and compliance risk
- Last reviewed: YYYY-MM-DD

**Why it exists:**
This job exists to remove inactive trial users under a specific compliance rule. It is not a general cleanup tool.

**Critical constraints:**
- it must apply only to the allowed user segment
- it must never expand into paid or retained accounts without human approval
- performance cleanup must not reuse this job for a broader deletion policy

---

## Add More Flows As Needed
