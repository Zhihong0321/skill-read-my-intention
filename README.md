# RMINGI - Read My Intention, Not Guess It

**AI can write working code. RMINGI helps AI understand why the code exists.**

AI coding is getting very good.

It can build features.
It can pass tests.
It can ship code.

But there is still one big gap between AI coders and experienced human coders:

**intention**

Good human coders do not start with code first.
They try to make the intention clear first.

They ask:

- Why does this feature exist?
- What business rule does it protect?
- What must never change?
- What looks simple, but is actually important?

AI usually does not do this well.

AI looks for clear instructions, patterns, and structure.
It can build code that works without fully understanding the real reason behind it.

That is where AI codebases start to drift.

## The Missing Piece

The biggest missing piece in AI coding today is not syntax.
It is not code generation.
It is not testing.

It is this:

**AI still struggles to reliably recover and respect human intention.**

One AI guesses.
The next AI builds on that guess.
Then another AI cleans up, refactors, or optimizes based on the wrong reason.

Nothing looks broken at first.
But the system slowly becomes dangerous because the original intention was never made clear.

## Why This Matters

A field can look optional, but be critical.

A status can look technical, but mean manual approval.

A cleanup job can look harmless, but exist for legal compliance.

The code works.
The tests pass.
The AI is still wrong.

## What RMINGI Does

RMINGI is a workflow for AI-built codebases.

It helps AI find places where guessing the reason would be expensive.

RMINGI does not try to document everything.

It focuses on risky intention gaps such as:

- payment states
- auth and permission rules
- optional fields with hidden meaning
- cron jobs and background processing
- soft deletes and retention rules
- integrations and webhooks
- compliance flows
- hidden business constraints

RMINGI separates guesses from truth.

- risky findings go into a draft
- humans confirm or correct them
- confirmed intention goes into a canonical map

## RMINGI v0.3

RMINGI v0.3 is built around five rules:

- Human-only confirmation
- Evidence before inference
- Draft guesses stay air-gapped
- Open questions stay visible
- Focus only on expensive guesses

## The 3 States

- `EVIDENCED` - strong evidence exists, but no human has confirmed it yet
- `OPEN` - the intention is still unclear and needs human review
- `CONFIRMED` - a human confirmed it, and future AI can rely on it

Only humans can move an entry to `CONFIRMED`.

## The Workflow

1. AI gathers evidence before making a guess.
2. AI writes risky findings into `.agents/sync-draft.md`.
3. AI groups them into small review packets by business flow.
4. A human confirms, corrects, or leaves them open.
5. Confirmed entries move into `.agents/intentions.md`.
6. Open entries stay visible until resolved.

## Evidence Ladder

Before inferring intention from code, RMINGI looks here first:

1. `README.md` and project docs
2. `.agents/` memory files
3. ADRs, notes, migrations, and ops docs
4. PRs, issues, and commit history
5. prompts and inline comments
6. code

## Artifacts

- `.agents/sync-draft.md` - temporary review draft, not canonical
- `.agents/intentions.md` - human-confirmed intention map, canonical

## When To Use RMINGI

Use it when:

- a project has had many AI sessions
- a new model is about to work on the repo
- a refactor is coming
- the code works, but nobody is fully sure why it works this way
- a rule feels obvious in code, but may not actually be obvious

## One-Line Summary

**RMINGI helps AI read intention before it changes code.**
