# Institutional Knowledge - {{PROJECT_NAME}}

**Project**: {{PROJECT_NAME}} - {{PROJECT_DESCRIPTION}}
**Purpose**: Capture learnings, patterns, and solutions to prevent repeated mistakes
**Created**: {{PROJECT_START_DATE}}
**Last Updated**: {{CURRENT_DATE}}

**Entry Count**: 0 / 20 (KB System Upgrade triggers at 20 — schedule review)
**Last Review**: {{CURRENT_DATE}}
**Status**: ✅ Manual mode (file-based)

---

## Overview

This file stores institutional knowledge for {{PROJECT_NAME}} development. It's used by:
- `kb-create` skill - Add new learnings
- `kb-query` skill - Search existing patterns
- `root-cause-analyzer` skill - Document root causes

### When to Upgrade to KB System

**Trigger Conditions** (upgrade when ANY is true):
- Entry count reaches **20**
- File size exceeds **2,000 lines**
- Search takes **>5 minutes** (currently <5 seconds with Cmd+F)
- Major project milestone complete

**Current Status**: Manual file working well. No upgrade needed yet.
**Next Review**: When entry count reaches 15

---

## Patterns

### KB-037 — Two-execution-deep pattern validation across detection-tier features

**Date**: 2026-04-24
**Origin**: Feature 206 delivery retrospective ([delivery.md](../specs/206-misinformation-threat-agent/delivery.md))
**Category**: Architecture — detection-agent pattern reuse

**Pattern**: A new detection-tier feature (F-1, F-2, and onward) re-applies 10 architectural patterns authored-once and confirmed stable across two independent deliveries:

1. Lean-agent shape (≤150 lines, 1 MANDATORY Read, zero MAESTRO) — ADR-023
2. Companion skill catalog with configurable-category-count pattern list — ADR-023 + F-1 convention
3. Regex-alternation minor-bump rule (ADR-030 Decision 8; F-2 is 2nd application, F-3 will be 3rd)
4. Proposed → Accepted dual-commit ADR lifecycle (ADR-027/028/029/030/031)
5. Additive-only shared-reference edits (`finding-format-shared.md` consumers list) — ADR-023 Decision 3
6. F-A2 referential-integrity contract (new producer appends, validator unchanged) — ADR-028
7. Two-part emission gate (keyword match AND structural indicator) — F-1 FR-011
8. 24-file zero-edit invariant (grows +2 per new detection-tier feature) — ADR-023 Decision 2
9. Heuristic A signal-class partition (input-side / sink-sanitization / factual-integrity) — ADR-030 Decision 1 + ADR-031 Decision 2
10. Orchestrator 5-callsite quintet reconciliation (architect-owned per MEDIUM-4) — grows +1 callsite-touch per feature

**When to apply**: Any new detection-tier AI-agent feature (F-3 ASI07, F-4 ASI09, F-5 LLM10, etc.) should treat these 10 patterns as STABLE and reuse the F-1/F-2 pattern library wholesale. Incremental cost is ~0.5 day for pattern-catalog authoring + ~2-3 hours for example regen + ~2 hours for governance overhead (ADR + SC sweep).

**Why it matters**: PRD time-envelopes for F-1 and F-2 were both conservative — F-2 delivered 3–4 days ahead of plan with ~30% under active-hour estimate. F-3+ can safely plan a 1.5-day envelope + 0.5-day buffer when honoring pattern reuse.

**Anti-pattern to avoid**: Re-authoring any of the 10 patterns from scratch. The 5-callsite quintet reconciliation in particular must remain architect-owned (MEDIUM-4) to prevent drift.

**Reference**: `.aod/results/wave5-sc-sweep-206.md`, `specs/206-misinformation-threat-agent/delivery.md` (section "Patterns Validated Two-Execution-Deep")

---

### KB-038 — Verbatim-locked Gemini prompts for image-generation templates

**Date**: 2026-04-25
**Origin**: Feature 212 delivery retrospective ([delivery.md](../specs/212-improve-executive-architecture-infographic/delivery.md))
**Category**: Process improvement — Gemini prompt management

**Problem**: Image-generation prompts that are dynamically composed at runtime drift between iterations. Subjective visual review (the only validation mode for image output) becomes non-reproducible because two consecutive runs can produce different prompts before they ever reach Gemini, making A/B comparison meaningless and making "did the change cause the visual delta or did the prompt drift?" unanswerable.

**Solution**: Lock the entire prompt block VERBATIM in the skill reference (e.g., `.claude/skills/tachi-infographics/references/executive-architecture.md`). Document the verbatim-lock rule explicitly in `gemini-prompt-construction.md` so future maintainers know not to dynamically recompose aesthetic language. Surfaced via FR-212-6 and enforced by tasks T007/T008.

**When to apply**: Any tachi infographic template (or any future Gemini-image generator) whose visual quality depends on consistent aesthetic directives. Prefer a frozen prompt block over a programmatic builder when the prompt drives subjective output that humans review side-by-side.

**Why it matters**: Feature 212's structural review (4/4 SC-212-1 PASS on iteration-1) was credible only because the prompt that produced the image was the exact prompt committed in `executive-architecture.md`. Dynamic composition would have broken the producer-evidence chain that made the absolute structural review trustworthy as a PM-proxy validation.

**Anti-pattern to avoid**: Building prompts via string interpolation or template engines at runtime when the prompt is the unit of A/B comparison. Reserve dynamic composition for fields that are *data*, not *aesthetic instruction*.

**Reference**: `specs/212-improve-executive-architecture-infographic/delivery.md`, `.claude/skills/tachi-infographics/references/executive-architecture.md` (verbatim-locked block), `.claude/skills/tachi-infographics/references/gemini-prompt-construction.md` (lock rule documentation)

---

### KB-039 — Heuristic A enrichment branch as 5/5-dimension cost reducer for detection-tier features

**Date**: 2026-04-26
**Origin**: Feature 219 delivery retrospective ([delivery.md](../specs/219-asi07-tool-abuse-enrichment/delivery.md))
**Category**: Architecture — detection-agent reuse pattern (Heuristic A enrichment branch)

**Pattern**: When a new threat-taxonomy entry maps cleanly to an existing detection agent's signal class, prefer the **enrichment branch** over the new-agent branch. Score the candidate against the 5-dimension reduction checklist at SDR/architect-plan time:

| Dimension | New-agent branch (F-1, F-2 baseline) | Enrichment branch (F-3 reduction) |
|-----------|---------------------------------------|-----------------------------------|
| New agent file (`.claude/agents/tachi/{name}.md`) | +1 file (~150 lines) | 0 files |
| New skill directory (`.claude/skills/tachi-{name}/`) | +1 dir (~3 files, ~400 lines) | 0 dirs |
| Schema bump (`schemas/finding.yaml` regex alternation) | minor version bump | no change |
| Consumers-list edit (`finding-format-shared.md`) | +1 line | no change |
| Functional orchestrator/dispatch edit | +1 entry | no change (cosmetic annotation only ~30s) |

5/5 reductions = 1-day envelope candidate. 3/5 = 2-day envelope (F-2 baseline). ≤2/5 = consider splitting into two features.

**When to apply**: Any new detection-tier feature whose target taxonomy could plausibly extend an existing agent's signal class. Architect adjudicates signal-class identity at SDR time; if disjoint, fall back to new-agent branch. Categories 9 and 10 enrichment of `tool-abuse` for ASI07:2026 (inter-agent / MCP-to-MCP communication) is the precedent — same signal class as existing tool-dispatch coverage (message flow between agent-or-tool endpoints).

**Why it matters**: F-3 completed the build envelope in <24 hours of clock time vs. PRD's 1-day target. Every dimension of edit surface the reduction zeroed out is a dimension of build cost, review cost, and regression-risk surface that did not need to be absorbed. The 24-file zero-edit invariant grew from 22 (F-Foundation) to 24 (post-F-1 + F-2) and held identically through F-3 (F-3 modifies only host files, not invariant files).

**Anti-pattern to avoid**: Defaulting to new-agent for every new taxonomy entry. The Heuristic A signal-class taxonomy (ADR-030 Decision 1; ADR-031 Decision 2 in LLM tier; ADR-032 Decision 1 in AG tier) is the canonical decision rule. Also avoid sub-prefix ID schemes (e.g., `AG-9-{N}`) when enriching — preserve single-namespace ID continuity (`AG-{N}` extends sequentially) for cohesive Agentic-category report rendering. Use the Pattern Category Disambiguation subsection to clarify boundary semantics in prose, not in the ID space.

**Reference**: `specs/219-asi07-tool-abuse-enrichment/delivery.md` (§2.1 5/5-Dimension Reduction Table; §4.1 Replicate Heuristic A When Signal Class Identical), `docs/architecture/02_ADRs/ADR-032-asi07-tool-abuse-enrichment.md` (Decisions D1, D7), `docs/architecture/02_ADRs/ADR-030-output-integrity-agent.md` (Decision 1 — Heuristic A signal-class taxonomy origin), `docs/architecture/02_ADRs/ADR-031-misinformation-agent.md` (Decision 8 — regex-alternation minor-bump rule, F-3 documented asymmetry)

---

## Bug Fixes

*No entries yet. Use `/kb-create` to add the first bug fix.*
