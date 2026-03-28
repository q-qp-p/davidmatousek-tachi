# Institutional Knowledge - tachi

**Project**: tachi - Automated threat modeling toolkit extending STRIDE with AI-specific threat agents for agentic applications
**Purpose**: Capture learnings, patterns, and solutions to prevent repeated mistakes
**Created**: {{PROJECT_START_DATE}}
**Last Updated**: 2026-03-28

**Entry Count**: 7 / 20 (KB System Upgrade triggers at 20 — schedule review)
**Last Review**: 2026-03-21
**Status**: ✅ Manual mode (file-based)

---

## Overview

This file stores institutional knowledge for tachi development. It's used by:
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

### PAT-001: Wave-Based Parallelism for Content-Heavy Features

**Date**: 2026-03-21
**Feature**: 001 — Project Skeleton & Interface Contract
**Category**: Process / Execution Strategy

**Context**: Feature 001 required 33 tasks producing 119 files (all markdown + YAML, zero runtime code). Tasks were organized into 6 execution waves with parallel markers `[P]` on independent tasks.

**Pattern**: Group tasks into waves by dependency order, mark independent tasks with `[P]`, and execute each wave with maximum parallelism. Content-only features (markdown/YAML) have high parallelism potential because most files have no cross-dependencies until a verification phase.

**Result**: 33 tasks completed in a single session. Wave 3 achieved maximum parallelism with 15 simultaneous tasks across 3 independent user stories. The Team-Lead's 3-4 hour estimate held because parallel execution compressed wall-clock time.

**When to Apply**: Any feature where deliverables are independent files (agent prompts, schemas, documentation, templates). Less applicable to features with runtime code where compilation order and test dependencies constrain parallelism.

**Quality Score**: 8/10

### PAT-002: Parallel Agent Validation Across User Stories

**Date**: 2026-03-22
**Feature**: 005 — STRIDE Threat Agents
**Category**: Process / Execution Strategy

**Context**: Feature 005 required validating 6 STRIDE threat agents across 5 user stories (41 tasks). User stories 1–3 each targeted 2 agents independently (Spoofing+Tampering, Repudiation+InfoDisclosure, DoS+PrivilegeEscalation), while US4 and US5 handled cross-agent consistency and end-to-end integration.

**Pattern**: When a feature produces multiple independent domain artifacts (e.g., 6 agents), organize user stories so each story validates a subset of artifacts in isolation. This enables Wave 3 (the implementation-heavy wave) to run 3 parallel tracks — one per user story. Cross-cutting concerns (consistency, integration) come in later waves after individual artifacts are validated.

**Result**: 41 tasks completed in ~1 day. Wave 3 achieved 3-way parallelism across US1/US2/US3 with zero cross-dependencies. US4 (cross-agent consistency) and US5 (integration) ran sequentially afterward but were lightweight because individual agent quality was already validated.

**When to Apply**: Any feature where multiple peer artifacts (agents, schemas, templates) need creation and validation. Structure user stories around artifact subsets, not phases. Less applicable when artifacts have tight cross-dependencies that prevent independent validation.

**Quality Score**: 8/10

### PAT-003: Phased Delivery with Per-Story Checkpoints

**Date**: 2026-03-22
**Feature**: 010 — Deduplication & Risk Rating
**Category**: Process / Delivery Strategy

**Context**: Feature 010 required 24 tasks across 3 user stories (correlation detection, deduplicated counts, risk calibration) plus setup and polish phases. Each user story had clear input/output boundaries and an independent validation test.

**Pattern**: Structure implementation into phases aligned with user stories, each ending with a validation checkpoint. Phase 1 (Setup) establishes shared foundations. Phases 2-4 implement one user story each with an independent test. Phase 5 (Polish) handles cross-cutting concerns. This creates natural pause points where partial delivery already provides value — e.g., correlation detection (US-1) is useful even without deduplicated counts (US-2).

**Result**: 24 tasks completed same-day. Each phase checkpoint confirmed correctness before building on it. US-3 (risk calibration) was independent of US-1/US-2, enabling parallel development. The phased approach also made the tasks.md easier for reviewers to validate — each phase was self-contained with clear success criteria.

**When to Apply**: Any feature with multiple user stories where each story has independent validation criteria. Structure phases around stories, not technical layers. Less applicable when user stories are tightly coupled and cannot be validated independently.

**Quality Score**: 8/10

---

### PAT-004: SARIF 2.1.0 Maps Naturally to STRIDE Threat Models

**Date**: 2026-03-22
**Context**: Delivery retrospective for Feature 012 — SARIF Output Generation. Estimated: 1-2 days, Actual: 1 day.

**Problem**:
Needed to convert STRIDE + AI threat model findings into SARIF 2.1.0 format for CI/CD pipeline integration (GitHub Code Scanning, Azure DevOps).

**Solution**:
The STRIDE category-to-rule mapping and severity-to-CVSS alignment translate directly into SARIF's `reportingDescriptor` and `result` objects. A well-defined intermediate representation (finding IR with category, severity, component, mitigation) makes the SARIF mapping mechanical. Correlated findings map to `relatedLocations`, component context maps to `logicalLocations`, and deterministic fingerprints enable stable tracking across runs.

**Why This Matters**:
Captured during structured delivery retrospective. Smooth sailing — everything went roughly as planned. When the IR is well-structured, adding new output formats is straightforward prompt engineering with no schema friction.

**When to Apply**: Any future output format additions (e.g., CycloneDX, CSAF). Design the IR first, then map to the target schema. The IR-to-format mapping should be mechanical, not creative.

**Tags**: #retrospective #delivery #architecture #pattern

**Quality Score**: 7/10

---

### PAT-005: Spec-First Architecture Enables Clean External API Degradation

**Date**: 2026-03-23
**Feature**: 018 — Threat Infographic Agent
**Category**: Architecture / Graceful Degradation

**Context**: Feature 018 introduced tachi's first external API dependency (Gemini image generation). The team needed to integrate an optional external service without compromising the local-first, zero-dependency pipeline.

**Pattern**: Design the specification as the primary deliverable and the external API output (image) as best-effort. The spec is always produced locally; the image is only attempted when the API key is present. Six failure conditions (missing key, rate limit, timeout, content policy, missing input, empty model) all resolve the same way: save the spec, log the reason, continue the pipeline.

**Result**: The infographic agent produces a useful deliverable (spec) in all conditions. The Gemini API is purely additive — its absence is invisible to the pipeline. Triple opt-out (flag, env var, config) gives users control at every level.

**When to Apply**: Any feature that integrates an external API where the API output is valuable but not essential. Design the local artifact first, make the API call optional, and ensure all failure modes produce the local artifact. This pattern preserves tachi's local-first principle.

**Tags**: #retrospective #delivery #architecture #pattern #graceful-degradation

**Quality Score**: 8/10

---

### PAT-006: Post-Pipeline Enrichment via Schema-Driven Scoring

**Date**: 2026-03-27
**Feature**: 035 — Quantitative Risk Scoring
**Category**: Architecture / Pipeline Extension

**Context**: Feature 035 needed to add quantitative risk scoring to the existing `/threat-model` pipeline without modifying the threat agents or their output. The scoring agent consumes threat model output (threats.md or threats.sarif) and produces enriched output (risk-scores.md and risk-scores.sarif).

**Pattern**: Design post-pipeline enrichment as a separate command and agent that reads existing output and produces new artifacts. Use a dedicated schema (risk-scoring.yaml) to define scoring dimensions, weights, and severity bands — making the scoring methodology configurable and transparent. The enrichment agent references the existing finding schema via an optional extension block, preserving backward compatibility. SARIF fingerprints and taxonomies are preserved from source to enriched output for tracking continuity.

**Result**: 29 tasks completed same-day across 9 phases. The scoring agent operates independently of threat agents — no modifications to existing commands, agents, or schemas were needed (only an optional extension reference added to finding.yaml). Dual-format output reused the established SARIF generation pattern (PAT-004), and the weighted composite formula with severity band mapping made scoring reproducible (+/- 0.5 tolerance).

**When to Apply**: Any feature that enriches existing pipeline output with new dimensions (e.g., compensating controls, compliance mapping, cost estimation). Design as a separate command consuming existing output, use a dedicated schema for the enrichment logic, and preserve source identifiers for traceability. Avoid modifying upstream agents or schemas — extend via optional references.

**Tags**: #retrospective #delivery #architecture #pattern #pipeline-extension

**Quality Score**: 8/10

---

### PAT-007: Chained Pipeline Enrichment Validates Schema-Driven Extension Pattern

**Date**: 2026-03-28
**Feature**: 036 — Compensating Controls Analysis
**Category**: Architecture / Pipeline Extension

**Context**: Feature 036 added the third stage to tachi's threat analysis pipeline (`/threat-model` → `/risk-score` → `/compensating-controls`). It consumes risk-scores output and produces compensating-controls output with control detection, effectiveness classification, recommendations, and residual risk — extending the same schema-driven enrichment approach from PAT-006.

**Pattern**: Successive pipeline stages can chain reliably when each stage follows the same contract: consume prior stage's dual output (MD + SARIF), extend via a dedicated schema (compensating-controls.yaml extending risk-scoring.yaml), and produce new dual output. The 6-phase agent design (parse → discover → detect → classify → recommend → output) decomposed cleanly because each phase has well-defined inputs and outputs. The 8 STRIDE + 2 AI control detection categories mapped directly to the existing threat taxonomy, requiring no upstream schema modifications.

**Result**: 21 tasks completed in ~1 day across 6 waves. Smooth execution — the pipeline extension pattern from Feature 035 transferred directly. Schema extension (rather than modification) preserved backward compatibility. The coverage matrix and residual risk calculation were straightforward because the finding IR already carried all necessary fields from upstream stages.

**When to Apply**: When adding successive enrichment stages to an existing pipeline. If the previous stage validated the schema-driven extension pattern, subsequent stages can follow the same template with high confidence. The key enabler is a well-structured finding IR that carries forward all fields needed by downstream stages.

**Tags**: #retrospective #delivery #architecture #pattern #pipeline-extension

**Quality Score**: 8/10

---

## Bug Fixes

*No entries yet. Use `/kb-create` to add the first bug fix.*
