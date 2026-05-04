# Environment Variables Contract

**Last Updated**: 2026-05-04
**Owner**: DevOps Agent
**Scope**: Test-only and CI-only environment variables for the upstream template repository.

---

## Overview

This document is the single source of truth for environment variables read by template scripts, CI workflows, and test harnesses. Variables fall into two categories:

1. **Adopter-facing** — variables that adopter projects set in their own environments (e.g., `AOD_BOOTSTRAP_*`, `CI`, `FORCE_RETAG`). These are documented in `CI_CD_GUIDE.md` → "Update-Script Environment Variables" and in `docs/guides/DOWNSTREAM_UPDATE.md`.
2. **Test-only / CI-only** — variables used solely by the test harness or CI workflows for determinism, fixture regeneration, or matrix isolation. These are NOT public contract and NOT for production use. Documented below.

---

## F-248 Substitution-Surface Test Variables

**Added in Feature 248** (Substitution Surface Hardening, PR #249, merged 2026-05-04).

These variables are read by `scripts/init.sh` and used by the F-248 test suite (`tests/scripts/test_init_sh_*.py`) and the baseline-regeneration script (`tests/fixtures/regenerate-baseline.sh`) to make placeholder substitution byte-deterministic. They are **test-only** — production invocations of `scripts/init.sh` MUST NOT set them.

| Variable | Default | Read at | Purpose |
|----------|---------|---------|---------|
| `AOD_RATIFICATION_DATE_OVERRIDE` | unset (falls back to `date +%Y-%m-%d`) | `scripts/init.sh:123` | Pins the constitution `RATIFICATION_DATE` substitution to a fixed value (typically `2026-05-04` for the F-248 baseline). Used by `tests/fixtures/regenerate-baseline.sh` and by the F-248 pytest fixtures to produce byte-deterministic init output. NOT for production use — production invocations rely on the `date(1)` fallback. |
| `AOD_CURRENT_DATE_OVERRIDE` | unset (falls back to `date +%Y-%m-%d`) | `scripts/init.sh:124` | Pins the `CURRENT_DATE` substitution to a fixed value. Same pattern and constraints as `AOD_RATIFICATION_DATE_OVERRIDE`. NOT for production use. |

**Why two variables, not one**: `RATIFICATION_DATE` and `CURRENT_DATE` are semantically distinct — the constitution's ratification date is a one-time historical record, while `CURRENT_DATE` is a refresh-on-init timestamp. Bundling them into a single override would conflate the two and risk masking a regression where one is updated and the other is not. The two-variable contract is intentional and stable.

**Production invariant**: `scripts/init.sh` MUST NOT change the fallback expression (`$(date +%Y-%m-%d)`) without a coordinated update to `regenerate-baseline.sh`. Any change to the date-formatting string would invalidate the entire `tests/fixtures/init-baseline-tree/` snapshot.

**Local test invocation** (matches `tests/fixtures/regenerate-baseline.sh:95-96`):

```bash
AOD_RATIFICATION_DATE_OVERRIDE=2026-05-04 \
  AOD_CURRENT_DATE_OVERRIDE=2026-05-04 \
  bash scripts/init.sh
```

**CI invocation**: the F-248 pytest workflow (`tachi-pytest.yml`) does NOT set these variables directly — instead, the test fixtures invoke them via `tests/scripts/init_sh_helpers.run_init_in_clone()` after the helper sets a deterministic value. This isolates per-test determinism from cross-test fixture coupling.

**Reference**: `docs/architecture/02_ADRs/ADR-038-placeholder-substitution-strategy.md` §Test Coverage. Spec FR-001..FR-011 in `specs/248-substitution-surface-hardening/spec.md`.

---

## F-129 Update-Script Test-Harness Overrides

**Added in Feature 129** (Downstream Template Update Mechanism).

These variables are read by `scripts/check-manifest-coverage.sh`, `scripts/check-extract-coverage.sh`, and the BATS test harness for fixture isolation. They are **NOT public contract** — adopter projects MUST NOT rely on them, and they may change without notice between template releases.

| Variable | Default | Purpose |
|----------|---------|---------|
| `AOD_COVERAGE_FILES` | derived from `git ls-files` | BATS-only override for the file list passed to coverage validators. Newline-separated. |
| `AOD_COVERAGE_MANIFEST` | `<repo_root>/.aod/template-manifest.txt` | BATS-only override for the manifest path under test. |
| `AOD_COVERAGE_REPO_ROOT` | `git rev-parse --show-toplevel` | BATS-only override for the repo-root path. |
| `AOD_COVERAGE_EXTRACT_SH` | `<repo_root>/scripts/extract.sh` | BATS-only override for the `extract.sh` path under test (F128). |
| `AOD_COVERAGE_EXTRACTIGNORE` | `<repo_root>/.extractignore` | BATS-only override for the `.extractignore` path under test (F128). |

**Why these exist**: the BATS suite (`tests/integration/*.bats`) needs to construct synthetic fixtures under `$BATS_TEST_TMPDIR` and run the validators against them, not against the live repo. The override variables let each test build a self-contained fixture graph without touching production paths.

---

## Cross-References

- **Adopter-facing update env vars**: `docs/devops/CI_CD_GUIDE.md` → "Update-Script Environment Variables" (`CI`, `FORCE_RETAG`, `AOD_UPDATE_TMP_DIR`, `AOD_BOOTSTRAP_*`, `AOD_UPSTREAM_URL`, `YES`, `SKIP_MARKER`).
- **Adopter scaffold env vars (Playwright E2E)**: `docs/devops/CI_CD_GUIDE.md` → "Playwright E2E in Adopter CI (FastAPI Stack Packs)" (`TEST_DATABASE_URL`, `TEST_SECRET_KEY`, `BACKEND_TEST_PORT`, `FRONTEND_TEST_PORT`).
- **`/aod.deliver` exit codes** (consumed by CI scripts, not env vars): `docs/devops/CI_CD_GUIDE.md` → "/aod.deliver Exit-Code Contract".

---

**Maintained By**: DevOps Agent
