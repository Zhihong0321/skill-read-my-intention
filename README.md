# RMINGI — Read My Intention, Not Guess It (v0.2)

> *The AI built it. It works. But did it build it for the right reason?*

**RMINGI** is an operational process for surfacing and syncing the **WHY** behind AI-built codebases. It is designed to find hidden assumptions that pose a risk to long-running, multi-model AI projects.

---

## The Problem

When AI builds features from short instructions, it guesses the business intent. The code works. It ships. But the guess is recorded nowhere. 

Six months and thirty AI sessions later, a new AI agent reads that code and makes a *second* guess built on top of the first. Eventually, you have a codebase where every part works, but nobody can say with confidence why it was built the way it was. 

That is **Intention Drift**.

---

## The Shift: From Documentation to Risk Reduction

RMINGI v0.2 makes a massive shift in philosophy: 
**"Don’t ask the AI to explain every feature. Ask it to identify where guessing the reason would be expensive."**

We do not use RMINGI to document that an email field accepts emails. We use it to mine-sweep for inferred business rules, compliance triggers, and hidden background processes. 

### 🔴 Example: The Soft Delete That Destroyed an Audit Trail
> **Instruction given:** *"Don't permanently delete customer records."*
> **What the AI built:** A soft-delete pattern (`deleted_at` timestamp).
> **What the AI assumed:** Safety net for accidental deletion. Operational hygiene.
> **What happened months later:** A "performance-optimizing AI" archived 80,000 "deleted" records to cold storage to speed up query times. A compliance audit later requested 3 years of customer history, and the records weren't in the active database. Huge legal exposure.
> 
> **The unstated intent:** Soft-deletion was for *regulatory compliance*. Records had to remain in the primary database for 7 years. 
> **How RMINGI catches it:** RMINGI flags `deleted_at` as a `WEAKLY INFERRED` intent with a `HIGH` risk rating. It batches the question to the human: *"Is this soft-delete for operational safety, or regulatory compliance?"*

---

## The 2-Phase Process

RMINGI is executed *after* a project has grown, or when handing off to a new model.

### Phase 1: Discovery (The Mine-Sweeper)
The AI scans the codebase using an **Evidence Ladder** (checking docs, comments, and project memory *before* reading code). It maps out Core Business Flows—including UI, background jobs, auth middleware, and integrations. 

It hunts for structural decisions driven by inferred business rules. These guesses are **air-gapped** into a temporary file (`.agents/sync-draft.md`) so future AI agents do not accidentally treat speculative assumptions as canonical policy.

### Phase 2: Sync (Batched Realignment)
The AI groups the risky guesses into **Review Packets** by business flow (e.g., "The Payment Lifecycle"). It presents them to the human in tight, easy-to-review batches.

Once the human corrects or confirms the intent, the AI moves the entry into the canonical `.agents/intentions.md`.

---

## Why Not Spec? Why Not Harness?

| Approach | Answers | Misses |
|----------|---------|--------|
| Spec-driven development | Did the AI build what I described? | WHY it was described that way at the time |
| Harness engineering | Did the AI build it consistently? | Whether it was the right thing to build |
| **RMINGI** | **Does the AI know WHY this feature exists?** | — |

Specs and harnesses dictate the *WHAT* and constrain the *HOW*. RMINGI aligns the *WHY*. A builder with perfect blueprints and perfect guardrails will still build the wrong house if they fundamentally misunderstood what the family needs it for.

---

## When To Run RMINGI

| Trigger | Action |
|---------|--------|
| Project is 6+ months old | Full initial sync — run on the whole codebase. |
| Switching AI models | Run discovery to orient the new model, sync any new gaps. |
| Planning a major refactor | Require the AI to read the affected `intentions.md` area first. |
| The Map gets "Stale" | If a confirmed intention contradicts reality or is >1 year old, re-sync it. |

---

## Output

A single canonical file in your project:

```
.agents/intentions.md
```

Organized by Business Flows and Capabilities. Any AI agent reads this before working on a feature, refactoring, or optimizing. 

---

## Usage

**As a standalone skill:**
1. Point your AI assistant at `SKILL.md`
2. Say: *"Run read-my-intention on this codebase"*
3. The AI produces the air-gapped discovery draft and begins the Batched Sync dialogue.

**As part of the AI First Maintenance bundle:**
- RMINGI is invoked by the Front Desk when intention drift is suspected.
- The output feeds into `context-navigator` and acts as a constraint layer for all architectural and refactoring skills.

---

## The Name
**RMINGI** — *Read My Intention, Not Guess It*

Code changes. Specs age. Architecture evolves. Layer upon layer of AI assumption is added to the stack. But **why a feature exists** — the core business intention — is absolute. RMINGI exists to make sure the AI is reading that intention, not guessing it.
