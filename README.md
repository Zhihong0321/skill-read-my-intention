# 🧠 RMINGI — Read My Intention, Not Guess It

[![Version](https://img.shields.io/badge/version-v0.2-blue.svg)]()
[![Methodology](https://img.shields.io/badge/methodology-risk_reduction-orange.svg)]()
[![Target](https://img.shields.io/badge/target-AI_First_Codebases-brightgreen.svg)]()

> *The AI built it. It works. But did it build it for the right reason?*

**RMINGI** is an operational safety-net process for surfacing and syncing the **WHY** behind AI-built codebases. It is designed to find hidden assumptions that pose a massive risk to long-running, multi-model AI projects.

---

## 🚨 The Threat: Intention Drift

When AI builds features from short instructions, it guesses the business intent. The code works. It ships. But the guess is recorded nowhere. 

Six months and thirty AI sessions later, a new AI agent reads that code and makes a *second* guess built on top of the first. Eventually, you have a codebase where every part works, but nobody can say with confidence why it was built the way it was. 

**Intention drift is the most expensive silent failure in AI-driven development.** 

---

## ⚡ What's New in v0.2?

RMINGI v0.2 makes a massive shift in philosophy based on real-world friction: 

**"Don’t ask the AI to explain every feature. Ask it to identify where guessing the reason would be expensive."**

1. **The Mine-Sweeper Shift:** We no longer document obvious things (e.g., "The email field accepts emails"). RMINGI explicitly hunts for inferred business rules, compliance triggers, background jobs, and auth middleware.
2. **Air-Gapped Drafts:** Speculation no longer goes straight into the canonical file. Guesses are air-gapped into `.agents/sync-draft.md` so future AI agents are not poisoned by unverified assumptions.
3. **Batched Realignment:** The AI groups risky guesses into "Review Packets" by flow (e.g., "The Payment Lifecycle") instead of asking you 50 questions one-by-one.
4. **The Evidence Ladder:** The AI is strictly commanded to read Docs, Memory, and Comments *before* it tries to infer intent from raw code.

---

## 🔴 Real Examples of Intention Drift

Why do you need a mine-sweeper? Because code looks benign until it isn't.

### Example 1: The Soft Delete That Destroyed an Audit Trail
> **Instruction:** *"Don't permanently delete customer records."*
> **AI Built:** A standard soft-delete pattern (`deleted_at` timestamp).
> **AI Assumed:** Operational hygiene. A safety net for accidental clicks.
> **The Fallout:** A "performance-optimizing AI" archived 80,000 "deleted" records to cold storage to speed up queries. A compliance audit later requested 3 years of user history. Huge legal exposure.
> 
> **How RMINGI catches it:** It flags `deleted_at` as a `WEAKLY INFERRED` intent with `HIGH` risk (Data/Compliance) and asks the human: *"Is this soft-delete for UI safety, or for legal retention?"*

### Example 2: The Status Field That Caused Double Charges
> **Instruction:** *"Add a pending status to payments."*
> **AI Built:** A `pending` state in the database.
> **AI Assumed:** "Pending" means the gateway is slow. Safe to auto-retry.
> **The Fallout:** Another AI added a cron job to retry timed-out pending payments. 72 hours later: duplicate charges, refund requests, support fires.
>
> **How RMINGI catches it:** It detects the state machine and asks: *"Does `pending` resolve automatically via webhook, or does it wait for manual accounting approval? Can it be retried?"*

---

## 🔄 The 2-Phase Process

RMINGI is executed *after* a project has grown, or when handing off to a new model.

### Phase 1: Discovery (The Mine-Sweeper)
The AI scans the codebase using the **Evidence Ladder**. It maps out Core Business Flows—including UI, background jobs, auth middleware, and integrations. 

It hunts for structural decisions driven by inferred business rules. These guesses are **air-gapped** into a temporary file (`.agents/sync-draft.md`).

### Phase 2: Sync (Batched Realignment)
The AI groups the risky guesses into **Review Packets** by business flow. It presents them to you in tight, easy-to-review batches.

Once you correct or confirm the intent, the AI moves the entry into the canonical `.agents/intentions.md`.

---

## 📊 Approach Comparison

| Approach | Primary Question it Answers | The Blind Spot |
|----------|---------|--------|
| **Spec-Driven** | Did the AI build what I described? | *WHY* it was described that way at the time. |
| **Harness Engineering** | Did the AI build it consistently? | Whether it was the *right* thing to build. |
| **RMINGI** | **Does the AI know WHY this exists?** | — |

Specs and harnesses dictate the *WHAT* and constrain the *HOW*. RMINGI aligns the *WHY*. A builder with perfect blueprints and perfect guardrails will still build the wrong house if they fundamentally misunderstood what the family needs it for.

---

## ⏱️ When To Run RMINGI

| Trigger | Action |
|---------|--------|
| Project is 6+ months old | Full initial sync — run on the whole codebase. |
| Switching AI models | Run discovery to orient the new model, sync any new gaps. |
| Planning a major refactor | Require the AI to read the affected `intentions.md` area first. |
| The Map gets "Stale" | If a confirmed intention contradicts reality or is >1 year old, re-sync it. |

---

## 📁 Artifacts

- `.agents/intentions.md` — The single canonical map organized by Business Flows. Any AI agent reads this before working on a feature, refactoring, or optimizing. 
- `.agents/sync-draft.md` — The volatile, air-gapped scratchpad used only during the Sync phase.

---

## 🚀 Usage

**As a standalone skill:**
1. Put `SKILL.md` in your project where your AI can read it.
2. Say: *"Run read-my-intention on this codebase"*
3. The AI produces the air-gapped discovery draft and begins the Batched Sync dialogue with you.

**As part of the [AI First Maintenance Bundle](https://github.com/Zhihong0321/skill-ai-first):**
- RMINGI is invoked by the Front Desk when intention drift is suspected.
- The output feeds into `context-navigator` and acts as a strict structural constraint for architectural skills.

---

## 🏷️ The Core Belief
**RMINGI** — *Read My Intention, Not Guess It*

Code changes. Specs age. Architecture evolves. Layer upon layer of AI assumption is added to the stack. But **why a feature exists** — the core business intention — is absolute. RMINGI exists to make sure the AI is reading that intention, not guessing it.
