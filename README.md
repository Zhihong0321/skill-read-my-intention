# RMINGI — Read My Intention, Not Guess It

> *The AI built it. It works. But did it build it for the right reason?*

**RMINGI** is a method and skill for surfacing and syncing the **WHY** behind AI-built codebases — before wrong guesses compound into production problems.

---

## The Problem

When AI builds features from short instructions, it guesses intent. Most of the time the guess is close enough. The code works. Tests pass. It ships.

But the guess is recorded nowhere. The next AI session reads the same code and makes a second guess — built on top of the first. Six months, four models, and thirty sessions later, you have a codebase where **every part works, but nobody can say with confidence why it was built the way it was.**

That is **intention drift** — and it is the most expensive silent failure in long AI-driven development.

---

## Real Examples

### 🔴 Example 1 — The Optional Field That Couldn't Be Touched

**The instruction:**
> *"Add agent code field to registration. Optional."*

**What the AI built:** An optional text input, no validation, no downstream usage found.

**What the AI assumed:** Nice-to-have cosmetic field. Low value. Could be removed.

**What actually happened:**
- Session 8: A search refactor AI indexed agent code as a unique identifier
- Session 14: A cleanup AI flagged it as "unused" and soft-removed it
- Session 22: The admin panel broke — the entire pre-assignment workflow depended on it

**The actual intention:**
> *Agent code is pre-assigned by admins BEFORE an agent self-registers. It is never a UID. It must never be required. It must survive even when blank. It must appear on the admin view at all times.*

Not one word of that was in the instruction. The AI had no way to know. **RMINGI would have surfaced it as a guess and asked.**

---

### 🔴 Example 2 — The Status Field That Caused Double Charges

**The instruction:**
> *"Add a pending status to payments."*

**What the AI built:** A `pending` state in the payment state machine, treated as "initiated but not yet confirmed."

**What the AI assumed:** Pending = in-flight, safe to retry automatically if it times out.

**What actually happened:**
- Session 11: An auto-retry feature was added for timed-out pending payments
- Session 11 shipped. 72 hours later: duplicate charges. Refund requests. Support fire.

**The actual intention:**
> *"Pending" means the payment is waiting for manual bank approval. It must NEVER be auto-retried. A human must review and approve or reject it.*

The word "pending" encoded a business rule that took months of bank negotiation to establish. The AI read it as a technical state. **RMINGI would have flagged "pending" as a guessed intention and asked what manual action it requires.**

---

### 🔴 Example 3 — The Soft Delete That Destroyed the Audit Trail

**The instruction:**
> *"Don't permanently delete customer records."*

**What the AI built:** A soft-delete pattern — `deleted_at` timestamp, records hidden from normal queries.

**What the AI assumed:** Safety net for accidental deletion. Standard practice.

**What actually happened:**
- Session 19: A performance AI saw 80,000 "deleted" records slowing queries and archived them to cold storage
- Three months later: a compliance audit requested 3 years of customer transaction history
- The archived records were not in the queryable database. Legal exposure.

**The actual intention:**
> *Customer records are soft-deleted for regulatory compliance. They must remain in the primary database, fully queryable, for a minimum of 7 years. Archiving or moving them violates the compliance requirement.*

The compliance constraint was the reason for soft delete. Without it, soft delete just looks like a hygiene pattern. **RMINGI would have flagged the retention requirement as unknown and asked.**

---

### 🔴 Example 4 — The Display Field That Was Actually a Business Engine

**The instruction:**
> *"Show when each agent was last active on the dashboard."*

**What the AI built:** A `last_seen` column, updated on login, displayed in the agent list.

**What the AI assumed:** Cosmetic display feature. Low priority. Good cleanup candidate.

**What actually happened:**
- Session 31: A UI cleanup AI removed `last_seen` from the dashboard ("rarely viewed, low value")
- The agent auto-deactivation scheduler silently stopped firing
- Inactive agents kept receiving leads for 6 weeks before anyone noticed

**The actual intention:**
> *`last_seen` is not a display feature. It is the input to the auto-deactivation rule: agents inactive for 30 days are deactivated. The dashboard display is a side effect.*

**RMINGI would have flagged `last_seen` as possibly more than cosmetic and asked whether it feeds any downstream logic.**

---

## How RMINGI Solves It

RMINGI is a two-phase process that runs **after** the project is built — specifically designed for large, long-running, AI-developed codebases.

### Phase 1 — Discovery

The AI reads the existing codebase and produces a draft **Intentions Map** — organized by what the human sees (pages, flows, features), not by file paths.

For each feature or UI element, the AI declares one of two states:

```
✅ CLEAR   — intent is directly derivable from the code
⚠️ GUESSED — intent was inferred; human must confirm
```

Every `GUESSED` entry includes:
- **What the code shows** — factual, no interpretation
- **Assumed intention** — the AI's best guess
- **Why this is a guess** — what information is missing
- **What needs confirmation** — specific questions for the human

### Phase 2 — Sync

The AI presents each guessed entry to the human — one at a time. The human corrects or confirms. The AI updates the Intentions Map.

```
⚠️ GUESSED  →  human confirms  →  ✅ CONFIRMED
⚠️ GUESSED  →  human corrects  →  ✅ CONFIRMED (with real intention)
⚠️ GUESSED  →  human defers   →  ⏸ DEFERRED (reason noted)
```

When complete: `.agents/intentions.md` is the shared record of WHY. Any AI agent reads it before touching any feature area.

---

## Why Not Spec? Why Not Harness?

| Approach | Answers | Misses |
|----------|---------|--------|
| Spec-driven development | Did the AI build what I described? | WHY it was described that way |
| Harness engineering | Did the AI build it consistently? | Whether it was the right thing to build |
| **RMINGI** | **Does the AI know WHY this feature exists?** | — |

Specs and harnesses are input constraints. RMINGI is shared understanding. A builder with perfect blueprints and perfect guardrails can still build the wrong house if they never understood what the family needs it for.

---

## When To Run It

| Trigger | Action |
|---------|--------|
| Project is 6+ months old | Full initial sync — run on the whole codebase |
| Switching AI models | Sync before new model touches production code |
| AI makes an unexpected decision | Spot sync on that feature area |
| Planning a major refactor | Sync affected feature areas first |
| Adding a significant new feature | Create intention entry before building |

---

## Output

A single structured file in your project:

```
.agents/intentions.md
```

Organized by user-facing areas — not by code structure. Any AI agent reads this before working on a feature. Before cleaning. Before refactoring. Before removing anything.

---

## Usage

**As a standalone skill:**
1. Point your AI assistant at `SKILL.md`
2. Say: *"Run read-my-intention on this codebase"*
3. The AI produces the discovery draft and begins the sync dialogue

**As part of the AI First Maintenance bundle:**
- RMINGI is invoked by the Front Desk when intention drift is suspected
- The output feeds into `context-navigator` and informs all specialist skills

---

## The Name

**RMINGI** — *Read My Intention, Not Guess It*

Because **intention is the one thing that doesn't drift.**

Code changes. Specs age. Architecture evolves. Model after model adds its own interpretation layer. But **why a feature exists** — that's absolute. It comes from the human. It stays true until the human changes it.

The method exists to make sure the AI reads it — not guesses it.
