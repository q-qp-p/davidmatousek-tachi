# Architecture Documentation - tachi

**Last Updated**: 2026-04-11
**Owner**: Architect
**Status**: Template

---

## Overview

This directory contains all technical architecture documentation for tachi.

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
- `ADR-024-owasp-aivss-evaluation.md` - **Accepted (2026-04-15)**: tachi diverges from OWASP AIVSS v0.8 at the present time; existing four-dimensional weighted-sum composite (`0.35 × CVSS 3.1 + 0.30 × Exploitability + 0.20 × Reachability + 0.15 × Scalability`) remains the canonical scoring model. Records the three-surface evaluation (dimension set, composite formula, severity bands) against AIVSS v0.8, establishes structural divergence on Surfaces A (dimensions) and B (formula) with alignment only on Surface C (severity bands), and defines re-evaluation triggers (AIVSS v1.0 + at least one external adopter case study). Cross-references ADR-020 (MAESTRO classification), ADR-019 (shared definitions), ADR-018 (baseline-aware scoring lineage). Closes the MAESTRO compliance umbrella (Phases 1-3 delivered in Features 136, 141, 082). Documentation-only spike — zero schema, script, or pipeline changes (Feature 143)
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
