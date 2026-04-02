---
name: read-my-intention
description: Use when an AI-built codebase has grown large and intention drift is suspected — where the AI may have been guessing the WHY behind features rather than reading it. This skill reads the existing codebase, produces a structured Intentions Map organized by UX/UI flows, explicitly flags every guessed intention, and runs a sync dialogue with the human to confirm or correct each guess. The output is .agents/intentions.md — a permanent record of WHY each feature exists, readable by any future AI agent.
---

# Read My Intention (RMI)

This skill recovers and syncs the WHY behind an AI-built codebase.

It does not describe what code does. It captures why features exist — from the human's perspective — in a form any future AI can read before touching that feature.

---

## When To Use This Skill

Use when any of these are true:

- the project has been AI-developed for more than a few months
- multiple AI models have contributed and each may have interpreted intent differently
- the AI made a decision you didn't expect — and you realized it was guessing
- you are onboarding a new AI model and want it oriented before it touches production code
- a refactor or cleanup run is planned and you want to protect features from being misread
- `.agents/intentions.md` does not exist yet

Do not use this skill for a new project before any code is written — at that stage, write intentions alongside building, not before.

---

## Two Phases

### Phase 1 — Discovery

The AI reads the codebase and builds a draft Intentions Map.

**Step 1: Build the UX map**

Scan the codebase for route definitions, view files, page components, and navigation structure. Build a list of all user-facing areas:
- pages and views
- major flows (registration, login, onboarding, checkout, etc.)
- admin and management areas
- API surfaces that drive UI behavior

Do not organize by file path. Organize by **what the human sees and uses**.

**Step 2: For each area, read the implementation**

For each page/flow/feature, read:
- the view or component file
- the data it reads and writes
- the validation and business rules applied
- any special conditions (optional fields, hidden states, role restrictions)

**Step 3: Write intention entries**

For each feature element, decide: is the intention CLEAR or GUESSED?

**CLEAR** — use when the intent is directly derivable from the code:
- a required email field on a login form → clearly authentication
- a price field that runs through a tax calculation → clearly billable amount
- a delete button that is only visible to admin role → clearly admin-only action

**GUESSED** — use when you are inferring intent from partial evidence:
- an optional field with no validation and no downstream usage found
- a field named ambiguously (e.g., "status", "type", "code")
- a feature that was built but has no active callers in the current codebase
- any time the instruction that created this feature was likely short and unexplained

Write every GUESSED entry with full reasoning — not just the guess, but WHY you are guessing and WHAT you need the human to confirm.

**Step 4: Produce the draft**

Write the draft to `.agents/intentions.md` using the format in `templates/intentions.md`.

End the draft with a section listing all GUESSED entries that need human review, in order of risk (higher-risk features first).

---

### Phase 2 — Sync

The AI presents each GUESSED entry to the human and asks for confirmation or correction.

**Sync protocol:**

1. Present one GUESSED entry at a time — do not dump all guesses at once
2. State:
   - what you see in the code
   - what you assumed the intention to be
   - why you are guessing (what information was missing)
   - what specific confirmation you need
3. Wait for the human's response
4. Update the intentions map:
   - if confirmed: change marker from `⚠️ GUESSED` to `✅ CONFIRMED`, record the confirmation
   - if corrected: rewrite the intention with the human's actual answer, mark as `✅ CONFIRMED`
   - if deferred: mark as `⏸ DEFERRED` with the reason
5. Move to next GUESSED entry

Do not skip entries. Do not batch multiple guesses into one question unless they are directly related.

**When sync is complete:**

All entries are either CONFIRMED, DEFERRED, or explicitly marked as OPEN with a reason. Write the final intentions map. Update the maintenance log.

---

## Intention Entry Format

### For CLEAR intentions:

```md
### [Feature or element name]

**Intent: CLEAR**
[One or two sentences stating the intention plainly.]
[Any constraints or rules that follow directly from this intention.]
```

### For GUESSED intentions:

```md
### [Feature or element name]

**⚠️ Intent: GUESSED**

**What the code shows:**
[Factual description of what was found in the code — no interpretation yet.]

**Assumed intention:**
[The AI's best guess at WHY this exists.]

**Why this is a guess:**
[What specific information is missing. What instruction created this feature. What about the code is ambiguous.]

**Needs confirmation:**
- [Specific question 1]
- [Specific question 2]
```

### After sync — confirmed:

```md
### [Feature or element name]

**✅ Intent: CONFIRMED** — [date confirmed]
[The human-confirmed intention, written plainly.]
[constraints and rules that follow from the confirmed intention]
```

---

## Rules

- organize by UX/UI, never by file or module name — the human must be able to navigate this document without reading code
- never write a CLEAR entry for something you actually inferred — CLEAR means the code proves it, not that the guess seems reasonable
- never present more than one GUESSED entry per sync exchange unless they are tightly related
- do not change or refactor any code during this skill — read only, write only to intentions.md
- if a confirmed intention reveals a bug or mismatch in the current code, note it in `.agents/ambiguities.md` — do not fix it in this run
- intentions.md is always organized from the human's perspective — features, flows, pages — never by technical layer

---

## Maintenance

After the initial sync:

- when a new feature is added: create its intention entry before or immediately after building
- when switching AI models: the new model reads intentions.md before any work begins
- when a feature is significantly changed: review and update its intention entry
- when running `residual-cleaner` or `architecture-optimizer`: check intentions.md first — do not clean or optimize what you do not understand the purpose of

---

## Output Location

`.agents/intentions.md` in the user's repo root.

Template: `templates/intentions.md`
