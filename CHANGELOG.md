# Changelog

All notable changes to RMINGI are documented in this file.

---

## [v0.4] — 2026-04-02

**Author:** Claude Opus 4.6 (Thinking)

### Added
- **5 real-world catastrophe examples** in README — soft delete audit trail, double charges, identity collision, cron scope creep, silent webhook failure
- **Conflict resolution protocol** in the Evidence Ladder — when sources disagree, log each claim as `OPEN`, never silently pick a winner
- **Expense heuristic table** — concrete criteria for what makes a guess "expensive enough" to flag
- **Core flow identification criteria** — a flow is "core" if it touches money, identity, deletion, external systems, or legal rules
- **Mermaid workflow diagram** in README showing the full 2-phase flow
- **Structured canonical entry format** with YAML-like schema
- **Worked example** in `examples/payment-flow/` showing RMINGI running against a payment system
- `CHANGELOG.md` — version history with migration notes
- `LICENSE` — MIT License
- `.gitignore`
- **Version history table** in README
- **Repo structure tree** in README
- **Approach comparison table** — RMINGI vs spec-driven vs harness engineering
- **Expanded re-sync triggers** — including "3+ cycles unresolved" rule

### Changed
- README completely rewritten for professional presentation — centered header, shields.io badges, structured tables
- SKILL.md expanded with clearer step-by-step guidance, decision heuristics, and conflict handling
- Templates updated with structured YAML-like entry metadata and clearer AI agent warnings
- `sync-draft.md` template now includes evidence source citations and conflict surface area

### Restored (from v0.2, removed in v0.3)
- The soft delete catastrophe example
- The double charge catastrophe example
- The agent code identity collision example
- The approach comparison table (RMINGI vs spec-driven vs harness)
- The "when to run" trigger table

---

## [v0.3] — 2026-04-02

**Author:** GPT 5.4 Pro

### Changed
- Simplified state model from CLEAR/GUESSED/WEAKLY INFERRED to 3 clean states: `EVIDENCED`, `OPEN`, `CONFIRMED`
- Tightened prose throughout README and SKILL.md
- Removed confidence/risk scoring matrix in favor of simpler state model
- Cleaned up templates with v0.3 branding

### Removed
- Real-world catastrophe examples from README (restored in v0.4)
- Approach comparison table
- Detailed "when to run" trigger table

---

## [v0.2] — 2026-04-02

**Author:** Gemini 3.1 Pro

### Added
- **Air-gapped drafts** — speculation goes to `sync-draft.md`, never directly to canonical `intentions.md`
- **Evidence Ladder** — AI must exhaust docs, memory, and comments before reading code
- **Batched Sync** — human review grouped by business flow packets instead of one-by-one
- **Mine-sweeper approach** — focus only on high-risk guesses, ignore obvious mechanics
- `templates/sync-draft.md` — new template for the air-gapped review draft
- Expanded scope to CRONs, webhooks, middleware, and compliance constraints

### Changed
- Reframed from "documentation skill" to "risk-scanning skill"
- Removed dependency on AI-First Maintenance bundle for standalone use

---

## [v0.1] — 2026-04-02

**Author:** Claude Sonnet 4.6

### Added
- Initial release
- Core concept: "Read My Intention, Not Guess It"
- CLEAR/GUESSED classification taxonomy
- Two-phase process: Discovery + Sync
- 4 real-world intention drift examples
- `SKILL.md` with full two-phase workflow
- `templates/intentions.md` with example entries

---

## Migration Notes

### v0.3 → v0.4
- No breaking changes to the state model (EVIDENCED/OPEN/CONFIRMED remains)
- Templates now include structured YAML-like metadata — old format still works but new entries should use the v0.4 schema
- The `examples/` directory is new — no migration needed

### v0.2 → v0.3
- **Breaking:** State labels changed:
  - `STRONGLY EVIDENCED` → `EVIDENCED`
  - `WEAKLY INFERRED` → `OPEN` (for uncertain) or `EVIDENCED` (for supported)
  - `GUESSED` → removed (use `OPEN` instead)
- Existing `intentions.md` files using v0.2 labels should update to v0.3 terminology
- Existing `sync-draft.md` files should be reviewed and re-labeled

### v0.1 → v0.2
- **Breaking:** State labels changed:
  - `CLEAR` → `STRONGLY EVIDENCED`
  - `GUESSED` → `WEAKLY INFERRED`
- The `sync-draft.md` file is new — v0.1 wrote guesses directly to `intentions.md`
- Existing `intentions.md` files should be audited: any entry without explicit human confirmation should be moved to `sync-draft.md`
