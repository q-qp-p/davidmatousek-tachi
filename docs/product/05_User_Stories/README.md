# User Stories - tachi

**Last Updated**: 2026-03-27
**Owner**: Product Manager (product-manager)
**Status**: Template - Complete after MVP launch

---

## When to Create This

**Create detailed user stories AFTER your MVP launches.**

Before MVP:
- High-level features in your PRD are sufficient
- Detailed stories emerge from real usage patterns
- You'll waste time on stories for features that change

**Pre-MVP**: Use PRD feature list → build MVP
**Post-MVP**: Break down backlog items into user stories based on actual needs

**Exception**: You can create user stories for complex MVP features that need detailed acceptance criteria.

> **AOD Lifecycle Note**: User stories generated during `/aod.discover` validation
> are stored in **GitHub Issue bodies**, not in this directory. GitHub Issues with
> `stage:*` labels are the source of truth for user stories. This directory serves
> as a reference for story format and prioritization guidance. See
> `docs/product/_backlog/BACKLOG.md` for the auto-generated backlog index.

---

## Overview

User stories describe features from the user's perspective. They follow the format:
> **As a** [persona], **I want** [capability], **so that** [benefit]

---

## User Story Template

```markdown
## US-NNN: [Story Title]

**As a** [persona from target-users.md],
**I want** [capability or action],
**so that** [business value or user benefit].

### Acceptance Criteria
- [ ] Given [context], when [action], then [expected result]
- [ ] Given [context], when [action], then [expected result]

### Related
- **Persona**: [Link to target-users.md]
- **PRD**: [Link to relevant PRD]
- **Priority**: [P0 Critical | P1 High | P2 Medium | P3 Low]

### Notes
[Additional context or constraints]
```

---

## User Story Prioritization

### P0 - Critical (Must Have)
- Blocks core user workflows
- Required for product to function
- Legal or security requirements

### P1 - High (Should Have)
- High user value
- Significant pain point resolution
- Competitive differentiation

### P2 - Medium (Nice to Have)
- Moderate user value
- Quality of life improvements
- Secondary workflows

### P3 - Low (Future)
- Low user value
- Edge cases
- Deferred for later phases

---

## Integration with PRDs

Each PRD should include relevant user stories:
- PRD functional requirements map to user stories
- User story acceptance criteria become PRD requirements
- User stories validated during spec creation

---

**Template Instructions**: Organize user stories by phase or feature area. Delete this message after creating your first user stories.

---

## Aggregated Feature Stories

> **Auto-populated by `/aod.deliver`**: When a feature is delivered, `/aod.deliver` extracts
> validated user stories from the feature's GitHub Issue body and appends them below under
> a `### Feature NNN: {feature-name}` heading. **GitHub Issues remain the source of truth**
> for user stories — this section provides a consolidated reference across delivered features.

<!-- Stories are appended below this line by /aod.deliver -->

### Feature 089: AOD Lifecycle Documentation Completeness

**PRD**: [089-aod-lifecycle-documentation-completeness](../02_PRD/089-aod-lifecycle-documentation-completeness-2026-03-12.md)
**Delivered**: 2026-03-12 | **PR**: #90 | **Tasks**: 16/16 complete | **Stories**: 7/7 passing

- **US-089-1** (P0): System Design Auto-Scaffolding from Plan - Auto-generate `docs/architecture/01_system_design/README.md` from approved plan.md content
- **US-089-2** (P0): Delivery File Change Validation - Validate that documentation agents produce actual file changes during `/aod.deliver`
- **US-089-3** (P1): User Story Export During Delivery - Extract validated user stories from GitHub Issues and append to `05_User_Stories/README.md`
- **US-089-4** (P1): Vision Placeholder Guard in Define - Warn on unresolved template placeholders in vision files during `/aod.define`
- **US-089-5** (P1): Template Placeholder Resolution in Scaffold - Auto-resolve `tachi` and `2026-03-21` in `docs/` after `/aod.stack scaffold`
- **US-089-6** (P1): Closure Summary Relocation - Write closure summaries to `.aod/closures/` instead of `docs/architecture/`
- **US-089-7** (P2): Quarterly Planning Scaffolds - New `/aod.roadmap` and `/aod.okrs` commands for planning document scaffolding

### Feature 091: Delivery Document Generation

**PRD**: [091-delivery-document-generation](../02_PRD/091-delivery-document-generation-2026-03-13.md)
**Delivered**: 2026-03-13 | **PR**: #92 | **Tasks**: 10/10 complete | **Stories**: 3/3 passing

- **US-091-1** (P0): Automatic Delivery Document Generation - Auto-generate `specs/{NNN}-*/delivery.md` during `/aod.deliver` with all retrospective sections populated
- **US-091-2** (P0): Testing Instructions in Delivery Document - Step-by-step "How to See & Test" section with numbered verification steps mapping to acceptance criteria
- **US-091-3** (P1): Delivery Metrics Persistence - Estimated vs actual duration and variance in a consistent format across all delivery documents

### Feature 093: Relocate Governance Results

**PRD**: [093-relocate-governance-results](../02_PRD/093-relocate-governance-results-2026-03-19.md)
**Delivered**: 2026-03-19 | **PR**: #94 | **Tasks**: 16/16 complete | **Stories**: 2/2 passing

- **US-093-1** (P0): Uninterrupted Governance Reviews - Governance review results written to `.aod/results/` without triggering Claude Code permission prompts during `/aod.define`, `/aod.plan`, and `/aod.build`
- **US-093-2** (P0): Consistent Results Directory Convention - All agent/skill documentation references `.aod/results/` as the canonical results path in `_AGENT_BEST_PRACTICES.md` and `CLAUDE.md`

### Feature 001: Project Skeleton & Interface Contract

**PRD**: [001-project-skeleton-interface-contract](../02_PRD/001-project-skeleton-interface-contract-2026-03-21.md)
**Delivered**: 2026-03-21 | **PR**: #2 | **Tasks**: 33/33 complete | **Stories**: 5/5 passing

- **US-001** (P1): Navigable Repository Structure - All directories (`agents/`, `adapters/`, `templates/`, `examples/`, `docs/`, `schemas/`) have READMEs explaining purpose and conventions
- **US-002** (P1): Interface Contract for Integration - `docs/INTERFACE-CONTRACT.md` with 7 sections covering input formats, invocation protocol, output schema, STRIDE-per-Element normalization, and input sanitization guidance
- **US-003** (P1): Consistent Output Template - `templates/threats.md` canonical template with all 7 required sections (System Overview, Trust Boundaries, STRIDE Tables, AI Threat Tables, Coverage Matrix, Risk Summary, Recommended Actions)
- **US-004** (P2): Machine-Readable Schemas - `schemas/finding.yaml`, `schemas/input.yaml`, `schemas/output.yaml` defining IR, input validation, and output validation schemas
- **US-005** (P2): Example Inputs for Validation - 3 example threat models (`examples/ascii-web-api/`, `examples/mermaid-agentic-app/`, `examples/free-text-microservice/`) covering ASCII, Mermaid, and free-text input formats

### Feature 003: Orchestrator Agent

**PRD**: [003-orchestrator-agent](../02_PRD/003-orchestrator-agent-2026-03-21.md)
**Delivered**: 2026-03-21 | **PR**: #4 | **Tasks**: 35/35 complete | **Stories**: 6/6 passing

- **US-003-1** (P1): Parse Architecture into Component Inventory - Orchestrator parses any of 5 supported input formats (ASCII, free-text, Mermaid, PlantUML, C4), classifies components as DFD element types, identifies trust boundaries, and produces a structured System Overview
- **US-003-2** (P1): Dispatch to Correct Threat Agents - Dispatches each component to correct STRIDE and AI agents based on DFD element type (STRIDE-per-Element) and AI keyword matching, with full architecture context sent to each agent
- **US-003-3** (P1): Assemble Findings into Structured Threat Model - Collects agent findings, validates risk levels against OWASP 3x3 matrix, assembles 8 tables (6 STRIDE + 2 AI), generates coverage matrix, risk summary, and recommended actions into a complete threats.md
- **US-003-4** (P2): Handle Errors Gracefully - Returns correct error codes (UNSUPPORTED_FORMAT, NO_COMPONENTS, INVALID_FORMAT_VALUE) with actionable messages, defaults ambiguous classifications to Process, and flags non-conforming agent findings for review
- **US-003-5** (P2): Support Both Dispatch Modes - Documents both parallel (concurrent agent framework) and sequential (one-at-a-time) dispatch protocols for platform-neutral operation
- **US-003-6** (P2): Enforce Input Sanitization Boundary - Treats architecture input as data to be parsed (not instructions to follow), with clear separation between orchestrator instructions and user-provided content

### Feature 005: STRIDE Threat Agents

**PRD**: [005-stride-threat-agents](../02_PRD/005-stride-threat-agents-2026-03-21.md)
**Delivered**: 2026-03-22 | **PR**: #6 | **Tasks**: 41/41 complete | **Stories**: 4/4 passing

- **US-005-1** (P0): Spoofing and Tampering Agents - Spoofing agent examines authentication mechanisms and produces S-prefixed findings; Tampering agent examines input validation and data integrity and produces T-prefixed findings, both referencing named components from input
- **US-005-2** (P0): Repudiation and Information Disclosure Agents - Repudiation agent examines logging and audit trail gaps with R-prefixed findings; Information Disclosure agent examines data classification and encryption with I-prefixed findings, with concrete mitigations tied to system technology stack
- **US-005-3** (P0): Denial of Service and Privilege Escalation Agents - DoS agent examines rate limiting and resource quotas with D-prefixed findings; Privilege Escalation agent examines RBAC/ABAC and permission boundaries with E-prefixed findings, following STRIDE-per-Element targeting
- **US-005-4** (P0): Consistent Table Format Output - All 6 agents produce findings conforming to schemas/finding.yaml with correct ID prefixes, component references, risk levels computed via OWASP 3x3 matrix, and framework-grounded references

### Feature 010: Deduplication & Risk Rating

**PRD**: [010-deduplication-risk-rating](../02_PRD/010-deduplication-risk-rating-2026-03-22.md)
**Delivered**: 2026-03-22 | **PR**: #11 | **Tasks**: 24/24 complete | **Stories**: 3/3 passing

- **US-010-1** (P0): Cross-Agent Finding Correlation - When multiple agents flag the same component for related threats, correlated findings appear in Section 4a with all agent perspectives grouped under CG-N IDs, using 5 deterministic correlation rules (STRIDE-to-AI category pairs)
- **US-010-2** (P0): Deduplicated Risk Summary and Coverage Matrix - Coverage matrix and risk summary reflect unique threats (deduplicated counts) rather than inflated raw counts, with three-state cell model (count, "---" for analyzed-but-clean, "n/a" for not-applicable) and deduplication footnote
- **US-010-3** (P1): Risk Calibration Documentation - OWASP 3x3 risk matrix documented in the Risk Summary section for reader verification of risk ratings, with all 9 likelihood x impact combinations visible

### Feature 012: SARIF Output Generation

**PRD**: [012-sarif-output-generation](../02_PRD/012-sarif-output-generation-2026-03-22.md)
**Delivered**: 2026-03-22 | **PR**: #13 | **Tasks**: 20/20 complete | **Stories**: 4/4 passing

- **US-012-1** (P0): Export Threat Findings as SARIF 2.1.0 - Orchestrator produces a `threats.sarif` file alongside `threats.md` during Phase 4 (Assess) that validates against the SARIF 2.1.0 JSON schema, with all findings mapped to SARIF results including severity levels, rule definitions, and OWASP/CWE references
- **US-012-2** (P1): Correlated Findings in SARIF - Correlation groups from F-010 deduplication are represented in SARIF using `relatedLocations` to link primary findings to correlated peers, with `partialFingerprints.correlationGroup` preserving group identity
- **US-012-3** (P1): Architecture Component Navigation in SARIF Viewers - SARIF results include both physical locations (input file URI) and logical locations (component name, trust zone path, DFD element type) for component-level navigation in GitHub Code Scanning and other SARIF viewers
- **US-012-4** (P1): Stable Finding Tracking Across Runs - Deterministic `partialFingerprints` using component+category hashing enables GitHub Code Scanning to track findings as persistent alerts across runs without creating duplicates

### Feature 015: Threat Report Agent & Attack Trees

**PRD**: [015-threat-report-agent-attack-trees](../02_PRD/015-threat-report-agent-attack-trees-2026-03-23.md)
**Delivered**: 2026-03-23 | **PR**: #16 | **Tasks**: 29/29 complete | **Stories**: 4/4 passing

- **US-015-1** (P1): Narrative Threat Report for Management - Report agent transforms structured threats.md into a narrative report with executive summary, cross-cutting theme identification, and compliance relevance notes comprehensible by non-technical audiences
- **US-015-2** (P1): Mermaid Attack Trees for Critical/High Findings - Mermaid flowchart TD attack trees generated for every Critical and High finding using Schneier methodology (AND/OR gates, root goals, sub-goals, leaf actions), embedded inline in threat-report.md and saved as standalone files in attack-trees/
- **US-015-3** (P2): Remediation Roadmap with Prioritization - Prioritized remediation roadmap listing all mitigations ordered by risk level (Critical first) with effort estimates (low/medium/high) and dependency notes, directly convertible to development tasks
- **US-015-4** (P1): Orchestrator Integration - Report agent integrated into orchestrator pipeline as Phase 5 (Report) running after Phase 4 (Assess), producing threat-report.md and attack-trees/ alongside existing threats.md and threats.sarif outputs

### Feature 018: Threat Infographic Agent

**PRD**: [018-threat-infographic-agent](../02_PRD/018-threat-infographic-agent-2026-03-23.md)
**Delivered**: 2026-03-23 | **PR**: #19 | **Tasks**: 18/18 complete | **Stories**: 4/4 passing

- **US-018-1** (P0): Visual Threat Infographic Specification - Infographic agent transforms structured threats.md into a visual risk specification (threat-infographic-spec.md) with risk distribution, coverage heat map data, top critical findings summary, and CVSS color palette for executive communication
- **US-018-2** (P0): Automated Image Generation via Gemini API - When Gemini API is available, the agent produces a presentation-ready threat-infographic.jpg from the infographic specification; when unavailable, the spec is saved as a standalone markdown document for manual rendering
- **US-018-3** (P0): Optional and Configurable Infographic Generation - Infographic generation is opt-out via orchestrator configuration, ensuring all tachi features remain fully functional without Gemini API access
- **US-018-4** (P0): Pipeline Integration as Phase 6 - Infographic agent integrated into orchestrator pipeline as Phase 6 (Infographic) running after Phase 5 (Report), with output validation and opt-out support

### Feature 021: Platform Adapters

**PRD**: [021-platform-adapters](../02_PRD/021-platform-adapters-2026-03-23.md)
**Delivered**: 2026-03-23 | **PR**: #22 | **Tasks**: 40/40 complete | **Stories**: 5/5 passing

- **US-021-1** (P0): Claude Code Adapter - Claude Code adapter maps orchestrator + 13 threat agents into `.claude/agents/tachi/` format with parallel dispatch via Agent tool and single `cp -r` installation
- **US-021-2a** (P1): Cursor Adapter - Cursor adapter maps agents to `.cursor/rules/` format with full prompt content preserved and platform-specific conventions followed
- **US-021-2b** (P1): Copilot Adapter - Copilot adapter maps agents to `.github/copilot/` format with full prompt content preserved and platform-specific conventions followed
- **US-021-3** (P1): GitHub Actions Adapter - GitHub Actions workflow triggers on architecture file changes, invokes agents via LLM API, generates `threats.md` and `threats.sarif`, and uploads SARIF to GitHub Code Scanning
- **US-021-4** (P0): Generic Adapter - Generic adapter provides standalone prompt files with sequential (chat UI) and programmatic (LLM API) invocation instructions requiring no platform-specific tooling

### Feature 024: Example Threat Models

**PRD**: [024-example-threat-models](../02_PRD/024-example-threat-models-2026-03-23.md)
**Delivered**: 2026-03-23 | **PR**: #25 | **Tasks**: 50/50 complete | **Stories**: 3/3 passing

- **US-024-1** (P1): Web Application Example - As a new user, I want a web application example, so that I can see what Tachi produces for a traditional architecture before running it on my own system
- **US-024-2** (P1): Agentic Application Example - As an AI developer, I want an agentic application example, so that I can see how the +AI agents add value beyond standard STRIDE
- **US-024-3** (P1): Microservices Example - As a platform engineer, I want a microservices example, so that I can see how Tachi handles complex multi-service architectures with many trust boundaries

### Feature 029: Agent Refactoring — Right-Size

**PRD**: [029-agent-refactoring-right-size](../02_PRD/029-agent-refactoring-right-size-2026-03-25.md)
**Delivered**: 2026-03-25 | **PR**: #30 | **Tasks**: 27/27 complete | **Stories**: 4/4 passing

- **US-029-1** (P0): Orchestrator Right-Sizing - Orchestrator reduced to ~1,100-1,200 lines (from 2,085) with SARIF generation spec, validation checklist, and error templates extracted to on-demand reference documents
- **US-029-2** (P0): Report Agent Right-Sizing - Threat-report agent reduced to ~300-400 lines (from 801) with output templates and verbose examples extracted to reference documents
- **US-029-3** (P0): Infographic Agent Right-Sizing - Threat-infographic agent reduced to ~300-400 lines (from 592) with Gemini API specification and error handling extracted to reference documents
- **US-029-4** (P0): Zero Regression on Threat Agents - All 11 STRIDE/AI threat agents byte-identical before and after refactoring, SARIF 2.1.0 validated, output structure equivalent on example architecture

### Feature 035: Quantitative Risk Scoring

**PRD**: [035-quantitative-risk-scoring](../02_PRD/035-quantitative-risk-scoring-2026-03-27.md)
**Delivered**: 2026-03-27 | **PR**: #37 | **Tasks**: 29/29 complete | **Stories**: 4/4 passing

- **US-035-1** (P0): Quantitative Threat Scoring - As a security engineer, each threat scored with CVSS base + exploitability + scalability + reachability, producing composite score (0.0–10.0) with dimensional breakdown for quantitative remediation prioritization
- **US-035-2** (P0): Risk Governance Fields - As a security manager, risk governance fields (owner, SLA, disposition, review date) attached to each scored threat for remediation tracking and risk acceptance decisions
- **US-035-3** (P0): Dual Output Formats - As a security manager, scored output in both human-readable (risk-scores.md) and machine-readable (risk-scores.sarif) formats for reporting and GRC tooling integration
- **US-035-4** (P1): Reachability-Aware Scoring - As an architect, reachability analysis accounts for trust boundaries from architecture diagrams, scoring internet-facing components higher than well-protected internal components

### Feature 036: Compensating Controls Analysis

**PRD**: [036-compensating-controls](../02_PRD/036-compensating-controls-2026-03-27.md)
**Delivered**: 2026-03-28 | **PR**: #40 | **Tasks**: 21/21 complete | **Stories**: 5/5 passing

- **US-036-1** (P0): Codebase Control Detection - As a developer, scan codebase for existing controls that address identified threats, classifying each as Control Found (with file:line evidence), Partial Control, or No Control Found
- **US-036-2** (P0): Compensating Control Recommendations - As a security engineer, actionable compensating control recommendations for each unmitigated threat, prioritized by risk score, with implementation guidance and effort estimates (Low/Medium/High)
- **US-036-3** (P0): Coverage Matrix - As a security manager, coverage matrix showing control status across all threats with summary statistics (% covered, % partial, % unmitigated) for at-a-glance security posture assessment
- **US-036-4** (P0): Residual Risk Calculation - As a security manager, residual risk calculated for each threat accounting for existing controls, with total inherent vs. total residual risk summary
- **US-036-5** (P0): Control Effectiveness Assessment - As an architect, control effectiveness assessed beyond mere existence — evaluating whether controls are adequate or need hardening
