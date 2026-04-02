---
name: read-my-intention
description: RMINGI v0.3. Use to find high-risk intention gaps in an AI-built codebase. This skill does not document everything. It gathers evidence, finds expensive guesses, writes them into an air-gapped `.agents/sync-draft.md`, and helps a human confirm the real intention before future AI changes the system.
---

# Read My Intention (RMINGI)

RMINGI is a risk-scanning skill for AI-built codebases.

Its job is simple:

**find places where the code may work, but the real intention may still be misunderstood**

This skill does not explain every feature.
It focuses on expensive guesses.

---

## Core Rules

- evidence comes before inference
- only humans can confirm intention
- draft guesses must never enter the canonical map by accident
- open questions must stay visible until resolved
- ignore low-risk and obvious mechanics

---

## Output Files

- `.agents/sync-draft.md`
  - temporary review file
  - may contain `EVIDENCED` and `OPEN` entries
  - not safe to treat as truth
- `.agents/intentions.md`
  - canonical map
  - contains `CONFIRMED` entries only
  - safe for future AI to rely on

---

## The 3 States

- `EVIDENCED`
  - strong evidence exists in docs, history, or code
  - still not human-confirmed
- `OPEN`
  - intention is still unclear
  - human review is needed
- `CONFIRMED`
  - human-confirmed
  - can be treated as ground truth

Only a human can move an entry to `CONFIRMED`.

---

## Evidence Ladder

Before guessing intention from code, gather context in this order:

1. `README.md` and existing project docs
2. any `.agents/` memory files
3. ADRs, product notes, migrations, runbooks, and ops docs
4. PRs, issues, and commit history
5. prompts, handoff notes, and inline comments
6. the code itself

If a layer does not exist or is inaccessible, note that gap and continue.

---

## What To Scan For

Map the codebase by business flow, not by file path.

Look for:

- payments and billing states
- auth and permission boundaries
- cron jobs and background workers
- data retention and deletion rules
- integrations and webhook flows
- optional fields with hidden meaning
- hidden support or admin workflows
- anything that could cause data loss, security problems, compliance risk, or broken money flow

Do not spend time documenting low-risk or obvious behavior.

---

## Phase 1 - Discovery

### Step 1: Gather Evidence

Read the evidence ladder before inferring business meaning from raw code.

### Step 2: Map Core Flows

List the main business flows and risky subsystems.
Group by what the business does, not by technical module names.

### Step 3: Find Expensive Guesses

For each risky area, ask:

- what is the code doing?
- what intention seems likely?
- what evidence supports that?
- what is still missing?
- what is the risk if this guess is wrong?

Use these confidence rules:

- use `EVIDENCED` when the likely intention has strong support, but no human confirmation yet
- use `OPEN` when the intention is still uncertain or conflicting

Ignore low-risk cosmetic mismatches.

### Step 4: Write the Draft

Write all risky findings into `.agents/sync-draft.md`.

Group entries into small review packets by business flow.
Aim for 3 to 5 related items per packet.

Never write guessed or unconfirmed intention into `.agents/intentions.md`.

---

## Phase 2 - Sync

Present one review packet at a time to the human.

For each entry, include:

- what the code is doing
- the likely intention
- the evidence found
- why the entry is still `EVIDENCED` or `OPEN`
- the risk if it is misunderstood
- the exact question that needs human review

After the human replies:

- if confirmed, move the entry into `.agents/intentions.md` as `CONFIRMED`
- if corrected, rewrite it with the human's explanation, then move it into `.agents/intentions.md` as `CONFIRMED`
- if still unresolved, keep it in `.agents/sync-draft.md` as `OPEN`

Do not hide unresolved questions.
Do not delete open items just to finish the run.

Delete `.agents/sync-draft.md` only when no `EVIDENCED` or `OPEN` entries remain.

---

## Canonical Entry Rules

Every entry in `.agents/intentions.md` must include:

- capability or flow name
- status: `CONFIRMED`
- confirmed by: human name or role, plus date
- why it exists
- critical constraints
- risk if misunderstood
- last reviewed date

Do not mark an entry as `CONFIRMED` based on model confidence alone.

---

## When To Re-Sync

Trigger a mini-sync when:

- a confirmed intention is older than 12 months
- current behavior contradicts the recorded intention
- a major refactor touches the flow
- a new AI model is being onboarded
- a human says, "that is not why we built it"

---

## What Not To Do

- do not refactor code during this skill
- do not turn this into full documentation work
- do not treat code structure alone as proof of business intent
- do not bury unresolved questions
- do not let AI-confirmed guesses become canonical truth
