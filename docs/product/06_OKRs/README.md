# OKRs (Objectives and Key Results) - tachi

**Last Updated**: 2026-04-17
**Owner**: Product Manager (product-manager)
**Status**: Template - Complete after MVP launch

---

## When to Create This

**Create OKRs AFTER your MVP launches and you have baseline metrics.**

Before MVP:
- You don't have baseline numbers to improve
- Your key metrics may not be measurable yet
- Focus should be on shipping, not measuring

**Pre-MVP Goal**: Launch MVP
**Post-MVP**: Set quarterly OKRs based on real usage data

**First OKRs**: Typically set 4-6 weeks after MVP launch when you have initial metrics.

> **AOD Lifecycle Note**: OKR documents can be scaffolded via `/aod.okrs`, which
> generates a standard OKR template (Objective, Key Results, Initiatives) in this
> directory with PM sign-off. `/aod.define` reads current OKRs for PRD alignment,
> and `DOCS_TO_UPDATE_AFTER_NEW_FEATURE.md` includes OKR progress as a checklist
> item during `/aod.deliver`. You can still create and maintain OKR files manually.

---

## Overview

OKRs align the team around measurable goals. They answer:
- **Objective**: What do we want to achieve? (qualitative)
- **Key Results**: How do we know we achieved it? (quantitative)

---

## OKR Template

```markdown
# tachi OKRs - YYYY-QN

**Quarter**: [Q1/Q2/Q3/Q4] YYYY
**Status**: [Planning | In Progress | Complete]
**Review Date**: [YYYY-MM-DD]

## Objective 1: [Qualitative Goal]

**Why This Matters**: [Alignment with product vision]

### Key Result 1.1: [Measurable Outcome]
- **Baseline**: [Starting value]
- **Target**: [End of quarter goal]
- **Current**: [Current progress]
- **Status**: 🟢 On Track | 🟡 At Risk | 🔴 Off Track
- **Owner**: [Team member]

### Key Result 1.2: [Another Measurable Outcome]
[Same structure]

## Objective 2: [Another Qualitative Goal]
[Same structure]

---

## Progress Tracking

**Week of [Date]**:
- KR 1.1: [Update]
- KR 1.2: [Update]

**Risks**:
- [Risk 1]

**Actions**:
- [ ] [Action item to address risk]
```

---

## OKR Best Practices

### Objectives (Qualitative)
- ✅ Inspiring and motivational
- ✅ Aligned with product vision
- ✅ Achievable but ambitious
- ❌ Not a task list

**Example Good Objectives**:
- "Become the go-to platform for [user segment]"
- "Delight users with exceptional [experience]"
- "Establish market leadership in [category]"

### Key Results (Quantitative)
- ✅ Specific and measurable
- ✅ Time-bound (end of quarter)
- ✅ 70-80% achievable (stretch goal)
- ❌ Not activities (use metrics)

**Example Good Key Results**:
- "Increase active users from 1,000 to 5,000"
- "Achieve NPS score of 50+"
- "Reduce churn from 10% to 5%"

---

## Integration with Product Workflow

### OKRs Drive PRDs
- Each PRD should support at least one key result
- PRD success metrics align with OKR key results
- PRD prioritization based on OKR impact

### OKRs Inform Roadmap
- Roadmap phases deliver on quarterly OKRs
- Feature prioritization based on OKR contribution
- Roadmap adjusts if OKRs change

---

## Review Cadence

### Weekly Check-In (15 min)
- Update key result progress
- Identify blockers
- Adjust tactics if needed

### End of Quarter Review (2 hours)
- Score all key results (0.0 - 1.0 scale)
- Document learnings
- Plan next quarter OKRs

---

**Template Instructions**: Create a new OKR file each quarter (YYYY-QN.md). Delete this message after creating your first OKR document.

---

## Feature Delivery Log

> **Purpose**: Track feature deliveries for OKR alignment once quarterly OKRs are established.
> Features listed here should be mapped to Key Results when OKRs are created.

| Date | Feature | PRD | Impact |
|------|---------|-----|--------|
| 2026-03-21 | F-001: Project Skeleton & Interface Contract | [001](../02_PRD/001-project-skeleton-interface-contract-2026-03-21.md) | Foundation layer complete: 11 threat agent prompts, interface contract, output template, 3 schemas, 3 examples. Unblocks F-002 through F-010. |
| 2026-03-21 | F-003: Orchestrator Agent | [003](../02_PRD/003-orchestrator-agent-2026-03-21.md) | Central orchestrator agent prompt: parses 5 architecture formats, dispatches to STRIDE and AI threat agents via STRIDE-per-Element and keyword matching, assembles structured threats.md output. Unblocks F-003 (STRIDE Agents), F-004 (AI Agents), F-005 (Dedup & Risk Rating), F-009 (Platform Adapters). |
| 2026-03-22 | F-005: STRIDE Threat Agents | [005](../02_PRD/005-stride-threat-agents-2026-03-21.md) | 6 validated STRIDE threat agents (spoofing, tampering, repudiation, info-disclosure, denial-of-service, privilege-escalation) with AI-specific threat patterns, STRIDE-per-Element matrix targeting, and OWASP/CWE/MITRE ATT&CK references. Updated 3 example threat models. Unblocks F-004 (AI Agents). |
| 2026-03-22 | F-010: Deduplication & Risk Rating | [010](../02_PRD/010-deduplication-risk-rating-2026-03-22.md) | Cross-agent finding correlation with 5 deterministic rules (STRIDE-to-AI pairs), deduplicated coverage matrix with three-state cell model, OWASP 3x3 risk calibration matrix in output, schema v1.1. Completes Phase 1 Foundation pipeline. Unblocks F-009 (Platform Adapters). |
| 2026-03-22 | F-012: SARIF Output Generation | [012](../02_PRD/012-sarif-output-generation-2026-03-22.md) | SARIF 2.1.0 output co-generated with threats.md during Phase 4 (Assess). Finding IR mapped to SARIF results with 8 rule definitions (6 STRIDE + 2 AI), CVSS-aligned severity levels, correlated finding representation via relatedLocations, component navigation via logicalLocations, and stable partialFingerprints for cross-run tracking. Enables GitHub Code Scanning, VS Code SARIF Viewer, and Azure DevOps integration. Unblocks F-009 (Platform Adapters — SARIF upload automation). |
| 2026-03-23 | F-015: Threat Report Agent & Attack Trees | [015](../02_PRD/015-threat-report-agent-attack-trees-2026-03-23.md) | Narrative threat report with executive summary, Mermaid attack trees for Critical/High findings, remediation roadmap. Phase 5 (Report) in orchestrator pipeline. |
| 2026-03-23 | F-018: Threat Infographic Agent | [018](../02_PRD/018-threat-infographic-agent-2026-03-23.md) | Visual infographic agent producing threat-infographic-spec.md with risk distribution, coverage heat map, top findings summary, and CVSS color palette. Optional Gemini API image generation. Phase 6 (Infographic) in orchestrator pipeline with opt-out support. Completes the reporting layer: structured analysis (F-005/F-007) -> narrative report (F-015) -> visual communication (F-018). |
| 2026-03-23 | F-024: Example Threat Models | [024](../02_PRD/024-example-threat-models-2026-03-23.md) | 3 end-to-end example threat models (web-app, agentic-app, microservices) with architecture diagrams and complete threat model outputs matching schema v1.1. Demonstrates STRIDE, AI threat agents, and OWASP cross-references. 5 user stories delivered. |
| 2026-03-27 | F-035: Quantitative Risk Scoring | [035](../02_PRD/035-quantitative-risk-scoring-2026-03-27.md) | Four-dimensional quantitative risk scoring (CVSS base + exploitability + scalability + reachability) with composite scores, governance fields, dual output formats (risk-scores.md + risk-scores.sarif). |
| 2026-03-28 | F-036: Compensating Controls Analysis | [036](../02_PRD/036-compensating-controls-2026-03-27.md) | Codebase control detection with file:line evidence, compensating control recommendations, coverage matrix, residual risk calculation, and control effectiveness assessment. |
| 2026-03-28 | F-039: Standalone /tachi.infographic Command | [039](../02_PRD/039-standalone-infographic-command-2026-03-28.md) | Standalone `/tachi.infographic` command with auto-detection of richest data source (risk-scores.md preferred), explicit file path override, template selection. Phase 6 removed from `/tachi.threat-model` pipeline. All platform adapters updated to 5-phase pipeline. |
| 2026-03-28 | F-045: End-to-End tachi Instruction Manual | [045](../02_PRD/045-instruction-manual-2026-03-28.md) | Comprehensive developer guide covering the full 4-command pipeline (`/tachi.threat-model` -> `/tachi.risk-score` -> `/tachi.compensating-controls` -> `/tachi.infographic`). Includes Quick Start (first threat model in 5 minutes), pipeline walkthrough with OpenClaw worked example, output interpretation for all 12+ artifacts, and prompt spec for consistent regeneration. First documentation-focused deliverable after core pipeline completion. |
| 2026-03-28 | F-048: Infographic Tiered Detection & Residual Risk | [048](../02_PRD/048-infographic-tiered-detection-residual-risk-2026-03-28.md) | Three-tier data source auto-detection for `/tachi.infographic` (compensating-controls.md > risk-scores.md > threats.md), residual risk extraction from compensating controls Coverage Matrix, progressive enhancement tips at each pipeline tier, and risk label distinction (Residual Risk / Inherent Risk / Severity) across both infographic templates. Completes the visualization layer of the composable pipeline. |
| 2026-03-28 | F-053: Risk Reduction Funnel | [053](../02_PRD/053-risk-reduction-funnel-2026-03-28.md) | New `risk-funnel` infographic template visualizing progressive risk reduction through the tachi pipeline as a 4-tier vertical funnel (threats identified -> inherent risk scored -> controls applied -> residual risk). Supports graceful degradation: 4-tier from compensating-controls.md, 3-tier from risk-scores.md, 1-tier from threats.md. Metrics sidebar with risk reduction percentage and control coverage. |
| 2026-03-30 | F-067: Deterministic Report Data Extraction | [067](../02_PRD/067-deterministic-report-data-extraction-2026-03-30.md) | Replaced LLM-based markdown parsing with deterministic Python script (scripts/extract-report-data.py) for `/tachi.security-report` data extraction. 3-tier severity source selection, internal consistency validation, scope data extraction. Report-assembler agent updated to invoke script instead of inline parsing. Fixes non-deterministic output where identical inputs produced varying severity counts (0-20 Critical) and risk levels (HIGH/CRITICAL). |
| 2026-03-30 | F-071: Deterministic Infographic Extraction | [071](../02_PRD/071-deterministic-infographic-extraction-2026-03-30.md) | Replaced LLM-based data extraction in threat-infographic agent with deterministic Python script (scripts/extract-infographic-data.py). Shared tachi_parsers.py module extracted from extract-report-data.py. Produces byte-identical JSON output for baseball card, system architecture, and risk funnel templates. Ensures cross-output consistency between infographics and security reports. Completes deterministic extraction across all tachi pipelines. |
| 2026-03-31 | F-075: Tachi Agent Best Practices | [075](../02_PRD/075-tachi-agent-best-practices-2026-03-31.md) | Extracted domain knowledge from 3 methodology agents (orchestrator, risk-scorer, control-analyzer) into on-demand skill files (tachi-orchestration, tachi-risk-scoring, tachi-control-analysis). Audited all 17 tachi agents for Claude 4.6 prompting best practices. Created shared _TACHI_AGENT_BEST_PRACTICES.md with tier caps and compliance table. Trimmed threat-report to 800-line cap. Unblocks #74 (baseline-aware pipeline). |
| 2026-04-01 | F-074: Baseline-Aware Pipeline | [074](../02_PRD/074-baseline-aware-pipeline-2026-03-31.md) | Baseline-aware threat detection pipeline with 4-phase orchestration (carry-forward, isolated discovery, merge/dedup, coverage gate). Correlates findings across runs with stable IDs via SARIF fingerprints. Delta annotations (`[NEW]`, `[UNCHANGED]`, `[UPDATED]`, `[RESOLVED]`) on all outputs. Coverage checklists per STRIDE category. Extended orchestrator, risk-scorer, and control-analyzer agents. New coverage-checklists schema. Updated all output templates and SARIF properties. Unblocks #55 (Security Progression Summary). |
| 2026-04-02 | F-078: Agent Context Optimization | [078](../02_PRD/078-agent-context-optimization-2026-04-01.md) | Restructured 6 tachi agents (orchestrator, risk-scorer, control-analyzer, report-assembler, threat-report, threat-infographic) from monolithic prompts to lean definitions with on-demand skill references. Created 4 skill directories (tachi-orchestration, tachi-risk-scoring, tachi-report-assembly, tachi-shared) with 25+ granular reference files. Added explicit model fields to all 17 agent definitions. Reduced agent prompt sizes by 40-60%. Zero regression on threat model outputs. |
| 2026-04-06 | F-086: Automated Release Tagging via GitHub Actions | [086](../02_PRD/086-automated-release-tagging-via-github-actions-2026-04-06.md) | Google release-please GitHub Action for automated version tagging from conventional commits. Deliverables: release-please.yml workflow, release-please-config.json, .release-please-manifest.json (baseline v4.0.0), README Releases section. Eliminates manual `git tag` commands; maintainer controls release timing via Release PR merge. |
| 2026-04-08 | F-084: MAESTRO Layer Mapping | [084](../02_PRD/084-maestro-layer-mapping-2026-04-07.md) | CSA MAESTRO seven-layer taxonomy overlay for all threat findings. New schema field, shared reference, orchestrator keyword classification in Phase 1, SARIF tags, downstream agent propagation, layer-based risk summary. All 6 example outputs regenerated. 4 user stories delivered. |
| 2026-04-08 | F-104: Downstream Baseline Propagation | [104](../02_PRD/104-downstream-baseline-propagation-2026-04-08.md) | Propagates baseline delta_status downstream through threat-report, infographic, and PDF report pipelines. RESOLVED findings excluded from active counts, NEW findings highlighted, Delta Summary section in reports. Shared parser updated for delta extraction. Completes the baseline story started in F-074. 4 user stories delivered. |
| 2026-04-09 | F-121: Rename Tachi Commands to tachi.* Namespace | [121](../02_PRD/121-rename-tachi-commands-to-namespace-2026-04-09.md) | All 6 tachi pipeline commands renamed to tachi.* dot-namespace (e.g., /threat-model -> /tachi.threat-model). New /tachi.architecture command added. Cross-references updated across 160+ files. Install script handles cleanup of old command files. 5 user stories delivered. |
| 2026-04-09 | F-120: Architecture Lifecycle Command | [120](../02_PRD/120-architecture-lifecycle-command-2026-04-09.md) | Version tracking with YAML frontmatter (version, date, checksum) for architecture files. Archive mechanism preserving previous versions. Threat model snapshot integration copying architecture into output folder. Guided update mode for walking through architecture change categories. 4 user stories delivered. |
| 2026-04-09 | F-112: Attack Path Pages in Security Report PDF | [112](../02_PRD/112-attack-path-pages-in-pdf-2026-04-09.md) | Dedicated attack path visualization pages in PDF security reports. Extraction pipeline parses attack trees, renders Mermaid diagrams to PNG, feeds structured data to Typst template. Conditional inclusion gated by has-attack-trees boolean. Backward compatible with existing reports. 4 user stories delivered. |
| 2026-04-10 | F-128: Executive Threat Architecture Infographic | [128](../02_PRD/128-executive-threat-architecture-2026-04-09.md) | Sixth infographic template (executive-architecture) producing CISO-ready threat architecture visualization with early-page PDF placement immediately after Executive Summary. `all` shorthand and new `exec` alias include the template; graceful handling of threat models with no qualifying findings. 51/51 tasks across 8 phases; 4 user stories delivered. T038 PM usability check deferred with 5-business-day post-merge SLA. |
| 2026-04-10 | F-136: MAESTRO Canonical Layer Correctness Fix | [136](../02_PRD/136-maestro-canonical-layer-correctness-fix-2026-04-10.md) | Correctness fix aligning tachi's MAESTRO seven-layer taxonomy with the canonical CSA Ken Huang reference. Three layers renamed: L5 Security → L5 Evaluation and Observability, L6 Agent Ecosystem → L6 Security and Compliance, L7 User Interface → L7 Agent Ecosystem. Acronym expansion corrected ("Multi-Agent Environment, Security, Threat, Risk, and Outcome"). Schema version bumped 1.2 → 1.3 with documented old → new enum migration path in CHANGELOG. Keyword sets rebalanced: new L5 observability keywords (audit log, monitoring, anomaly detection, SIEM, forensics, telemetry), L6 retains security keywords (auth, WAF, guardrail, RBAC), L7 merges agent-ecosystem and user-facing keywords. Typst template three-way divergence ("Integration Services" L6 bug) corrected. Wave 0 pre-edit grep discovery report committed for audit trail. All six example outputs regenerated; five non-agentic-app baselines byte-deterministic under SOURCE_DATE_EPOCH=1700000000 with test_backward_compatibility.py passing. Observability components (audit loggers, SIEM, anomaly detection) now have a canonical L5 Evaluation and Observability home instead of being misrouted to Security or lost in Unclassified. ADR-020 amended with revision note. 45/45 tasks complete; 8 user stories delivered. Release-please will cut v4.10.0 minor release (feat(136) prefix). |
| 2026-04-11 | F-082: Threat Agent Skill References | [082](../02_PRD/082-threat-agent-skill-references-2026-04-11.md) | Completes the lean-agent architecture for all 17 tachi agents by migrating the remaining 11 threat detection agents (6 STRIDE + 5 AI) from self-contained inline shape to lean + skill references pattern. STRIDE agents reduced from 113-141 lines to 50-54 lines; AI agents reduced from 167-201 lines to 78-114 lines — every agent within FR-10 tier caps (STRIDE ≤120, AI ≤150, hard cap ≤180). 11 new companion skill directories created at `.claude/skills/tachi-<name>/references/` (spoofing, tampering, repudiation, info-disclosure, denial-of-service, privilege-escalation, prompt-injection, data-poisoning, model-theft, tool-abuse, agent-autonomy), each hosting a `detection-patterns.md` reference file loaded via a single `**MANDATORY**: Read` directive at detection start (new "detection variant" of the lean pattern, sibling to the methodology variant used by control-analyzer). Enrichment floor cleared: +30 new pattern categories added across the 11 agents against a ≥22 aggregate floor — +8 margin. Source attribution: OWASP Top 10 2021, OWASP LLM Top 10 2025, OWASP AI Exchange, MITRE ATT&CK v15+, MITRE ATLAS v5.1+ (including Oct 2025 agent techniques AML.T0058-T0062 — context poisoning, memory corruption, agent-in-the-middle, excessive agency runtime, cascading agent failures), CWE Top 25 2024, NIST AI 600-1. New ADR-023 records the sibling detection variant as a second documented lean-agent shape. Shared reference `finding-format-shared.md` gains a "For Threat Agents" producer section (additive-only); OWASP 3×3 risk matrix now lives in exactly one canonical file (`severity-bands-shared.md`, normalized to Unicode ×). T057 live regeneration on agentic-app confirmed +8 new findings (22 baseline → 30). Zero new runtime dependencies (SC-014 — empty diff on `pyproject.toml`, `requirements*.txt`, `package.json`). 68 tasks across 18 waves; 5 user stories delivered. PR #151 merged via squash (commit 6f9a40d). |
| 2026-04-12 | F-141: MAESTRO Phase 2 — Cross-Layer Attack Chain Analysis | [141](../02_PRD/141-maestro-cross-layer-attack-chains-2026-04-12.md) | Cross-layer attack chain correlation engine identifying multi-layer MAESTRO attack paths from threat findings. New attack-chains.md artifact, threat report Section 6 narrative, PDF chain diagram pages, schema additions (attack-chain.yaml), parser additions (tachi_parsers.py), 800+ lines test coverage. Transforms tachi from "STRIDE tool with MAESTRO labels" to "full MAESTRO implementation" with canonical CSA cross-layer deliverables. 6 user stories delivered across 7 implementation waves. |
| 2026-04-15 | F-143: MAESTRO Phase 4 — OWASP AIVSS Evaluation ADR | [143](../02_PRD/143-maestro-aivss-evaluation-adr-2026-04-14.md) | Documentation-only ADR spike evaluating OWASP AIVSS v0.8 against tachi's four-dimensional composite scoring model across three surfaces (dimensions, composite formula weights, severity band thresholds). Decision: **diverge** from AIVSS at present time — tachi's existing `(0.35 × CVSS 3.1) + (0.30 × Exploitability) + (0.20 × Reachability) + (0.15 × Scalability)` composite remains canonical; AIVSS v0.8 documented as a peer agentic-AI scoring framework tachi is aware of and intentionally non-aligned with. New **ADR-024** (`docs/architecture/02_ADRs/ADR-024-owasp-aivss-evaluation.md`, Status: Accepted) delivers the three-surface side-by-side comparison with Overlap/Gap/Conflict/No equivalent row labels, two worked examples quantifying score divergence, explicit *When to Re-Evaluate* clause (AIVSS v1.0 + at least one external adopter case study), and cross-references to ADR-020 (MAESTRO classification), ADR-019 (shared cross-agent definitions), and ADR-018 (baseline-aware scoring lineage). Companion 80–200 word **AIVSS Relationship** section added to `.claude/skills/tachi-risk-scoring/SKILL.md` reflecting the ADR decision (cross-surface consistency verified — both surfaces use "diverge"). **Zero production code changes** — no schemas, scripts, agents, example outputs, or pipeline dependencies modified. Closes umbrella MAESTRO compliance discovery [#136](https://github.com/davidmatousek/tachi/issues/136); Phases 1–3 delivered in F-136, F-141, F-082. Conditional follow-on (T023) N/A — Option C (diverge) chosen, no adopt-path implementation feature filed. 32 tasks complete + 1 N/A (T023 conditionally skipped per decision). 4 user stories delivered. PR #167 squash-merged to main 2026-04-15. |
| 2026-04-17 | F-180: F-A1 Taxonomy Crosswalk Collection | [180](../02_PRD/180-taxonomy-crosswalk-collection-2026-04-17.md) | Foundation data layer for tachi's cross-framework taxonomy integration. New `schemas/taxonomy/` directory with 9 files: 7 catalog YAMLs (owasp ≥60 records, mitre-attack ≥38 techniques, mitre-atlas ≥12 including Oct 2025 agent techniques AML.T0058-T0062, nist-ai-rmf exactly 72 Subcategories per FR-021 amendment from PRD-era 68 to authoritative count, cwe ≥53 including CWE Top 25 2025, tachi-control-category exactly 8, tachi-stride-ai-category exactly 11) + crosswalk.yaml (526 primary edges, exceeding ≥500 Tier 1 floor from PRD Risk R3) + README.md with runnable Python snippet, per-framework provenance, 3-level confidence calibration rubric with anti-drift rule, and "What F-A1 does NOT give you today" subsection. New **ADR-027** (Accepted) records per-item record shape, per-edge record shape, 7-value taxonomy enum, 3-value edge_type enum (primary/related/superseded), 3-value confidence enum (high/medium/low), and scope-exception rationale. New integrity test suite `tests/schemas/test_taxonomy_integrity.py` (4+1 tests) enforces FR-027 through FR-032 — referential integrity, closed-enum membership, citation shape validation. FR-022 NIST AI RMF Surface B verbatim transcription complete (27 edges from `nist-ai-rmf-mapping.md` Surface B); Surface C (GAI Risk → STRIDE+AI, 15 edges) deferred to F-A1.1 follow-on per architect Option (c) decision — Surface C target taxonomy (NIST AI 600-1 §2.X GAI Risks) is structurally distinct from AI RMF Subcategories and requires 8th taxonomy enum value + new `nist-ai-600-1.yaml` catalog. **Zero production code changes** — no modifications to `scripts/`, `.claude/agents/`, `.claude/skills/`, `.claude/commands/`, `templates/`, or existing `schemas/*.yaml` (except additive 2 cross-reference links to top-level `README.md` and `docs/architecture/00_Tech_Stack/README.md` per FR-038). Zero new runtime dependencies (pyyaml already in requirements-dev.txt). 3 follow-on Issues filed: F-A1.1 (Surface C), F-A1.2 (related/superseded edges), F-A1.3 (citation link-rot). Unblocks F-A2 (source_attribution schema field) and F-B (coverage attestation report section). 41/41 tasks complete across 5-day build structure; 5 user stories delivered. Governance: PM + Architect + Team-Lead sign-off (all APPROVED_WITH_CONCERNS with resolved concerns inline). Two PM sign-off amendments during feature execution: (1) FR-021 count amendment 68→72 under FR-024 primary-source-correction discipline, (2) Surface C Option (c) scope-narrow. PR #181 squash-merged to main 2026-04-17 (commit 8b7c7bf). |
| 2026-04-17 | F-189: F-A2 Source Attribution Schema Extension | [189](../02_PRD/189-source-attribution-schema-extension-2026-04-17.md) | **2nd of 11 features in BLP-01 multi-taxonomy coverage initiative (Foundation tier: F-A1 → F-A2 → F-A3)** — delivered same day as F-A1 (Feature 180) foundation data layer. Finding-side bridge closing the one-sided structural gap F-A1 left: tachi now has both a machine-readable framework vocabulary (F-A1's 7 catalog YAMLs + 526-edge crosswalk) AND machine-readable finding-to-framework citations (F-A2's `source_attribution` field), making coverage claims aggregable as data rather than free-text prose. `schemas/finding.yaml` minor bump 1.4 → 1.5 per ADR-026 additive-field rule; new optional `source_attribution` list-of-RECORD field with per-record shape `{taxonomy, id, relationship}`. Closed 5-value `taxonomy` enum `{owasp, mitre-attack, mitre-atlas, nist-ai-rmf, cwe}` — deliberately excludes the 2 tachi-internal taxonomies (`tachi-control-category`, `tachi-stride-ai-category`) from F-A1 because they are internal vocabulary, not external frameworks tachi claims coverage of. Closed 3-value `relationship` enum `{primary, related, derived}` with `primary` default. Parser round-trip in `scripts/tachi_parsers.py::parse_threats_findings` preserves conditional-key semantics: field omitted when absent from source (NOT defaulted to empty array — preserves Feature 104 `delta_status` precedent). Two-tier validation architecture: V1/V2/V3/V5 enum validation at parse time (unknown taxonomy, unknown relationship, malformed record shape, duplicate taxonomy-id pairs); V4 referential-integrity validation as separate callable phase (Phase 4 Assess caller) resolving each `{taxonomy, id}` against the F-A1 catalog YAMLs — separation preserves parser purity and enables downstream consumers to defer referential checking. New **ADR-028** (`docs/architecture/02_ADRs/ADR-028-source-attribution-schema-extension.md`, Status: Accepted) records the additive-optional-field decision under the ADR-026 minor-bump rule (naming this the "first list-of-RECORD precedent" — extends ADR-026's enum-typed-field precedent), the serialization-surface choice (new conditional Section 9 YAML block gated by `has-source-attribution` boolean, mirroring Feature 141 `has-attack-chains` precedent), the 5-value taxonomy enum scope restriction rationale, and the V4 separate-phase validation architecture. **Zero edits to the 22-file detection tier** (11 STRIDE+AI threat agents + 11 companion skill-reference files) — preserves the zero-edit invariant established in Feature 082 under ADR-023. **Zero new runtime dependencies** (empty diff on `pyproject.toml`, `requirements*.txt`, `package.json`). **Byte-identity SC-2 regression gate green**: all 5 non-agentic example PDF baselines regenerate byte-identically under `SOURCE_DATE_EPOCH=1700000000` per ADR-021 — F-A2 is contract-only, findings in existing example threat models carry no `source_attribution` and serialize identically pre/post-F-A2. 36/36 tasks complete across 3-day build structure (2026-04-20 Monday → 2026-04-22 Wednesday, spanning a weekend gap from 2026-04-18 Saturday PRD approval). Governance: PM APPROVED_WITH_CONCERNS + Architect APPROVED + Team-Lead APPROVED_WITH_CONCERNS. 3 user stories delivered (US-189-1 multi-framework citation, US-189-2 parser round-trip backward compatibility, US-189-3 closed-enum relationship with referential integrity). Unblocks downstream **F-A3** (threat-detection agent populators — threat agents choosing to cite IDs during detection) and **F-B** (coverage attestation report section — aggregates `source_attribution` across findings to produce per-framework coverage percentages). PR #190 squash-merged to main 2026-04-17 (commit `6d5d890`). |
