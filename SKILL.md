---
name: read-my-intention
description: RMINGI v0.2. Use to recover and align the business intention behind an AI-built codebase. This skill does not document everything; it acts as a risk-scanner. It finds areas where intentions were guessed, places them in an air-gapped `.agents/sync-draft.md`, groups them by business flow, and syncs with the human. Confirmed choices are permanently stored in `.agents/intentions.md`.
---

# Read My Intention (RMINGI)

This skill recovers and syncs the **WHY** behind an AI-built codebase.

It does not document what code does. It acts as a targeted mine-sweeper, hunting for hidden assumptions, inferred business rules, and undocumented constraints that pose a risk to future development.

---

## The Workflow: 2 Phases

### Phase 1 — Discovery (Mine-Sweeping for Risk)

The AI scans the codebase, prioritizes high-risk domains, and builds a draft of assumptions. Speculation is strictly air-gapped into a draft file so future agents do not read guesses as facts.

**Step 1: The Evidence Ladder**
Before guessing intent from code, gather context. Read in this order:
1. `README.md` and any existing documentation files
2. Any `.agents/` memory files (if they exist)
3. Prompts or inline code comments
4. The code itself

**Step 2: Map the Core Business Flows**
Identify the primary capabilities of the system. Do not organize by file paths or UI alone. Look for:
- Core UI flows (Checkout, Registration, Dashboards)
- Background processes (CRON jobs, Schedulers, Syncs)
- Security boundaries (Auth middleware, Role checks)
- Integrations (Payment hooks, Third-party APIs)
- Data lifecycle (Archiving, Soft deletes)

**Step 3: Hunt for "Expensive Guesses"**
Do not document obvious mechanics (e.g., "The email field accepts email"). Hunt for structural decisions driven by inferred business rules. 

Classify assumptions using the **Confidence & Risk Model**:
- **Confidence:** 
  - `STRONGLY EVIDENCED`: Intent is explicitly stated in comments/docs.
  - `WEAKLY INFERRED`: Intent was guessed from variable names or structure. Focus your attention here.
- **Risk if wrong:**
  - `HIGH`: Data loss, compliance violation, security hole, broken payment.
  - `MEDIUM`: Broken UX flow, degraded performance.
  - `LOW`: Cosmetic mismatch. (Ignore these).

**Step 4: Produce the Sync Draft**
Write the findings to a temporary artifact: `.agents/sync-draft.md`. 
Group all `WEAKLY INFERRED` entries into "Review Packets" by flow (e.g., "The Registration Cycle"). 

---

### Phase 2 — Sync (Batched Realignment)

The AI presents the Review Packets to the human to correct, refine, or confirm.

**Sync Protocol:**
1. Do not ask questions one-by-one. Present **Review Packets** (max 3-5 related features at a time) grouped by Business Flow.
2. For each packet, state:
   - What the code is doing.
   - What you assumed the business intention is.
   - Why you suspect it might be a guess.
   - The concrete risk if the guess is wrong.
3. Wait for the human's response to the packet.
4. If the human confirms or corrects the intent:
   - Move the entry from `.agents/sync-draft.md` to the canonical `.agents/intentions.md`.
   - Add the human's exact reasoning.
5. Move to the next packet.

**When sync is complete:**
Delete `.agents/sync-draft.md` if empty. `.agents/intentions.md` is now the shared, canonical record of WHY.

---

## Intention Entry Schema

In `.agents/intentions.md`, every entry must follow this schema:

```md
### [Feature or Capability Name]

- **Status:** ✅ CONFIRMED
- **Confirmed By:** [Human/Model Date]
- **Risk if Misunderstood:** [High/Med] - [Brief risk statement]

**The Intention:**
[Plain language statement of WHY this feature exists and what business rule it serves.]

**Critical Constraints:**
- [What future AIs MUST NOT change]
- [What rules must be preserved]
```

## Maintenance & Change Workflows

After the initial RMINGI sync, the map becomes an operational tool.

- **Before Refilling/Architecture Changes:** An AI must read the relevant sections of `intentions.md` to ensure structural changes don't violate business intent.
- **When Not to Trust the Map:** If a `CONFIRMED` intention is more than 12 months old, or directly contradicts current system behavior, an AI should flag it and trigger a mini-sync to ask the human if the intention has evolved.
- **New Features:** A new feature's intention should be added to `intentions.md` *before* code is written, ensuring intent leads execution.
