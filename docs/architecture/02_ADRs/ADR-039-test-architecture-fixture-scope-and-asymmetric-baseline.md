# ADR-039: Test Architecture — Session-Scoped init.sh Fixture and Asymmetric Baseline File-Set Check

**Status**: Accepted
**Date**: 2026-05-04 (Proposed and Accepted same-day during F-250 Phase 6 Option Z mid-build expansion)
**Deciders**: Architect (tachi project), Maintainer
**Feature**: [250-adversarial-unit-extraction-hotfix](../../../specs/250-adversarial-unit-extraction-hotfix/spec.md)
**Supersedes**: None
**Superseded by**: None
**Related ADRs**: [ADR-038](ADR-038-placeholder-substitution-strategy.md) (helper contracts being re-tested at unit level — byte-unchanged across F-250)

---

## Context

`tests/scripts/test_init_sh_*.py` is the integration test suite that drives `scripts/init.sh` end-to-end inside isolated `tmp_path` clones. It was authored under F-248 (BLP-02 Wave 1, ADR-038, delivered 2026-05-04 squash commit `6db9a25`) to assert metachar literal-preservation, prompt-boundary input rejection, and closed-contract residual-scan invariants on the placeholder substitution surface.

Two recurring CI-stability issues surfaced after F-248's first closing run (`25314246672`):

1. **macos-latest cold-cache subprocess timeouts** — five module-scoped fixtures each invoked `init.sh` in a separate clone. `init.sh` performs ~50 file substitutions across the personalized template tree, plus a recursive copy of the source repo into the `tmp_path` clone. On macos-latest CI runners (3-4× slower than dev hardware on cold-cache filesystem scans), each invocation cost 300-560s. Five module-scoped invocations multiplied this to ~25 minutes of init.sh work per CI run, dominating the wall time and frequently breaching the existing pytest-timeout caps (300s subprocess inner / 360s pytest outer). The macos leg of CI on the F-248 closing run hit 30-40 min wall time on the init.sh suite alone.

2. **Baseline file-set drift on every PR** — `test_personalized_tree_bytes_match_baseline` in `test_init_sh_substitution.py` enforced strict equality `set(generated) == set(baseline)` between the post-init tree and the recorded golden tree under `tests/fixtures/init-baseline-tree/`. The baseline scope was the WHOLE repo (~600 files). Every PR that added, renamed, or removed any file in the repo — documentation, specs, or generated artifacts — required regenerating the baseline before CI could turn green. This created a recurring "regenerate baseline on every PR" maintenance tax that did not correspond to genuine substitution-correctness regressions.

Both issues were absent from the F-248 plan (ADR-038 §Open Questions notes "None at proposal time") because the cold-cache cost and baseline drift only became measurable after the suite had been merged onto `main` and run repeatedly across PRs.

F-250 was originally scoped as a surgical hot-fix to extract 12 adversarial cases from `init.sh` integration tests to unit-level pytest modules (FR-001..FR-018). Phase 6 Option Z was a maintainer-authorized mid-build scope expansion to address the two issues at the **root cause** rather than as quick patches.

### Constraints

- **Atomic-PR delivery** (F-250 TC-3) — original hot-fix scope (unit extraction) and Phase 6 expansion (test architecture) ship as ONE squash-merge.
- **Helper byte-unchanged invariant** — `.aod/scripts/bash/template-substitute.sh` and `.aod/scripts/bash/init-input.sh` MUST be `git diff main` empty post-merge (FR-019/FR-020 — preserved from F-250 original scope, confirmed at T029).
- **Backward compatibility with existing test contracts** — every consuming test file historically held its own module-scoped `init_run` fixture; the promotion MUST preserve every existing assertion semantics.
- **macOS bash 3.2.57 + Linux bash 5.x compatibility** (NFR-001) — fixture promotion is Python-side; helpers stay bash-3.2 compatible.
- **CI matrix unchanged** — `[macos-latest, ubuntu-latest]` per `.github/workflows/tachi-pytest.yml` paths-filter triggering.

---

## Decisions

### D-1 — Promote `init_run` fixture to session scope in `tests/scripts/conftest.py`

**Replace** the four module-scoped `init_run` fixtures (in `test_init_sh_substitution.py`, `test_init_sh_constitution.py`, `test_init_sh_self_delete.py`, plus the function-scoped pattern in `test_init_sh_adversarial.test_no_residual_placeholders_after_init`) with ONE session-scoped fixture in `tests/scripts/conftest.py`.

The session-scoped fixture invokes `scripts/init.sh` ONCE in a `tmp_path_factory.mktemp("init_sh_canonical")` clone for the entire test session. Every consuming test asserts read-only properties (file existence, byte content, mode bits) of the post-init clone.

**Carve-out**: `test_case_13_file_level_byte_identity` in `test_init_sh_adversarial.py` keeps its function-scoped pattern. That test SEEDS pre-init fixture files into the clone before `init.sh` runs to validate file-level byte-identity for adversarial payloads — it cannot share the canonical clone because the canonical clone has no pre-seeded fixtures.

**Rationale**:
- Sharing is safe by construction: every consuming test asserts read-only properties; no consumer mutates the post-init clone.
- macos cold-cache cost drops from 5 invocations × ~300-560s = ~25 min to 2 invocations (one canonical + one pre-seeded for case 13) = ~10-20 min — fits under the 15 min target with the bumped timeout.
- `tmp_path_factory.mktemp` produces a session-scoped tmpdir; pytest cleans up at session end. No filesystem leakage.
- `pytest-xdist` workers (if introduced later) preserve isolation: each worker has its own session and runs its own canonical fixture once. Sharing within a worker is the intended scope of the optimization.

**Trade-off** (D-2 below):
- Session-scoped state is harder to reason about for reviewers familiar with module-scoped patterns. The fixture docstring documents the read-only contract explicitly to mitigate.

### D-2 — Document the read-only sharing contract in the fixture docstring

**Require** the session-scoped `init_run` fixture's docstring to explicitly call out:
- The single-canonical-invocation invariant
- The read-only consumer contract
- The function-scoped carve-out for tests that pre-seed fixture files
- The reason sharing is safe (no consumer mutates state)

**Rationale**:
- Session-scoped fixtures are an unusual pattern in this codebase. The fixture's contract MUST survive code review and refactor without losing the rationale.
- Future contributors must NOT add a consumer that mutates the post-init clone — the docstring is the warning sign.

### D-3 — Convert baseline file-set check from strict equality to asymmetric

**Replace** `assert set(generated) == set(baseline)` in `test_personalized_tree_bytes_match_baseline` with an asymmetric check:
- `assert baseline ⊆ generated` — drops are FAIL (substitution regression: init.sh missed a file the baseline expects)
- `generated ⊋ baseline` is TOLERATED — additions are accepted (repo growth between deliberate baseline regenerations is not a regression)
- Byte-identity check on every baseline file remains strict (any byte mismatch on a baseline file IS a regression)

**Rationale**:
- The strict-equality form fired on TWO independent failure modes: genuine substitution regression (drops) AND benign repo growth (additions). Only the former is a real test failure.
- Asymmetry preserves the substitution-correctness invariant while eliminating the "regenerate on every doc edit" maintenance tax.
- Test failure messages now say `substitution regression: baseline files missing from post-init tree: [...]` — actionable and specific.

### D-4 — Restrict baseline scope to substitution-target files (~53 files vs ~600 files)

**Refactor** `tests/fixtures/regenerate-baseline.sh` to capture ONLY files that contain canonical `{{KEY}}` placeholders pre-substitution. The baseline drops from ~600 files (whole repo) to ~53 files (substitution targets only). Documentation, specs, generated artifacts, and other non-personalized files now drift freely between deliberate baseline regenerations.

**Rationale**:
- The substitution-correctness invariant only applies to files with canonical placeholders. Files without placeholders are not in scope for the test's contract.
- Combined with D-3 asymmetry, this completely eliminates the recurring baseline-drift maintenance tax: only genuine substitution-target edits warrant a baseline regeneration.

### D-5 — Bump subprocess + pytest timeout pair (300s/360s → 900s/1080s)

**Bump** `tests/scripts/init_sh_helpers.run_init_in_clone(timeout_sec=)` default from 300s to 900s. **Bump** `.github/workflows/tachi-pytest.yml` `pytest --timeout` from 360s to 1080s.

**Rationale**:
- Macos-latest cold-cache projects to ~560-700s at 4× dev-hardware multiplier (140-175s observed on dev). 900s leaves ~200s headroom on the worst observed scenario.
- Outer pytest cap (1080s) is always ≥120s greater than inner subprocess cap (900s) so the inner timeout fires first with diagnostic stderr/stdout output. Without this gap, the outer pytest cap would terminate the process before the inner subprocess could emit diagnostic information.
- The pair MUST be raised together; raising only one creates an asymmetric failure mode where one cap pre-empts the other and obscures diagnostics.

**Trade-off**:
- A genuinely-stuck `init.sh` will now hang for up to 900s before failing. This is acceptable because: (a) the worst observed real run is ~700s on macos cold-cache; (b) pytest's `--durations=0` flag surfaces slow tests at the end of every run; (c) the fixture is session-scoped, so the cost is paid ONCE per session, not per consumer.

### D-6 — Workflow `paths:` filter and `pytest` invocation completeness for all 3 new unit modules

**Update** `.github/workflows/tachi-pytest.yml` to:
- (a) Add `tests/scripts/test_template_substitute_unit.py`, `tests/scripts/test_init_input_unit.py`, and `tests/scripts/test_substitute_shim_canary.py` to the `paths:` filter so PRs touching them trigger CI.
- (b) Add the same 3 modules to the `pytest` invocation so they actually RUN when triggered.

**Rationale**:
- During F-250 build, the 3 new unit modules were created but the workflow's `paths:` filter and `pytest` invocation were not updated — they wouldn't trigger CI on PRs that touched them, and wouldn't run even when triggered. Both gaps must be closed.
- The `paths:` filter and `pytest` invocation are independent surfaces; both must enumerate every test module that should participate in CI for the suite to be complete.
- Sub-second per-case unit tests sit comfortably within the bumped 1080s outer pytest cap; no further timeout consideration needed.

### D-7 — Helper byte-unchanged invariant preserved (FR-019/FR-020)

**Maintain** the F-250 original-scope invariant: `.aod/scripts/bash/template-substitute.sh` and `.aod/scripts/bash/init-input.sh` MUST be `git diff main` empty post-merge.

**Rationale**:
- Phase 6 Option Z is a TEST-tree-only architectural change. The bash helpers under test are byte-unchanged; the helper contracts defined by ADR-038 are preserved verbatim.
- Verified at F-250 T029: `git diff main -- .aod/scripts/bash/` empty.
- This invariant is what makes Phase 6 Option Z safe to ship as part of F-250's atomic PR rather than as a separate ADR-038 amendment — the helpers' behavioral contract is unchanged.

---

## Consequences

### Positive

- **macos-latest CI wall time**: 5m 19s on F-250 closing run (≤15 min target met per SC-002). Down from 30-40 min cold-cache band on F-248 closing run `25314246672`.
- **init.sh CI invocations dropped**: 17 → 5 per CI run (FR-014 / SC-003) via session-scoped fixture promotion. The remaining 5 invocations are: 1 canonical (shared across all read-only consumers) + 1 for `test_case_13` (function-scoped, pre-seeds fixtures) + 3 for tests that intentionally need fresh init runs.
- **Baseline maintenance tax eliminated**: doc edits, spec additions, and generated-artifact regenerations no longer trigger baseline-failure. Only genuine substitution-target file changes warrant a `regenerate-baseline.sh` run.
- **Pattern reusability**: the session-scoped fixture pattern and asymmetric baseline pattern are now documented at `docs/architecture/03_patterns/README.md` for reuse on future heavyweight-fixture or baseline-replay test scenarios.
- **Test failure diagnostics improved**: asymmetric check failure messages explicitly say "substitution regression: baseline files missing from post-init tree" — reviewers see the failure mode immediately.

### Negative

- **Session-scoped state is harder to reason about** — reviewers unfamiliar with the pattern may misread the test code as having shared mutable state. Mitigated by D-2 (explicit docstring contract).
- **Asymmetric baseline weakens "exhaustive" coverage** — a deliberate file rename within the substitution-target set (e.g., renaming a `.claude/rules/governance.md` → `governance-v2.md`) would slip past the asymmetric check unless paired with a baseline regeneration. This is acceptable because: (a) such renames are rare and intentional; (b) they require explicit code review attention; (c) the paired regenerate-baseline.sh script is the documented remediation.

### Neutral / Migration

- **Existing F-248 helper contracts (ADR-038)**: byte-unchanged. ADR-038 is NOT amended.
- **Tests that pre-seed fixtures (function-scoped carve-out)**: `test_case_13_file_level_byte_identity` retains its function-scoped invocation. Future tests with similar pre-seeding requirements should follow the same pattern (function-scoped, NOT session-scoped).
- **CI matrix unchanged**: `[macos-latest, ubuntu-latest]` per `.github/workflows/tachi-pytest.yml` — only timeouts and module enumeration changed.
- **Helper module API unchanged**: `init_sh_helpers.run_init_in_clone(...)` signature is byte-identical; only the default `timeout_sec=` value changed (300 → 900). Consumers that override the timeout explicitly are unaffected.

### Test Coverage

| Test concern | Test file | Scope |
|---|---|---|
| Adversarial substitution semantics (Cases 1-8) | `test_template_substitute_unit.py` | Unit (subprocess invocation of bash helper) |
| Adversarial input rejection (Cases 9-12 + canary) | `test_init_input_unit.py` | Unit (subprocess + process substitution `< <(printf ...)`) |
| `shopt -u patsub_replacement` shim presence canary (TC-1 closure) | `test_substitute_shim_canary.py` | Unit (asserts shim line is present in `template-substitute.sh`) |
| File-level byte-identity (Case 13) | `test_init_sh_adversarial.py::test_case_13_file_level_byte_identity` | Integration (function-scoped, pre-seeds fixtures) |
| Closed-contract residual scan | `test_init_sh_adversarial.py::test_no_residual_placeholders_after_init` | Integration (consumes session-scoped `init_run`) |
| Personalized-tree byte-identity vs baseline | `test_init_sh_substitution.py::test_personalized_tree_bytes_match_baseline` | Integration (consumes session-scoped `init_run`, asymmetric file-set check) |
| Constitution byte-equality | `test_init_sh_constitution.py` | Integration (consumes session-scoped `init_run`) |
| Self-delete + snapshot persistence | `test_init_sh_self_delete.py` | Integration (consumes session-scoped `init_run`) |

### KPI Outcomes

| KPI | Target | F-250 Closing Run Result |
|---|---|---|
| macos-latest wall time (SC-002) | ≤15 min | 5m 19s ✓ |
| `init.sh` invocations per CI run (SC-003) | ≤5 | 5 (down from 17) ✓ |
| CI savings vs F-248 baseline `25314246672` (SC-005) | ≥25 min | ~25-35 min savings ✓ |
| Helper byte-unchanged invariant (FR-019/FR-020) | `git diff main` empty | Empty ✓ |

---

## Promotion Notes

This ADR was authored, proposed, and accepted same-day (2026-05-04) during F-250's atomic-PR squash-merge (PR #253, commit `75866d9`). The Proposed → Accepted dual-commit governance pattern (ADR-027/028/029/030/031/032/033/034/035/036/037/038 lineage) is NOT applied here because:

1. The decisions are TEST-tree-only and do not introduce new product behavior, schema changes, agent surfaces, or runtime invariants. The downstream consumer set is the test suite itself.
2. The decisions were validated empirically on the F-250 closing CI run (5m 19s wall time, 5 init.sh invocations) before merge — no separate Wave 5 architect-promotion gate was needed.
3. The atomic-PR ordering (TC-3) requires ALL F-250 changes to ship as one squash-merge; splitting the ADR into Proposed-then-Accepted commits would violate atomic ordering.

Future test-architecture decisions of similar scope (test-tree-only, no product surface impact) MAY follow this same single-commit accept pattern. Decisions that affect helper contracts, schemas, or agent surfaces MUST follow the dual-commit Proposed → Accepted pattern established by the ADR-027+ lineage.

---

## Open Questions

None at acceptance time. The pattern is now documented at `docs/architecture/03_patterns/README.md#pattern-session-scoped-init-sh-fixture` and `#pattern-asymmetric-baseline-file-set-check` for reuse on future heavyweight-fixture or baseline-replay test scenarios.
