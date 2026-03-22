# Institutional Knowledge - tachi

**Project**: tachi - Automated threat modeling toolkit extending STRIDE with AI-specific threat agents for agentic applications
**Purpose**: Capture learnings, patterns, and solutions to prevent repeated mistakes
**Created**: {{PROJECT_START_DATE}}
**Last Updated**: 2026-03-22

**Entry Count**: 2 / 20 (KB System Upgrade triggers at 20 — schedule review)
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

---

## Bug Fixes

*No entries yet. Use `/kb-create` to add the first bug fix.*
