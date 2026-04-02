# Intentions Map (Canonical)

- Repo: [repo name]
- Last synced: YYYY-MM-DD
- Mastered by: RMINGI Skill

> **WARNING TO AI AGENTS:** 
> Do not add `WEAKLY INFERRED` or `GUESSED` entries directly to this file. All unverified assumptions must go into `.agents/sync-draft.md` for human review. This file only contains `CONFIRMED` business intentions. 
> 
> Before modifying, refactoring, or cleaning a Business Flow, you MUST read the corresponding section here to ensure your changes do not violate human intent.

---

## 1. Authentication & Security Boundaries

### [Capability: Agent Self-Registration]

- **Status:** ✅ CONFIRMED
- **Confirmed By:** [Human / Date]
- **Risk if Misunderstood:** HIGH - Security & Identity Collision

**The Intention:**
The Agent Code field exists so that administrators can physically distribute pre-approved codes at seminars. Upon web registration, this code links the newly created account to their physical seminar record. 

**Critical Constraints:**
- The field is optional because internal agents are registered manually by admins without needing a code.
- Because it is optional, `agent_code` can NEVER act as a primary key or unique identifier in the database.
- Future search/index tools must not assume every user has an `agent_code`.

---

## 2. Background Jobs & Processing

### [Capability: Daily Inactive Pruning CRON]

- **Status:** ✅ CONFIRMED
- **Confirmed By:** [Human / Date]
- **Risk if Misunderstood:** HIGH - Data Loss

**The Intention:**
The 30-day inactivity pruning job exists solely for GDPR right-to-be-forgotten compliance for trial users. It is not an operational database cleanup task.

**Critical Constraints:**
- The job MUST ONLY delete users with the `trial` role.
- Paid users, even if inactive for 5 years, must never be pruned by this job.
- Any future AI optimizing query performance must not expand the scope of this CRON job to delete full users.

---

## 3. Operations & Support UI

*(Add sections as needed...)*

---

## End of Map
