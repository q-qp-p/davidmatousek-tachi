# Local Development Environment - tachi

**Last Updated**: 2026-04-17
**Owner**: DevOps Agent

---

## First-Read for Local Contributors

New contributors walking through the tachi pipeline for the first time should start with **`examples/maestro-reference/`** (Feature 145). It is the canonical MAESTRO worked example and contains a complete set of pipeline outputs — `threats.md`, `risk-scores.md`, `compensating-controls.md`, `threat-report.md`, `attack-trees/`, `attack-chains.md`, all infographic JPEGs, and `security-report.pdf` — rendered from a multi-agent agentic-AI architecture that surfaces findings across all seven MAESTRO layers and several of the six canonical agentic patterns (Feature 142). Reading these artifacts end-to-end is the fastest way to build a mental model of what each tachi stage produces and how they compose into the final PDF. The example is also committed to the byte-deterministic backward-compatibility baseline set (see Python Test Suite below), so its outputs are stable across regeneration.

---

## Prerequisites

### Required Software
- **Docker Desktop**: Version {{VERSION}}+
- **Node.js**: Version {{VERSION}} (LTS recommended)
- **Git**: Version {{VERSION}}+

### Optional Software
- **Python**: Version 3.9+ (stdlib only at runtime, no external packages required). Required by the deterministic data extraction scripts in `scripts/`: `extract-report-data.py` (security report data for `/tachi.security-report`), `extract-infographic-data.py` (infographic template data for `/tachi.infographic`), and the shared `tachi_parsers.py` module. Most macOS and Linux systems include Python 3.9+ by default; verify with `python3 --version`. For running the test suite, install dev dependencies via `pip install -r requirements-dev.txt` (see Python Test Suite below)
- **VS Code**: Recommended IDE
- **Postman/Insomnia**: API testing
- **jq**: JSON processor, required by `.aod/scripts/bash/run-state.sh` for the Full Lifecycle Orchestrator (`brew install jq` on macOS, `apt-get install jq` on Linux)
- **GitHub CLI (`gh`)**: Used by `make init` to auto-create a GitHub Projects board for backlog tracking. Requires the `project` OAuth scope (`gh auth refresh -s project`). If not installed or not authenticated, init continues without creating the board. Install via `brew install gh` on macOS or see [cli.github.com](https://cli.github.com)
- **Typst CLI**: Required by `/tachi.security-report` for PDF generation. Install via `brew install typst` on macOS, `cargo install typst-cli` on Linux, or `winget install typst` on Windows. If not installed, the `/tachi.security-report` command displays platform-specific install instructions and halts. See `templates/tachi/security-report/` for Typst template sources
- **Mermaid CLI (`mmdc`)**: **Hard prerequisite when scanning projects that contain an `attack-trees/` directory with Critical/High findings** (Feature 130, [ADR-022](../../architecture/02_ADRs/ADR-022-mmdc-hard-prerequisite.md)). Used by `scripts/extract-report-data.py::render_mermaid_to_png()` to render Mermaid attack tree diagrams to PNG for the Attack Path Pages section of the PDF security report (Feature 112). Requires Node.js. Install via `npm install -g @mermaid-js/mermaid-cli`. If not installed and attack trees are present, the `/tachi.security-report` pipeline fails loud at the preflight gate with a `RuntimeError` listing the install command — this replaces the pre-Feature-130 silent text fallback which produced broken PDFs with raw Mermaid source dumped verbatim. For projects WITHOUT an `attack-trees/` directory, mmdc remains unused and the pipeline continues to run unaffected. `scripts/install.sh` emits a best-effort courtesy warning at setup time if mmdc is absent. See `README.md` `## Prerequisites` section for per-OS install commands and the CI acceptance test at `.github/workflows/tachi-mmdc-preflight.yml`

### make init Personalization

`make init` personalizes the following template files by replacing `tachi` and other template variables with your project values at setup time:

- `CLAUDE.md`
- `README.md`
- `.claude/README.md`
- `.claude/agents/_README.md`
- `.claude/rules/commands.md`
- `.claude/rules/context-loading.md`
- `.claude/rules/deployment.md`
- `.claude/rules/git-workflow.md`
- `.claude/rules/governance.md`
- `.claude/rules/scope.md`
- `docs/product/02_PRD/INDEX.md`

No manual edits to these files are needed before running `make init`. After initialization, these files will contain your project name and description in place of template placeholders.

---

## Quick Start

```bash
# Clone repository
git clone {{REPOSITORY_URL}}
cd tachi

# Install dependencies
npm install

# Start development environment
docker-compose up -d

# Run database migrations
npm run migrate

# Start development servers
npm run dev
```

---

## Docker Compose Services

```yaml
# docker-compose.yml structure
services:
  frontend:
    # Frontend development server with hot reload
    ports: ["{{FRONTEND_PORT}}:{{FRONTEND_PORT}}"]

  backend:
    # Backend API server
    ports: ["{{BACKEND_PORT}}:{{BACKEND_PORT}}"]

  database:
    # PostgreSQL (or your database)
    ports: ["{{DATABASE_PORT}}:{{DATABASE_PORT}}"]
```

---

## Environment Variables

Copy `.env.example` to `.env`:

```bash
cp .env.example .env
```

**Required Variables**:
```
DATABASE_URL=postgresql://localhost:{{DATABASE_PORT}}/tachi_dev
API_URL=http://localhost:{{BACKEND_PORT}}
FRONTEND_URL=http://localhost:{{FRONTEND_PORT}}
```

**Optional Variables**:
```
AOD_LOG_FILE=.aod/logs/aod.log
GEMINI_API_KEY=your-gemini-api-key
```

The `AOD_LOG_FILE` variable controls where the logging utility writes its output. If not specified, it defaults to `.aod/logs/aod.log`. You can override this to write logs to a different location.

The `GEMINI_API_KEY` variable enables AI-generated threat infographic images via the Gemini API (`gemini-3-pro-image-preview` model). This is used by the threat infographic agent when invoked via the standalone `/tachi.infographic` command. If not set, the agent produces Mermaid-based visual specifications without rasterized image output. No local infrastructure is required -- the agent calls the external Gemini API directly.

---

## Common Commands

```bash
# Start all services
docker-compose up -d

# Stop all services
docker-compose down

# View logs
docker-compose logs -f [service-name]

# Rebuild after changes
docker-compose up --build

# Reset database
docker-compose down -v
docker-compose up -d
npm run migrate
```

---

## Platform Adapter Testing (Local)

Feature 021 introduced platform adapters in `adapters/`. To test an adapter locally, copy its files into a test project that uses the target platform.

### Testing File-Transformation Adapters

File-transformation adapters (Claude Code, Cursor, Copilot, Generic) contain static files. Testing means verifying the files are correctly recognized by the target platform.

```bash
# Claude Code adapter: copy agents into a test project
mkdir -p /path/to/test-project/.claude/agents/tachi
cp adapters/claude-code/agents/*.md /path/to/test-project/.claude/agents/tachi/

# Cursor adapter: copy rules into a test project
mkdir -p /path/to/test-project/.cursor/rules/tachi
cp adapters/cursor/rules/*.mdc /path/to/test-project/.cursor/rules/tachi/

# Copilot adapter: copy agents and instructions into a test project
mkdir -p /path/to/test-project/.github/agents/tachi
cp adapters/copilot/agents/*.agent.md /path/to/test-project/.github/agents/tachi/
cp adapters/copilot/instructions/*.instructions.md /path/to/test-project/.github/agents/tachi/
```

### Testing the GitHub Actions Adapter

The GitHub Actions adapter requires a GitHub repository with Actions enabled. To test locally before pushing:

1. Copy the workflow file:
   ```bash
   mkdir -p /path/to/test-project/.github/workflows
   cp adapters/github-actions/tachi.threat-model.yml /path/to/test-project/.github/workflows/
   ```

2. Validate the YAML syntax:
   ```bash
   # Requires yq or a YAML linter
   yq eval '.' adapters/github-actions/tachi.threat-model.yml > /dev/null && echo "YAML valid"
   ```

3. Set the `LLM_API_KEY` repository secret in the test repository (Settings > Secrets and variables > Actions).

4. Trigger the workflow by opening a PR that modifies files under `docs/architecture/`, or use manual dispatch from the Actions tab.

### Verifying VERSION Files

After modifying source agents, regenerate and verify VERSION files:

```bash
# Regenerate VERSION for a specific adapter
./scripts/generate-adapter-version.sh adapters/claude-code

# Verify the VERSION file contents
cat adapters/claude-code/VERSION
```

The VERSION file contains the source commit SHA, generation date, and SHA-256 checksums of each source agent file. Use it to confirm adapter files are derived from the expected source version.

---

## Python Test Suite

Feature 128 introduced the first pytest-based test infrastructure for tachi. Project-level pytest configuration lives in `pyproject.toml` at the repo root (`[tool.pytest.ini_options]` section: `testpaths = ["tests"]`, strict markers, `-ra` reporting). Development dependencies are pinned in `requirements-dev.txt` (pytest, pytest-cov).

### Installation

```bash
# Create a virtualenv (recommended)
python3 -m venv .venv
source .venv/bin/activate

# Install development dependencies
pip install -r requirements-dev.txt
```

No runtime dependencies are added -- the extraction scripts remain stdlib-only. Dev dependencies are only needed when running tests.

### Running Tests

```bash
# Run the full test suite
pytest tests/

# Run a single module
pytest tests/scripts/test_extract_report_data.py

# Run with coverage
pytest tests/ --cov=scripts --cov-report=term-missing

# Run only smoke tests
pytest tests/scripts/test_smoke.py
```

### Test Layout

```
tests/
├── conftest.py                          # Shared fixtures and pytest hooks
└── scripts/
    ├── test_smoke.py                    # Fast sanity checks
    ├── test_attack_chain_extraction.py  # Attack chain data extraction (Feature 141)
    ├── test_attack_chains.py            # Cross-layer attack chain correlation (Feature 141)
    ├── test_backward_compatibility.py   # Legacy output compatibility
    ├── test_command_dispatch.py         # Command routing
    ├── test_extract_infographic_data.py # Infographic data extraction
    ├── test_extract_report_data.py      # Security report data extraction
    ├── test_mmdc_preflight.py           # mmdc preflight gate and mid-render aggregator (Feature 130)
    ├── test_pdf_page_positioning.py     # PDF layout ordering
    └── fixtures/
        ├── exec_arch/                   # Executive architecture infographic inputs
        ├── report_data/                 # Report extraction inputs
        └── golden/                      # Expected outputs for golden-file tests
```

### Adding New Tests

- Place new test modules under `tests/scripts/` using the `test_*.py` naming convention
- Store input fixtures under `tests/scripts/fixtures/<category>/`
- Store expected outputs for golden-file comparisons under `tests/scripts/fixtures/golden/`
- Keep tests hermetic: no network calls, no dependence on absolute host paths

### CI Integration Status

The pytest harness is available locally but is not yet wired into the GitHub Actions workflows. See `docs/devops/CI_CD_GUIDE.md` for the current CI test story and follow-up notes.

---

## Troubleshooting

### Port Already in Use
```bash
# Find process using port
lsof -i :{{PORT}}

# Kill process
kill -9 <PID>
```

### Database Connection Failed
```bash
# Check database is running
docker-compose ps

# Check logs
docker-compose logs database

# Reset database
docker-compose down -v && docker-compose up -d
```

### Hot Reload Not Working
- Ensure volumes are mounted correctly in docker-compose.yml
- Check file watcher limits (Linux: `sudo sysctl fs.inotify.max_user_watches=524288`)

---

**Template Instructions**: Replace all `{{TEMPLATE_VARIABLES}}` with your actual configuration.
