# Worked Example: Payment Flow

This example shows RMINGI running against a small e-commerce payment system.

It demonstrates the full lifecycle:
1. **before.md** — What the codebase looks like before RMINGI (the dangerous state)
2. **sync-draft.md** — What the AI's air-gapped draft looks like after discovery
3. **after.md** — The confirmed intention map after a human completes the sync

## The Scenario

A SaaS billing system has been built over 8 months by 3 different AI models.
The code works. Tests pass. But nobody can say with certainty:

- What `pending` actually means in the payment state machine
- Whether soft-deleted accounts are kept for UX safety or legal compliance
- Whether the retry cron can safely re-charge a card
- Who is allowed to issue a refund

Each AI made reasonable guesses. Each guess compounded on the last.
RMINGI's job is to surface these before the next AI makes a catastrophic change.
