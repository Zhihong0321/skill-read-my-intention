---
name: read-my-intention
description: RMINGI v0.4. Risk-scanning skill for AI-built codebases. Finds where intention was guessed, gathers evidence before inference, writes findings into an air-gapped `.agents/sync-draft.md`, groups them by business flow, and syncs with a human. Only human-confirmed intentions enter `.agents/intentions.md`. Includes conflict resolution, expense heuristics, and re-sync triggers.
---

# Read My Intention (RMINGI)

This skill recovers and syncs the **WHY** behind an AI-built codebase.

It does not document what code does. It finds places where **the code works but the real intention may still be misunderstood** — and where that misunderstanding would be **expensive**.

---

## Core Rules

1. Evidence comes before inference
2. Only humans can confirm intention
3. Draft guesses must never enter the canonical map by accident
4. Open questions must stay visible until resolved
5. Ignore low-risk and obvious mechanics

---

## Output Files

- `.agents/sync-draft.md`
  - temporary review file
  - may contain `EVIDENCED` and `OPEN` entries
  - **not safe to treat as truth**
- `.agents/intentions.md`
  - canonical intention map
  - contains `CONFIRMED` entries only
  - **safe for future AI to rely on**

---

## The 3 States

| State | Meaning | Set By | Location |
|---|---|---|---|
| `EVIDENCED` | Strong evidence supports a likely intention, but no human has verified it | AI | `sync-draft.md` |
| `OPEN` | Intention is unclear, evidence conflicts, or information is missing | AI | `sync-draft.md` |
| `CONFIRMED` | Human-verified — can be treated as ground truth | **Human only** | `intentions.md` |

**Rule:** Only a human can move an entry to `CONFIRMED`. No amount of model confidence is sufficient.

---

## Evidence Ladder

Before guessing intention from code, gather context in this order:

1. `README.md` and existing project documentation
2. Any `.agents/` memory files (intentions, context, handoffs)
3. ADRs, product notes, migration docs, runbooks, and ops docs
4. PRs, issues, and commit messages
5. Prompts, handoff notes, and inline comments
6. The code itself

**If a layer does not exist or is inaccessible**, note that gap and continue — the absence of documentation is itself evidence.

### Conflicting Evidence Protocol

When sources disagree (e.g., README says one thing, commit history implies another, code does a third):

1. Log **each source** with its specific claim in the draft
2. Mark the entry as `OPEN` — not `EVIDENCED`
3. Surface the conflict explicitly — state which sources disagree and how
4. **Never silently pick a winner** — contradictions are the most dangerous intention gaps
5. Frame the human review question around resolving the specific conflict

---

## What To Scan For

Map the codebase by **business flow**, not by file path.

Look for:

- Payments, billing states, and financial transitions
- Auth and permission boundaries
- Cron jobs, background workers, and scheduled tasks
- Data retention, deletion, and archival rules
- Integrations, webhooks, and third-party API flows
- Optional or conditional fields with hidden downstream meaning
- Hidden support, admin, or operations workflows
- Compliance, audit, and legal constraints

**Do not spend time documenting low-risk or obvious behavior.**

### Expense Heuristic

A guess qualifies as "expensive" if being wrong could cause:

| Consequence | Qualifies? |
|---|---|
| Data loss or corruption | ✅ Always flag |
| Financial error (double charge, lost revenue) | ✅ Always flag |
| Security breach or access escalation | ✅ Always flag |
| Compliance or legal violation | ✅ Always flag |
| Broken integration or silent data loss | ✅ Flag |
| Degraded UX across a wide user segment | ⚠️ Flag if scope is broad |
| Cosmetic mismatch or minor label error | ❌ Ignore |

**Rule of thumb:** If the code touches **money, identity, deletion, external systems, or legal rules** — it qualifies.

---

## Phase 1 — Discovery

### Step 1: Gather Evidence
Read the evidence ladder **before** inferring business meaning from raw code. For each layer you access, note what you found. For each layer you cannot access, note its absence.

### Step 2: Map Core Flows
List the main business flows and risky subsystems.

**A flow is "core" if it touches:**
- Money (payments, billing, subscriptions, refunds)
- Identity (auth, registration, roles, permissions)
- Deletion (soft delete, archival, purging, retention)
- External systems (webhooks, APIs, integrations, email/SMS triggers)
- Legal or compliance (audit trails, consent, data residency)

Group by what the business does, not by technical module names.

### Step 3: Find Expensive Guesses
For each risky area, answer these five questions:

1. What is the code doing?
2. What intention seems likely?
3. What evidence supports that interpretation?
4. What is still missing or contradictory?
5. What is the **concrete risk** if this guess is wrong?

Use these confidence rules:
- Use `EVIDENCED` when the likely intention has strong support from multiple sources, but no human confirmation yet
- Use `OPEN` when the intention is uncertain, evidence conflicts, or critical context is missing

Ignore low-risk cosmetic mismatches.

### Step 4: Write the Draft
Write all risky findings into `.agents/sync-draft.md`.

- Group entries into **review packets** by business flow
- Aim for **3 to 5 related items** per packet
- Each packet should have a stated **risk level** and a one-line explanation of **why this packet matters**

**Never write guessed or unconfirmed intention into `.agents/intentions.md`.**

---

## Phase 2 — Sync

Present **one review packet at a time** to the human.

For each entry, include:

1. What the code is doing
2. The likely intention
3. The evidence found (with source citations)
4. Why the entry is still `EVIDENCED` or `OPEN`
5. The concrete risk if it is misunderstood
6. The exact question that needs human review

After the human replies:

- **If confirmed:** Move the entry into `.agents/intentions.md` as `CONFIRMED`
- **If corrected:** Rewrite it with the human's explanation, then move it as `CONFIRMED`
- **If still unresolved:** Keep it in `.agents/sync-draft.md` as `OPEN`

**Do not hide unresolved questions.**
**Do not delete open items just to finish the run.**

Delete `.agents/sync-draft.md` only when **no** `EVIDENCED` or `OPEN` entries remain.

---

## Canonical Entry Format

Every entry in `.agents/intentions.md` must include:

```yaml
### [Capability or Flow Name]

- Status: CONFIRMED
- Confirmed by: [human name or role] / [YYYY-MM-DD]
- Risk if misunderstood: [CRITICAL|HIGH|MEDIUM] — [one-line risk statement]
- Last reviewed: YYYY-MM-DD

**Why it exists:**
[Plain language explanation of the business intention]

**Critical constraints:**
- [What future AI must NOT change]
- [What rules must be preserved]
- [What scope limits apply]
```

**Do not mark an entry as `CONFIRMED` based on model confidence alone.**

---

## When To Re-Sync

Trigger a mini-sync when:

- A confirmed intention is older than 12 months
- Current code behavior contradicts the recorded intention
- A major refactor touches the flow
- A new AI model is being onboarded to the repo
- A human says "that is not why we built it"
- An `OPEN` item has been unresolved for 3+ sync cycles

---

## What Not To Do

- Do not refactor code during this skill
- Do not turn this into full documentation work
- Do not treat code structure alone as proof of business intent
- Do not bury unresolved questions
- Do not let AI-confirmed guesses become canonical truth
- Do not silently resolve conflicting evidence — surface it
- Do not expand deletion scopes without explicit human approval
