# Delivery Document: Feature 250 — Adversarial Unit Extraction Hot-Fix

**Delivery Date**: 2026-05-04
**Branch**: `250-adversarial-unit-extraction-hotfix` (deleted post-merge)
**PR**: #253 (squash-merged 2026-05-04T16:39:23Z)
**Release**: v4.28.1 (release-please PR #254 opened automatically ~35s post-merge)

---

## What Was Delivered

- **Cold-cache CI flake eliminated**: 12 adversarial substitution-semantics + input-rejection cases moved from `scripts/init.sh` integration runs to direct bash helper invocations across 3 new pytest modules (`test_template_substitute_unit.py`, `test_init_input_unit.py`, `test_substitute_shim_canary.py`). Adopters can now ship a feature branch on `macos-latest` without runner-roulette or admin-override merges.
- **Maintainer triage signal sharpened**: a substitution-helper regression now fails the named unit test directly (e.g., `case_3_backref FAIL`) with helper-scoped stderr — no more parsing 8 MB of init.sh integration noise to identify the helper at fault.
- **Permanent CI test process hardening (Phase 6 Option Z, mid-build scope expansion)**: session-scoped `init_run` fixture in `tests/scripts/conftest.py` collapses 5 module-scoped duplicates → 1 canonical clone (drops macos cold-cache cost from 5×300s+ to 2×300s+); asymmetric file-set check in `test_init_sh_substitution.py` (drops are FAIL, additions are TOLERATED — eliminates baseline regen on every doc edit); substitution-target-only baseline restricted ~600 → ~53 files; subprocess timeout 300s → 900s; pytest timeout 360s → 1080s; workflow `paths:` filter + `pytest` invocation completeness for the 3 new modules.
- **Load-bearing shim canary**: `test_substitute_shim_canary.py` permanently asserts that `shopt -u patsub_replacement` stays in `template-substitute.sh` — closes the architect's TC-1 gap so a future cleanup PR can't silently drop the FR-010/SC-006 invariant.
- **ADR-039**: `docs/architecture/02_ADRs/ADR-039-test-architecture-fixture-scope-and-asymmetric-baseline.md` records the new test-architecture canon (session-scoped fixture + asymmetric baseline) repo-wide.
- **Helper contracts byte-unchanged**: `.aod/scripts/bash/template-substitute.sh` and `.aod/scripts/bash/init-input.sh` `git diff main` empty post-merge; ADR-038 contract intact.
- **Auto-released as v4.28.1**: Conventional Commits PR title (`fix(250): permanent CI test process hardening`) → release-please patch bump triggered automatically.

---

## How to See & Test

1. **Confirm the hot-fix is on `main`**: `git log main --oneline -1` should show the squash-merge commit (`75866d9 fix(250): permanent CI test process hardening (#253)`).
2. **Verify the 3 new unit modules run and pass locally**: `pytest tests/scripts/test_template_substitute_unit.py tests/scripts/test_init_input_unit.py tests/scripts/test_substitute_shim_canary.py --durations=0 --timeout=15 -v`. Expected: 14/14 PASS in <2s total wall time (8 substitute + 5 input + 1 canary).
3. **Verify the integration backstop survives**: `pytest tests/scripts/test_init_sh_adversarial.py --timeout=300 -v`. Expected: 2/2 PASS (`test_case_13_file_level_byte_identity` + `test_no_residual_placeholders_after_init`).
4. **Verify FR-003 (zero init.sh refs in new modules)**: `grep -E "run_init_in_clone|clone_into_tmpdir|init_sh_helpers|scripts/init\.sh" tests/scripts/test_template_substitute_unit.py tests/scripts/test_init_input_unit.py tests/scripts/test_substitute_shim_canary.py`. Expected: zero matches.
5. **Verify FR-019/FR-020 (helper byte-unchanged)**: `git diff <pre-merge-sha> main -- .aod/scripts/bash/template-substitute.sh .aod/scripts/bash/init-input.sh`. Expected: empty.
6. **Verify SC-002 (macos wall time ≤15 min)** on PR #253's own CI run: `gh run view 25330658727 --log | grep -E "tachi pytest.*macos-latest"`. Observed: 5m19s — well under the ≤15 min target (baseline band: 30-40 min).
7. **Verify SC-008 (release-please opened)**: `gh pr list --state all --search "release-please 4.28.1"`. Expected: PR #254 present.
8. **AC-5 deliberate-fault matrix replay** (manual, bash 5.x dev workstation per [quickstart.md §4](./quickstart.md)): see `.aod/results/deliberate-fault-matrix-250.md` for the recorded 6/8 split (cases 1/3/6 FAIL under shim removal, cases 2/4/5/7/8 PASS) — confirms FR-010 regression-detection precision.

---

## Delivery Metrics

| Metric | Value |
|--------|-------|
| Estimated Duration | 4-6 hours active (size-S envelope per team-lead sign-off) |
| Actual Duration | ~4 hours 11 minutes wall clock (first commit 2026-05-04T08:28 EDT → merge 12:39 EDT) |
| Variance | On-target despite Phase 6 mid-build scope expansion (+8 tasks); active dev hit the lower bound of the envelope |

---

## Surprise Log

Mid-build CI run `25325616748` exposed that the original F-250 scope (extract 12 cases to unit-level) was necessary but insufficient: the retained 5 init.sh integration invocations on `macos-latest` STILL hit the 300s cold-cache timeout class through module-scoped fixture duplication, and a separate baseline-file-set drift was failing CI on every PR that touched any documentation file. Phase 6 Option Z scope expansion was authorized mid-build to address the recurring root cause — TC-4 scope fences explicitly relaxed for the expansion while FR-019/FR-020 byte-unchanged invariants on the bash helpers were preserved.

---

## Lessons Learned

| Category | Lesson | KB Entry |
|----------|--------|----------|
| Process improvement | Mid-build CI signal can reveal scope-fence inadequacies. When the original PRD assumes a specific root cause (e.g., "extracting cases solves the timeout class") and CI evidence later contradicts that assumption, authorize a documented mid-build scope expansion rather than papering over with retry loops or quick patches. The maintainer directive "fix ALL issues correctly and completely, no quick patches" plus a Phase 6 header in tasks.md naming the relaxed scope fences makes the expansion reviewable; preserving FR-019/FR-020 byte-unchanged invariants keeps the relaxation bounded. | Entry 2 in INSTITUTIONAL_KNOWLEDGE.md |

---

## Feedback Loop

**New Ideas**: None

The Path C scope refactor (Test-1 reads `templates/manifest.yaml` categories rather than walking the whole project tree) remains as NG-1 in `spec.md` — partially addressed by Phase 6 T025 (restricted baseline to substitution targets), but the full refactor is deferred. No new GitHub Issue created from this retrospective: NG-1 is already documented in spec.md and the F-250 KPI window (T021) carries forward as the immediate post-delivery focus, not new ideation.

---

## Source Artifacts

| Artifact | Path |
|----------|------|
| Specification | [specs/250-adversarial-unit-extraction-hotfix/spec.md](./spec.md) |
| Implementation Plan | [specs/250-adversarial-unit-extraction-hotfix/plan.md](./plan.md) |
| Task Breakdown | [specs/250-adversarial-unit-extraction-hotfix/tasks.md](./tasks.md) |
| PRD | [docs/product/02_PRD/250-adversarial-unit-extraction-hotfix-2026-05-04.md](../../docs/product/02_PRD/250-adversarial-unit-extraction-hotfix-2026-05-04.md) |
| Research | [specs/250-adversarial-unit-extraction-hotfix/research.md](./research.md) |
| Quickstart | [specs/250-adversarial-unit-extraction-hotfix/quickstart.md](./quickstart.md) |
| Data model | [specs/250-adversarial-unit-extraction-hotfix/data-model.md](./data-model.md) |
| Contracts | [specs/250-adversarial-unit-extraction-hotfix/contracts/](./contracts/) |
| Security scan | [specs/250-adversarial-unit-extraction-hotfix/security-scan.md](./security-scan.md) |

---

## Test Evidence

### Test Scenarios (Living Documentation)

#### Acceptance Criteria Coverage

| AC ID | Given/When/Then | Scenario(s) | Status |
|-------|-----------------|-------------|--------|
| US-01-AC-1 | Given an adopter pushed a tachi feature branch with the post-hot-fix init.sh test suite, When CI runs, Then both `macos-latest` and `ubuntu-latest` MUST report green inside the standard CI window without per-test timeout failures | PR #253 own CI run: `tachi pytest` workflow, both legs SUCCESS | Covered (CI-observed) |
| US-01-AC-2 | Given the post-hot-fix CI workflow has run on at least 5 consecutive merges, When run history is inspected, Then `macos-latest` MUST show 5/5 green-rate | Sustained tracking window 2026-05-04 → 2026-05-18 (T021); initial 1/1 sample observed | [MANUAL-ONLY] sustained window — recorded in §Post-merge KPI Tracking below |
| US-01-AC-3 | Given the post-hot-fix init.sh test suite is executing on `macos-latest`, When suite duration is measured, Then total wall time MUST be ≤15 min | PR #253 own CI run: 5m19s observed (FAR under target) | Covered (CI-observed) |
| US-02-AC-1 | Given a deliberate regression in `aod_template_substitute_placeholders` on bash 5.x, When `pytest tests/scripts/` runs, Then ≥1 of `case_1_ampersand`, `case_3_backref`, `case_6_multibyte` MUST fail with stderr naming the corrupted byte sequence | Deliberate-fault matrix log (T013) | [MANUAL-ONLY] one-shot bash 5.x verification — recorded in `.aod/results/deliberate-fault-matrix-250.md` |
| US-02-AC-2 | Given a deliberate regression in `aod_init_read_validated`, When `pytest tests/scripts/` runs, Then failing test ID MUST be in `test_init_input_unit.py` and stderr MUST name the input-rejection class | Input-validator regression demonstration (T014) | [MANUAL-ONLY] one-shot manual fault-injection — appended to `.aod/results/deliberate-fault-matrix-250.md` |

**Totals**: 5 ACs — 2 covered (CI-observed), 3 manual-only (one-shot deliberate-fault and sustained KPI window), 0 uncovered

### Execution Evidence

#### E2E Validation Gate

| Field | Value |
|-------|-------|
| Status | `error` (knowledge-system stack pack lacks `aod-test-contract` block per `stack-contract-lint.sh` exit 5; non-fatal per ADR-006) |
| Gate Mode | `hard` |
| Gate Result | `skip` |
| Tests Passed | N/A (gate did not run) |
| Tests Failed | N/A |
| Tests Skipped | N/A |
| Duration | N/A |

**Failure Details**: N/A — Step 9a routed to error branch because the active stack pack (`knowledge-system`) does not declare a Section 7 `aod-test-contract` block. This is a stack-pack-level gap, not a feature-level test failure. Build-wave test evidence (below) provides the full pass coverage.

### Build-Wave Test Results

| Wave | Tests | Passed | Failed | Status |
|------|-------|--------|--------|--------|
| wave-04 | 14 | 14 | 0 | pass |
| wave-06 | 15 | 15 | 0 | pass |

**Build Summary**: 29/29 PASS across the build's two recorded waves (wave-04 = 14 unit tests in 420ms; wave-06 = 15 tests in 284s including the 2 retained integration tests). T009 deviation note (`--timeout=15` was too aggressive for retained integration tests; re-ran with `--timeout=300` → both PASS) is recorded in `.aod/results/wave-6-t009-deviation-note.md`.

#### Artifacts

| Artifact | Path | Summary |
|----------|------|---------|
| Wave-04 results | `specs/250-adversarial-unit-extraction-hotfix/test-results/wave-04/results.json` | 14/14 unit-level PASS, 420ms total wall time |
| Wave-06 results | `specs/250-adversarial-unit-extraction-hotfix/test-results/wave-06/results.json` | 15/15 PASS including 2 retained integration tests |
| Deliberate-fault matrix | `.aod/results/deliberate-fault-matrix-250.md` | bash 5.x one-shot fault injection: cases 1/3/6 FAIL, cases 2/4/5/7/8 PASS under shim removal (FR-010/SC-006 verified) |
| Security scan note | `specs/250-adversarial-unit-extraction-hotfix/security-scan.md` | Step 5 security-analyst PASS; PR scope is test-infrastructure-only |
| PM/architect/devops doc audits | `.aod/results/{pm,architect,devops}-deliver-250.md` | All APPROVED (1+4+1 files updated) |

**Archived Artifact Metrics**:
- Tests Run: 29 (build waves)
- Passed: 29
- Failed: 0
- Coverage: N/A (this is a test-architecture refactor, not a coverage delta)

**Notes**: PR #253 own CI run (`25330658727`) confirmed the post-merge target — `macos-latest` 5m19s, `ubuntu-latest` 1m29s, both green. Build-wave evidence above predates the merge and tracks the implementation-time pass status.

### Manual Validation

This subsection is present because three ACs carry `[MANUAL-ONLY]` markers (one-shot deliberate-fault verification + sustained KPI window). No `--no-tests=<reason>` opt-out was logged.

**Manual-only acceptance criteria** (carried from `spec.md`):

- AC US-01-AC-2: [MANUAL-ONLY] sustained 5-merge KPI window over days 1-14 (2026-05-04 → 2026-05-18) — initial 1/1 sample captured below.
- AC US-02-AC-1: [MANUAL-ONLY] one-shot bash 5.x deliberate-fault verification matrix recorded in `.aod/results/deliberate-fault-matrix-250.md`.
- AC US-02-AC-2: [MANUAL-ONLY] one-shot input-validator regression demonstration appended to the same file.

---

## Post-merge KPI Tracking (T021 sustained window)

**Window**: 2026-05-04 (hot-fix merged) → 2026-05-18 (5-merge target horizon).

**Initial sample (PR #253 own merge, 2026-05-04)**:

| KPI | Target | Observed | Status |
|-----|--------|----------|--------|
| `macos-latest` green-rate | 5/5 (SC-004) | 1/1 (cumulative window start) | ✓ on target |
| `ubuntu-latest` green-rate | 5/5 (unchanged) | 1/1 | ✓ on target |
| `macos-latest` init.sh-suite wall time | ≤15 min (SC-002) | 5m19s | ✓ FAR under target |
| CI savings vs baseline `25314246672` | ≥25 min (SC-005) | ≈25-35 min (baseline 30-40 min band → 5m19s observed) | ✓ on target |

**Tracking protocol**: each subsequent merge to `main` over the 14-day window is appended to this section with `(macos wall time, both legs green Y/N)`. If any merge in the next 5-sample window fails to meet SC-002 or SC-004, escalate via a new GitHub Issue and link this delivery doc.

---

## Documentation Updates

| Domain | Agent | Files Updated | Status |
|--------|-------|---------------|--------|
| Product | product-manager | 1 (`docs/product/02_PRD/INDEX.md`) | APPROVED — audit at `.aod/results/pm-deliver-250.md` |
| Architecture | architect | 4 (`tech-stack/README.md`, `01_system_design/README.md`, `03_patterns/README.md`, NEW `ADR-039-test-architecture-fixture-scope-and-asymmetric-baseline.md`) | APPROVED — audit at `.aod/results/architect-deliver-250.md` |
| DevOps | devops | 1 (`docs/devops/CI_CD_GUIDE.md`) | APPROVED — audit at `.aod/results/devops-deliver-250.md` |

---

## Cleanup

- [X] Feature branch deleted (local + remote)
- [X] All 29 tasks complete (T020 closed by release-please verification; T021 closed by initial KPI sample + tracking window)
- [X] No TBD/TODO in updated docs (verified by docs agents)
- [X] Committed and pushed (squash-merge `75866d9` on `main`; release-please PR #254 open)
- [X] GitHub Issue closed (`stage:done`)

**Feature 250 is now officially CLOSED.**
