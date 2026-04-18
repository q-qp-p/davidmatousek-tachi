# CI/CD Setup Guide - tachi

**Last Updated**: 2026-04-18 (Feature 194: F-B Coverage Attestation Report Section)
**Owner**: DevOps Agent

---

## Overview

This guide provides instructions for setting up CI/CD pipelines for tachi. Choose your platform based on your hosting provider.

---

## When to Add CI/CD

Add CI/CD after you have:
1. ✅ Working local development environment
2. ✅ At least one deployable feature
3. ✅ Basic test coverage (unit tests)
4. ✅ Defined deployment environments

---

## Platform Guides

### GitHub Actions (Recommended)

**Best For**: Projects hosted on GitHub, flexible workflow needs

**Setup**:
1. Create `.github/workflows/ci.yml`
2. Define workflow stages (lint, test, build, deploy)
3. Add repository secrets (Settings → Secrets and Variables)

**Example Workflow**:
```yaml
name: CI/CD

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'
      - run: npm ci
      - run: npm test
      - run: npm run lint

  deploy-staging:
    needs: test
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Deploy to Staging
        run: |
          # Platform-specific deployment command
          echo "Deploy to staging"

  deploy-production:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/checkout@v3
      - name: Deploy to Production
        run: |
          # Platform-specific deployment command
          echo "Deploy to production"
```

**Resources**:
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Workflow Syntax](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)

---

### Vercel (Frontend/Full-Stack)

**Best For**: Next.js, React, Vue, static sites

**Setup**:
1. Install Vercel CLI: `npm i -g vercel`
2. Link project: `vercel link`
3. Configure via `vercel.json` or dashboard

**Auto-Deploy**:
- **PR**: Auto-deploys to preview URL
- **main branch**: Auto-deploys to production

**Configuration** (`vercel.json`):
```json
{
  "buildCommand": "npm run build",
  "devCommand": "npm run dev",
  "installCommand": "npm install",
  "framework": "{{FRAMEWORK}}",
  "regions": ["{{REGION}}"]
}
```

**Resources**:
- [Vercel Documentation](https://vercel.com/docs)
- [GitHub Integration](https://vercel.com/docs/concepts/git/vercel-for-github)

---

### Railway (Backend/Full-Stack)

**Best For**: Node.js, Python, Go, Docker-based apps

**Setup**:
1. Connect GitHub repository
2. Configure build command
3. Set environment variables
4. Define services in `railway.toml`

**Resources**:
- [Railway Documentation](https://docs.railway.app/)

---

### GitLab CI

**Best For**: Projects on GitLab

**Setup**: Create `.gitlab-ci.yml`

**Example**:
```yaml
stages:
  - test
  - deploy

test:
  stage: test
  script:
    - npm ci
    - npm test

deploy:
  stage: deploy
  script:
    - echo "Deploy to production"
  only:
    - main
```

---

### tachi Threat Model (GitHub Actions Adapter)

**Best For**: Automated threat analysis on PRs that modify architecture files

Feature 021 introduced a ready-to-use GitHub Actions workflow at `adapters/github-actions/tachi-threat-model.yml`. This is not a general CI/CD pipeline -- it is a specialized security workflow that runs tachi's 14 threat agents against architecture changes and uploads findings to GitHub Code Scanning.

**Installation**:
```bash
cp adapters/github-actions/tachi-threat-model.yml .github/workflows/
```

**Required Secret**:
| Secret | Description |
|--------|-------------|
| `LLM_API_KEY` | Anthropic API key (set in Settings > Secrets and variables > Actions) |

**Prerequisites**:
- GitHub Actions enabled on the repository
- GitHub Advanced Security enabled (for SARIF upload to Code Scanning)

**Triggers**:
- **Automatic**: Pull requests modifying `docs/architecture/**`, `*.mermaid`, `*.puml`, or `*.drawio`
- **Manual**: Workflow dispatch from the Actions tab with configurable inputs (architecture path, LLM model, max tokens)

**Outputs**:
- `threats.md` -- Full threat model analysis in markdown (downloadable artifact)
- `threats.sarif` -- SARIF 2.1.0 findings uploaded to Code Scanning (visible in Security > Code scanning alerts)

**How It Differs from Standard CI/CD**:
Unlike linting or test workflows, this adapter invokes an LLM API at runtime. It reads source agent prompts from `agents/` and sends architecture input to the Anthropic API. No agent files are copied or transformed -- the orchestrator prompt is used as the system instruction.

**Error Handling**: The workflow handles missing API keys, authentication failures, rate limiting (with retry), timeouts, and empty responses with actionable error messages.

**Full Documentation**: See `adapters/github-actions/README.md` for detailed configuration, permissions, SARIF deduplication, and verification steps.

---

### Automated Release Tagging (release-please)

**Best For**: Automated version tagging, GitHub Release creation, and CHANGELOG generation from conventional commits

Feature 086 introduced a release-please workflow that automates version management. When commits land on `main`, release-please analyzes conventional commit messages and either opens a release PR (bumping the version and updating CHANGELOG.md) or updates an existing release PR with new changes. Merging the release PR creates a GitHub Release with a Git tag.

**Workflow File**: `.github/workflows/release-please.yml`

**Trigger**: Push to `main` branch

**Configuration Files**:
| File | Purpose |
|------|---------|
| `release-please-config.json` | Release type (`simple`), changelog section mapping |
| `.release-please-manifest.json` | Current version baseline (started at `4.0.0`) |

**Changelog Sections** (mapped from conventional commit types):
| Commit Type | Section |
|-------------|---------|
| `feat` | Features |
| `fix` | Bug Fixes |
| `docs` | Documentation |
| `chore`, `refactor`, `perf`, `test`, `style` | Miscellaneous |

**How It Works**:
1. Developer merges a feature branch to `main` (via PR)
2. release-please workflow runs automatically on push
3. If releasable commits exist, release-please opens (or updates) a release PR titled `chore(main): release X.Y.Z`
4. The release PR contains the updated `CHANGELOG.md` and version bump
5. When the release PR is merged, release-please creates a GitHub Release with the corresponding Git tag

**Required Permissions**:
```yaml
permissions:
  contents: write        # Create tags and releases
  pull-requests: write   # Open and update release PRs
```

**No Repository Secrets Required**: release-please uses the default `GITHUB_TOKEN` provided by GitHub Actions.

**Infrastructure Impact**: No new environment variables, Docker services, or deployment changes. The workflow runs entirely within GitHub Actions.

---

### tachi mmdc Preflight Gate (Feature 130)

**Best For**: Proving the `/tachi.security-report` preflight gate fires on a fresh runner where `@mermaid-js/mermaid-cli` (`mmdc`) is intentionally absent

Feature 130 introduced a CI acceptance test at `.github/workflows/tachi-mmdc-preflight.yml` that verifies the fail-loud preflight behavior documented in [ADR-022](../architecture/02_ADRs/ADR-022-mmdc-hard-prerequisite.md). This workflow is **not** a general CI/CD pipeline — it is a single-job verification that the pipeline aborts non-zero when `mmdc` is absent and the scanned project contains attack trees.

**Workflow File**: `.github/workflows/tachi-mmdc-preflight.yml`

**Trigger**: Pull requests touching any of:
- `scripts/extract-report-data.py` (the preflight gate lives in `render_mermaid_to_png()`)
- `templates/tachi/security-report/attack-path.typ` (Typst template that consumes rendered diagrams)
- `scripts/install.sh` (courtesy warning signal)
- `.claude/commands/tachi.security-report.md` (shell preflight gate)
- `README.md` (Prerequisites section)
- `.github/workflows/tachi-mmdc-preflight.yml` (the workflow itself)

**Runner**: `ubuntu-latest` — intentionally chosen because `ubuntu-latest` ships WITHOUT `@mermaid-js/mermaid-cli` preinstalled (plan.md spike S3 confirmed). Do NOT switch to a custom image that preinstalls mmdc — that would silently validate the happy path instead of the loud-failure path this workflow is designed to exercise.

**Job Steps**:

1. **Checkout repository** — standard `actions/checkout@v4`
2. **Set up Python 3.11** — via `actions/setup-python@v5`
3. **Set up Typst** — via `typst-community/setup-typst@v5` (must NOT transitively install Node.js tooling; see T4 enforcement below)
4. **Diagnostic: show mmdc absence** — logs `which mmdc` output for human-readable CI debugging (architect refinement R3)
5. **Enforce mmdc absence (T4 / plan Risk #6)** — **team-lead T4 enforcement assertion** that fails the job if `command -v mmdc` unexpectedly succeeds. This guards against future upstream changes (e.g., a new major version of `typst-community/setup-typst` that transitively installs Node.js tooling, or a GitHub Actions runner image update that adds mmdc to the base image) that would silently break the spike-S3 assumption and make the subsequent preflight test meaningless. If this assertion ever fires, treat it as a high-severity CI infrastructure bug — do NOT install mmdc to "fix" it.
6. **Run `scripts/extract-report-data.py` against `examples/mermaid-agentic-app/`** — direct Python invocation with `--target-dir`, `--output`, `--template-dir`. Slash commands cannot run in CI, so we invoke the script directly. The `mermaid-agentic-app` example contains an `attack-trees/` directory with Critical/High findings, so the preflight gate is expected to fire. Expected exit code: non-zero. Captures stdout+stderr to `/tmp/out.txt` for the next step.
7. **Assert canonical error tokens present** — greps `/tmp/out.txt` for three tokens from the canonical preflight `RuntimeError` message:
   - `@mermaid-js/mermaid-cli`
   - `npm install -g @mermaid-js/mermaid-cli`
   - `Attack path rendering`

   Each token is grepped individually so a missing token produces a specific, actionable error message. This is the **seven-location canonical command consistency** guarantee — the install command appears in exactly 7 enforcement locations across the codebase (extract-report-data.py raise, tachi.security-report.md shell echo, install.sh warning, README Prerequisites, test_mmdc_preflight.py assertion, tachi-mmdc-preflight.yml grep, ADR-022 decision body). Any drift fails this workflow.

**Required Permissions**:
```yaml
permissions:
  contents: read
```

**No Repository Secrets Required**: The workflow uses only `GITHUB_TOKEN` implicitly via `actions/checkout@v4`.

**What This Workflow Does NOT Test**:
- Happy-path rendering when `mmdc` IS installed (covered by local pytest suite `tests/scripts/test_mmdc_preflight.py` and backward-compatibility baselines in `tests/scripts/test_backward_compatibility.py`)
- Non-attack-tree projects (covered by the backward-compatibility baseline suite — 5 example PDFs remain byte-identical without mmdc required)
- Mid-render failures (covered by the 5 aggregator tests in `tests/scripts/test_mmdc_preflight.py`)

**Infrastructure Impact**: No new environment variables, Docker services, or deployment changes. The workflow runs entirely within GitHub Actions using the standard `ubuntu-latest` image.

**Related Files**:
- `scripts/extract-report-data.py` (`render_mermaid_to_png()` is the enforcement point)
- `tests/scripts/test_mmdc_preflight.py` (9 local tests: 4 preflight + 5 mid-render aggregator)
- [ADR-022](../architecture/02_ADRs/ADR-022-mmdc-hard-prerequisite.md) (hard-prerequisite posture decision)
- `specs/130-prd-130-fix/plan.md` (spike S3 and Risk #6 mitigation)

---

### Python Test Harness (pytest)

**Best For**: Validating deterministic data extraction scripts, command dispatch, PDF page positioning, and golden-file comparisons for tachi pipeline outputs

Feature 128 introduced the first pytest-based test infrastructure for tachi. The harness lives at the repo root and is runnable locally today.

**Project Configuration**: `pyproject.toml` (repo root)
```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_functions = ["test_*"]
addopts = "-ra --strict-markers"
```

**Development Dependencies**: `requirements-dev.txt`
- `pytest>=8.0`
- `pytest-cov>=4.1`

**Test Layout**:
```
tests/
├── conftest.py
├── schemas/
│   └── test_taxonomy_integrity.py       # Feature 180 (F-A1): FR-027..FR-032 integrity tests
└── scripts/
    ├── test_smoke.py
    ├── test_attack_chain_extraction.py
    ├── test_attack_chains.py
    ├── test_backward_compatibility.py
    ├── test_command_dispatch.py
    ├── test_coverage_attestation.py
    ├── test_coverage_attestation_pagination.py
    ├── test_extract_infographic_data.py
    ├── test_extract_report_data.py
    ├── test_finding_pattern_parser.py
    ├── test_mmdc_preflight.py
    ├── test_pattern_classification_rules.py
    ├── test_pattern_extraction.py
    ├── test_pattern_synthesis.py
    ├── test_pdf_page_positioning.py
    ├── test_source_attribution.py
    ├── generate_pagination_fixture.py
    └── fixtures/
        ├── coverage_attestation/
        ├── exec_arch/
        ├── finding_pattern_parser/
        ├── pattern_extraction/
        ├── pattern_synthesis/
        ├── report_data/
        └── golden/
```

**Local Execution**: See `docs/devops/01_Local/README.md` section "Python Test Suite" for full local setup and run commands.

**Current CI Status (as of F-194 close, 2026-04-18)**:
- Pytest harness **exists locally** and runs against 305 passed + 1 skipped under `SOURCE_DATE_EPOCH=1700000000` across 17 modules (the 1 skip is the `mermaid-agentic-app` known-limitation carry-over, unrelated to F-194)
- Feature 194 added 2 new test modules (`tests/scripts/test_coverage_attestation.py` — aggregator unit tests over per-finding rows, per-framework aggregates, Covered / Partial / Gap classification, zero-denominator `N/A` handling, partition invariant; `tests/scripts/test_coverage_attestation_pagination.py` — pagination smoke on a synthetic 100-finding × 5-framework fixture) plus 1 committed fixture generator (`tests/scripts/generate_pagination_fixture.py`). **Zero new runtime dependencies** (empty diff on `pyproject.toml` / `requirements*.txt` / `package.json`), **zero new CI workflow**, zero changes to existing workflows, zero changes to `scripts/install.sh`. A post-merge follow-up deferred `import yaml` inside `_load_framework_yaml_records` in `scripts/extract-report-data.py` to preserve the stdlib-only module-load invariant for runtime scripts — `pyyaml` remains dev-only per Feature 128 precedent. Runs under the existing `pytest tests/` invocation. SC-002 byte-identity on the 5 non-agentic baselines under `SOURCE_DATE_EPOCH=1700000000` (ADR-021) continues unchanged
- Feature 189 added 1 new test module (`tests/scripts/test_source_attribution.py`) containing 9 tests covering `schemas/finding.yaml` 1.4 -> 1.5 minor-bump additive `source_attribution` field — two-tier validation (parser-tier V1/V2/V3 closed-enum + validator-tier V4 referential integrity against `schemas/taxonomy/`), round-trip fidelity, and absent-vs-empty-array semantics. No new runtime dependencies (empty diff on `pyproject.toml` / `requirements-dev.txt` / `package.json`), no new CI workflow, no changes to existing workflows, no changes to `scripts/install.sh`. Runs under the existing `pytest tests/` invocation
- Feature 180 added 1 new test module under a new top-level directory (`tests/schemas/test_taxonomy_integrity.py`) containing 4+1 integrity tests (FR-027..FR-032) covering taxonomy schema validation, crosswalk referential integrity, bidirectional consistency, cycle detection, uniqueness, and a performance guard -- no new runtime dependencies, no new CI workflow. Runs under the existing `pytest tests/` invocation
- Feature 142 added 4 new test modules (`test_pattern_synthesis.py`, `test_pattern_classification_rules.py`, `test_pattern_extraction.py`, `test_finding_pattern_parser.py`) covering MAESTRO Phase 3 agentic threat pattern expansion -- no new CI workflow
- Feature 141 added 2 new test modules (`test_attack_chains.py`, `test_attack_chain_extraction.py`) -- no new CI workflow
- Pytest is **not yet wired** into any GitHub Actions workflow
- Existing workflows (`release-please.yml`, `tachi.threat-model.yml`, `tachi-mmdc-preflight.yml`) are unchanged by F-194, F-189, and F-180
- Wiring pytest to CI is tracked as a follow-up

**Recommended Follow-Up Pipeline Step** (not implemented):
When a future feature adds a CI workflow for Python tests, use the snippet in "Essential CI/CD Components -> Unit Tests" above. The workflow should install `requirements-dev.txt`, run `pytest tests/`, and surface coverage via `pytest-cov` on pull requests that modify `scripts/`, `tests/`, `pyproject.toml`, or `requirements-dev.txt`.

**Infrastructure Impact**: No new runtime dependencies, no new environment variables, no new Docker services. Development dependencies are isolated to `requirements-dev.txt` and only installed when running tests.

---

## Essential CI/CD Components

### 1. Linting
```yaml
- name: Lint Code
  run: npm run lint
```

### 2. Type Checking
```yaml
- name: Type Check
  run: npm run typecheck
```

### 3. Unit Tests
```yaml
- name: Run Tests
  run: npm test
```

For tachi's Python test suite (introduced in Feature 128), a pytest step looks like:
```yaml
- name: Setup Python
  uses: actions/setup-python@v5
  with:
    python-version: '3.11'
- name: Install dev dependencies
  run: pip install -r requirements-dev.txt
- name: Run pytest
  run: pytest tests/
```

### 4. Build Verification
```yaml
- name: Build Application
  run: npm run build
```

### 5. Security Scanning
```yaml
- name: Security Audit
  run: npm audit --audit-level=moderate
```

---

## Environment Variables in CI/CD

### GitHub Actions
```yaml
env:
  DATABASE_URL: ${{ secrets.DATABASE_URL }}
  API_KEY: ${{ secrets.API_KEY }}
  LLM_API_KEY: ${{ secrets.LLM_API_KEY }}  # Required by tachi-threat-model workflow
```

### Vercel
- Set via dashboard: Settings → Environment Variables
- Or CLI: `vercel env add VARIABLE_NAME`

---

## Best Practices

### DO ✅
- Run tests before deployment
- Use separate workflows for staging and production
- Cache dependencies to speed up builds
- Set up failure notifications
- Use environment-specific secrets

### DON'T ❌
- Commit secrets to repository
- Skip tests in CI
- Deploy directly to production without staging
- Ignore failed builds
- Use hardcoded credentials

---

## Monitoring CI/CD

Track these metrics:
- **Build Time**: Target <5 minutes
- **Success Rate**: Target >95%
- **Deployment Frequency**: Measure velocity
- **Mean Time to Recovery**: Track incident response

---

## Troubleshooting

### Build Fails on CI but Works Locally
- Check Node.js versions match
- Verify all dependencies in package.json
- Check environment variables are set
- Review CI logs for specific errors

### Slow Builds
- Enable dependency caching
- Parallelize test suites
- Optimize build commands
- Use smaller Docker base images

---

## Next Steps

After setting up CI/CD:
1. ✅ Add status badges to README
2. ✅ Configure branch protection rules
3. ✅ Set up deployment notifications
4. ✅ Document deployment process in README
5. ✅ Train team on CI/CD workflows

---

**Template Instructions**: Choose ONE platform and set up basic CI/CD. Expand with additional checks as project matures.
