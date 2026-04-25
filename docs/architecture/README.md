# Architecture Documentation - tachi

**Last Updated**: 2026-04-25
**Owner**: Architect
**Status**: Template

---

## Overview

This directory contains all technical architecture documentation for tachi.

**Canonical MAESTRO Worked Example**: For the end-to-end demonstration of the MAESTRO umbrella (Phases 1-5 delivered across Features 084 / 136 / 141 / 142 / 143 / 144), see [`examples/maestro-reference/`](../../examples/maestro-reference/) — the Healthcare CDSS multi-agent reference scenario spanning all seven MAESTRO layers, with cross-layer attack chains, agentic pattern classifications, and compliance posture cross-references (ADR-024 AIVSS, ADR-025 NIST AI RMF) surfaced end-to-end by a single pipeline run. Delivered in Feature 145 (content-authoring only — zero code, schema, or agent changes).

---

## Structure

### 00_Tech_Stack/
Technology choices and justifications
- `README.md` - Tech stack overview with {{TEMPLATE_VARIABLES}}

### 01_system_design/
High-level system design and component diagrams
- `README.md` - System architecture overview (auto-scaffolded from plan.md by `/aod.project-plan` after Architect sign-off)
- `upstream-sync-architecture.md` - Repo topology and sync flow between private, public template, and adopter projects
- Component interaction diagrams
- Data flow diagrams

### 02_ADRs/ (Architecture Decision Records)
Significant technical decisions with context and trade-offs
- `ADR-000-template.md` - ADR template and example
- `ADR-001-atomic-state-persistence.md` - Write-then-rename for orchestrator state (Feature 022)
- `ADR-002-prompt-segmentation.md` - On-demand reference file loading for context efficiency (Feature 030)
- `ADR-005-serialization-trade-off.md` - Serialization trade-off for parallel Triad reviews (Feature 047)
- `ADR-006-non-fatal-observability-operations.md` - Non-fatal error handling for observability and circuit-breaker functions (Feature 054)
- `ADR-007-stack-pack-dual-surface-injection.md` - Dual-surface injection pattern for stack pack context loading (Feature 058)
- `ADR-008-opt-out-flag-for-default-quality-gates.md` - Opt-out flag (`--no-simplify`) for default-on quality gate steps in commands (Feature 065)
- `ADR-009-template-variable-expansion-scope.md` - Expanding `tachi` placeholder to all user-facing template files (Feature 061)
- `ADR-010-minimal-return-architecture.md` - Minimal return architecture for subagent→main token efficiency (Feature 073)
- `ADR-011-multi-flag-opt-out-and-step-insertion-pattern.md` - Multi-flag opt-out pattern and step insertion convention for `/aod.build` (Feature 080)
- `ADR-012-cross-agent-correlation-detection.md` - Deterministic cross-agent correlation detection algorithm (Feature 010)
- `ADR-013-sarif-output-format-adoption.md` - SARIF 2.1.0 output format for GitHub Code Scanning integration (Feature 012)
- `ADR-014-gemini-api-optional-image-generation.md` - Optional Gemini API image generation for infographic agent (Feature 018)
- `ADR-015-platform-adapter-hub-and-spoke-distribution.md` - Hub-and-spoke distribution pattern for platform adapters (Feature 021)
- `ADR-019-shared-definitions-and-model-field-governance.md` - Shared cross-agent definitions and model field governance (Feature 078)
- `ADR-020-maestro-layer-classification.md` - CSA MAESTRO seven-layer taxonomy for agentic AI component classification (Feature 084; Revision History adds canonical layer rename rule for enum-value-only minor schema bumps in Feature 136)
- `ADR-021-source-date-epoch-for-deterministic-pdf-comparison.md` - SOURCE_DATE_EPOCH reproducible-builds convention for byte-deterministic PDF baseline comparison (Feature 128)
- `ADR-022-mmdc-hard-prerequisite.md` - `mmdc` (Mermaid CLI) as hard prerequisite gated on attack-tree detection; establishes fail-loud-on-missing-CLI posture with defense-in-depth preflight gates — first ADR governing CLI-prerequisite posture (Feature 130)
- `ADR-023-threat-agent-skill-references-pattern.md` - Detection variant of the lean + skill references pattern as a sibling to the methodology variant; single-point `**MANDATORY**: Read` at detection start for all 11 threat agents (6 STRIDE + 5 AI); MAESTRO classification remains orchestrator-owned; shared reference edits are additive-only; `finding-format-shared.md` gains "For Threat Agents" producer section. Completes the lean-agent architecture migration for all 17 tachi agents (Feature 082)
- `ADR-024-owasp-aivss-evaluation.md` - **Accepted (2026-04-15)**: tachi diverges from OWASP AIVSS v0.8 at the present time; existing four-dimensional weighted-sum composite (`0.35 × CVSS 3.1 + 0.30 × Exploitability + 0.20 × Reachability + 0.15 × Scalability`) remains the canonical scoring model. Records the three-surface evaluation (dimension set, composite formula, severity bands) against AIVSS v0.8, establishes structural divergence on Surfaces A (dimensions) and B (formula) with alignment only on Surface C (severity bands), and defines re-evaluation triggers (AIVSS v1.0 + at least one external adopter case study). Cross-references ADR-020 (MAESTRO classification), ADR-019 (shared definitions), ADR-018 (baseline-aware scoring lineage), and ADR-025 (companion NIST AI RMF evaluation). Documentation-only spike — zero schema, script, or pipeline changes (Feature 143)
- `ADR-025-nist-ai-rmf-evaluation.md` - **Accepted (2026-04-16)**: tachi adopts a **documentation-only NIST AI RMF 1.0 mapping posture** (Option A) — no schema, pipeline, or runtime changes; the mapping lives in this ADR and a companion skill reference (`.claude/skills/tachi-shared/references/nist-ai-rmf-mapping.md`). Records the three-surface evaluation of AI RMF 1.0 and the NIST AI 600-1 Generative AI Profile: Surface A (AI RMF Functions Govern/Map/Measure/Manage × tachi pipeline phases — 3 Overlap, 1 structural Gap on Govern), Surface B (16 representative AI RMF Subcategories × 8 tachi compensating-control categories — 8 Overlap, 2 Gap, 2 No equivalent; MEASURE 2.7 is the strongest direct mapping), Surface C (12 NIST AI 600-1 GAI risks × tachi STRIDE+AI categories — 4 Overlap, 1 Gap, 7 No equivalent; §2.9 Information Security is the strongest direct mapping). Captures the structural tier-mismatch insight: AI RMF Functions are *organizational-tier outcomes* while tachi produces *artifact-tier evidence* — this tier gap is why Option A (docs-only) was chosen over deeper wired integration. Distinct from ADR-024's pre-1.0 AIVSS divergence: AI RMF 1.0 is mature (3+ year runway through 2028, federal procurement adoption, FFIEC/HIPAA references), so maturity is a permission rather than a blocker. Cross-references ADR-024 (companion AIVSS), ADR-020 (MAESTRO classification), ADR-019 (shared definitions), ADR-018 (baseline lineage), ADR-021 (determinism), ADR-023 (skill-references pattern). Closes the MAESTRO compliance umbrella together with Features 084/141/136/082/143. Documentation-only spike — zero code, schema, agent, or example changes (Feature 144)
- `ADR-026-pattern-classification-mechanism.md` - **Accepted (2026-04-16)**: records the **Hybrid Post-Hoc Synthesis (Option C)** decision for MAESTRO Phase 3 agentic pattern classification. Adds orchestrator **Phase 3.6 (Pattern Synthesis Engine)** running after Feature 141's Phase 3.5 cross-layer chain correlation and before Phase 4 assessment; introduces `agentic_pattern` enum field on `schemas/finding.yaml` (8 values: 6 canonical CSA MAESTRO patterns + `none` + `multiple`, default `none`) with schema version bumped **1.3 → 1.4**; extends the Feature 136 enum-VALUE-rename minor-bump rule to cover **NEW enum-typed field additions** where the new field is additive, has a default, and the schema shape is unchanged. Establishes a durable **governance rule for future post-hoc synthesis phases**: finding-level metadata synthesis MAY write back to the finding IR (Phase 3.6 model); cross-finding aggregates remain aggregate-only / separate-artifact (Phase 3.5 model). Preserves the zero-edit invariant on the 11 detection agents (6 STRIDE + 5 AI) stabilized in Feature 082 per ADR-023 governance. Cross-references ADR-019 (shared definitions), ADR-020 (MAESTRO classification — extended here with Phase 3), ADR-021 (determinism), ADR-023 (skill-references pattern) (Feature 142)
- `ADR-027-taxonomy-crosswalk-schema.md` - **Accepted (2026-04-17)**: records 8 numbered decisions for the F-A1 machine-readable taxonomy catalog + crosswalk foundation (`schemas/taxonomy/` directory, 9 files, 526 primary edges). Per-item record shape (5-field `{id, full_id, name, url, cwe_refs}` with unidirectional OWASP→CWE rule), per-edge record shape, 7-value `taxonomy` enum (closed for F-A1), 3-value `edge_type` enum (only `primary` populated in F-A1; `related` / `superseded` deferred to F-A1.2), 3-value `confidence` enum with anti-drift rule (downgrade to weaker label if curator cannot articulate a one-sentence citation supporting `high`/`medium`), citation non-empty-and-resolvable rule, **Interpretation C single-feature cadence exception** (bounded to foundation-data features; does NOT set precedent for downstream gap-closure features like F-A2), and the **Proposed → Accepted dual-commit governance protocol** mirroring ADR-024/025 precedent. Cross-references ADR-020/021/023/024/025/026 (Feature 180)
- `ADR-028-source-attribution-schema-extension.md` - **Accepted (2026-04-17)**: records 7 numbered decisions for the F-A2 finding-level `source_attribution` contract. Minor-bump 1.4 → 1.5 under the **Complex-Shape Addition Clarifier** extending the ADR-026 minor-bump rule from NEW enum-typed SCALAR field additions to NEW list-of-RECORD field additions — first such extension in tachi schema evolution. Q1 serialization surface (conditional `## 9. Source Attribution` YAML block in `threats.md` per Feature 141 `has-attack-chains` precedent), closed 5-value `taxonomy` enum (external frameworks only — deliberately distinct-scope subset of ADR-027's 7-value catalog enum), closed 3-value `relationship` enum (`primary` / `related` / `derived`, default `primary`), Q2 referential-integrity contract (two-tier validation with separate post-parse helper `validate_source_attribution` invoked from orchestrator Phase 4), 22-file zero-edit invariant preserved (11 STRIDE+AI agent files + 11 companion `detection-patterns.md` skill-references), Proposed → Accepted dual-commit governance protocol. Cross-references ADR-020/021/023/026/027 (Feature 189)
- `ADR-029-coverage-attestation-report-section.md` - **Accepted (2026-04-18)**: records 8 numbered decisions for the F-B coverage-attestation PDF section. Decision 2 (three-state classification rubric: Covered / Partial / Gap) with Decision 3 (catalog-length denominator) computed from `source_attribution` arrays over the 5 external-framework catalogs. One new Typst page template (`templates/tachi/security-report/coverage-attestation.typ`) conditionally included via `has-source-attribution` boolean flag (Feature 141 / 112 precedent). Aggregator surface in `scripts/extract-report-data.py` with lazy `import yaml` inside `_load_framework_yaml_records` to preserve stdlib-only module-load invariant (post-merge refinement). 22-file zero-edit invariant preserved. Cross-references ADR-020/021/023/027/028 (Feature 194)
- `ADR-030-output-integrity-agent.md` - **Accepted (2026-04-18)**: records 8 numbered decisions for the F-1 `output-integrity` threat agent — **first net-new threat detection agent under BLP-01 Tier 1** and **first downstream consumer of F-A1 + F-A2 + F-B Foundation tier**. (D1) adopt new AI-tier agent for OWASP LLM05:2025 output-handling coverage closure; (D2) Heuristic A Outcome B split scope — F-1 owns encoding/sanitization primitives, F-4 `trust-exploitation` forward-referenced for human-victim signal class per BLP-01 §8; (D3) lean-agent detection variant per ADR-023; (D4) LLM05 + ML09:2023 documentation-only bundling (ML09 cited in prose but NOT in `source_attribution`); (D5) 22-file zero-edit invariant preserved with grep-auditable enumeration; (D6) Proposed → Accepted dual-commit with post-merge SHA fill; (D7) `category: llm` enum reuse (no new category value required since `OI-{N}` ID prefix plus `source_attribution.id: LLM05` carry sufficient discrimination); (D8) **ADR-026 Complex-Shape Clarifier extended to regex-alternation prefix additions** — first such extension; `schemas/finding.yaml` bumped 1.5 → 1.6 with `id.pattern` regex extended from `^(S|T|R|I|D|E|AG|LLM|AGP)-\d+$` to `^(S|T|R|I|D|E|AG|LLM|AGP|OI)-\d+$`. Cross-references ADR-020/021/022/023/026/027/028/029 (Feature 201)
- `ADR-031-misinformation-agent.md` - **Accepted (2026-04-24)**: records 9 numbered decisions for the F-2 `misinformation` threat agent — **second net-new threat detection agent under the ADR-030 lineage** (closing OWASP LLM09:2025 Misinformation on the Coverage Matrix: Planned → Covered) and **second net-new producer of `source_attribution`** after F-1. (D1) adopt new AI-tier agent for LLM09:2025 factual-integrity coverage closure as the 7th AI-tier agent; (D2) **Heuristic A three-way scope inheritance from ADR-030 Decision 1** — `prompt-injection` (input-side attacker injection) / `output-integrity` (downstream-execution-sanitization) / `misinformation` (factual-integrity, human-victim + decision-cascade-victim) partition auditable at vocabulary / CWE mapping / mitigation-taxonomy levels; three-way partition is inheritance from ADR-030, not re-adjudication; (D3) lean-agent shape conformance per ADR-023 detection variant (≤150 lines, single `**MANDATORY**: Read`, zero MAESTRO); (D4) 5-category pattern scope (Ungrounded Factual Emission / Citation Fabrication / Overreliance-Missing-HITL / Retrieval-Grounding Gap / Confidence-Calibration Absence) with Q1 deferrals (Model-Specific Hallucination Patterns, Feedback-Loop Overreliance); (D5) **24-file zero-edit invariant preserved** with grep-auditable enumeration (22 original + F-1's 2) — F-2 adds 1 agent + 1 companion skill as new additions, bringing the invariant to 26 post-merge; (D6) Proposed → Accepted dual-commit with post-merge SHA fill (fifth consecutive ADR under this protocol in the ADR-027/028/029/030/031 series); (D7) post-merge SHA fill recording squash commit; (D8) **schema bump 1.6 → 1.7 as 2nd recorded application of ADR-030 Decision 8 regex-alternation minor-bump rule** — `MI` prefix appended to `id.pattern` regex (`^(S|T|R|I|D|E|AG|LLM|AGP|OI|MI)-\d+$` — 11 alternation values); pure precedent application, no further extension; (D9) **CWE-1039 deliberate exclusion** per architect MEDIUM-3 scope-discipline ruling — model-robustness primitive distinct from factual-content primitive; adversarial-robustness coverage would belong in a future sibling agent with `AR-{N}` prefix. Cross-references ADR-021/023/026/027/028/029/030 (Feature 206)
- `ADR-NNN-decision-title.md` - Individual ADRs

### 03_patterns/
Reusable design patterns and best practices
- `README.md` - Pattern catalog
- Pattern documentation by category

### 04_deployment_environments/
Environment-specific configurations and documentation
- `README.md` - Environment overview
- `development.md` - Local development setup
- `staging.md` - Staging environment
- `production.md` - Production environment

---

## Key Principles

1. **Document Decisions**: Use ADRs for significant technical choices
2. **Keep Current**: Update docs alongside code changes
3. **Auto-Scaffold**: `01_system_design/README.md` is auto-generated from plan.md during `/aod.project-plan` approval (Feature 089)
4. **Template Variables**: Use `{{TEMPLATE_VARIABLES}}` for project-specific values
5. **Version Control**: All architecture docs in git

---

## Quick Links

- [Tech Stack](00_Tech_Stack/README.md)
- [System Design](01_system_design/README.md)
- [ADRs](02_ADRs/ADR-000-template.md)
- [Patterns](03_patterns/README.md)
- [Environments](04_deployment_environments/README.md)

---

**Maintained By**: Architect
