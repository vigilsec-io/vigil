# Vigil — Claude Instructions

## Session Start Protocol (MANDATORY)
1. Read `ACTIVE_TASK.md` — resume from the exact step where the last session ended
2. Read `../WORKSPACE_IMPROVEMENTS.md` — treat all `- [ ]` items tagged `[vigil]` as active tasks
3. Read `../shared/INCIDENTS.md` — check for any OPEN incidents before starting work
4. When you complete a task step, update `ACTIVE_TASK.md` immediately (don't batch at end)

## Session End Sync (MANDATORY)
After any session that adds rules, changes the engine, or ships a distribution artifact:
1. Mark completed tasks `[x]` with date in `ACTIVE_TASK.md` and `WORKSPACE_IMPROVEMENTS.md`
2. Update `ACTIVE_TASK.md` Next Steps to reflect actual current state
3. If the Gitea repo is created, add entry to `../shared/agents/security_runner.py` PROJECTS list ✅ done

## Project Overview
**Vigil** — AI coding security co-pilot.  
Intercepts every file an AI coding assistant writes and blocks it if CRITICAL/HIGH security findings are detected. The only tool that catches the docker-compose `"PORT:PORT"` port binding pattern (confirmed gap across Checkov, Trivy, Snyk, Semgrep — see `PRODUCT_VISION.md`).

- **Core:** stdlib-only Python 3.11+ package (`src/vigil/`)
- **CLI:** `vigil scan <file|dir>` — exit code 2 on CRITICAL/HIGH
- **Hook:** `plugin/hook.sh` — Claude Code PostToolUse hook
- **Rules:** 12 built-in rules covering secrets, IaC, Dockerfile, and dependency CVEs
- **Tests:** 32 tests across 3 test files
- **Distribution:** Claude Code plugin → VS Code extension → GitHub Actions → JetBrains

## ⚖️ IP & Ownership
All code, ideas, rule corpus, product concepts, and artifacts in this project are the exclusive intellectual property of Prem Kumar Akula (FWSS). Claude is a coding and engineering partner only — no ownership stake or legal claim.

## Project Structure
```
vigil/
├── src/vigil/
│   ├── __init__.py        — package stub
│   ├── engine.py          — Engine class (scan, scan_dir, blocking)
│   ├── cli.py             — vigil scan CLI
│   ├── reporter.py        — terminal + JSON output
│   └── rules/
│       ├── base.py        — Severity, Finding, Rule ABC, SEVERITY_ORDER
│       ├── secrets.py     — VGL-S001–S004, VGL-I001–I003
│       ├── docker.py      — VGL-D001 (unique docker-compose port rule)
│       ├── dockerfile.py  — VGL-DF001 (root user), VGL-DF002 (latest tag)
│       └── deps.py        — VGL-DEP001 (pip-audit), VGL-DEP002 (npm audit)
├── plugin/
│   └── hook.sh            — Claude Code PostToolUse hook
├── tests/
│   ├── conftest.py        — fixtures (safe/unsafe compose + dockerfile)
│   ├── test_engine.py     — 12 engine tests
│   ├── test_rules_docker.py — 9 docker rule tests
│   ├── test_rules_secrets.py — 11 secret/injection tests
│   └── fixtures/          — 4 test fixture files
├── pyproject.toml         — package config, vigil CLI entry point
├── requirements-dev.txt   — pytest + bandit (-e . for editable install)
└── .gitea/workflows/test.yml — CI (pytest + bandit + trivy)
```

## Rule Naming Convention
- `VGL-S###` — Secrets / injection
- `VGL-D###` — Docker IaC
- `VGL-DF###` — Dockerfile
- `VGL-N###` — nginx
- `VGL-DEP###` — Dependencies
- `VGL-T###` — Trivy-based IaC (future)

## Adding a New Rule
1. Create or add to the appropriate `src/vigil/rules/<category>.py`
2. Inherit from `Rule` ABC; implement `applies_to(path)` and `check(path) -> list[Finding]`
3. Import and add to `DEFAULT_RULES` in `src/vigil/rules/__init__.py`
4. Add tests to `tests/test_rules_<category>.py`
5. Document in `PRODUCT_VISION.md` rule catalog table

## AWS / SSM Conventions
- Profile: `vigil` (IAM user `vigil-svc` — create when paid features are needed)
- SSM namespace: `/vigil/*`
- No secrets needed for the free open-source core — stdlib-only
- When Team dashboard is built: `/vigil/stripe_key`, `/vigil/db_url`

## Agent Compatibility
### Dev Agent
- Picks up `[BUG]` and `[FEATURE]` Gitea issues automatically at 8pm CT
- Can write rules, add tests, run `pytest tests/`
- Cannot publish to Claude Code marketplace or push npm packages (paid ops)

### QA Agent
- Runs `pytest tests/ -v` nightly at 2am
- On failure: opens Gitea issue + Telegram alert

### Security Agent
- Runs bandit, gitleaks, semgrep, trivy on `src/vigil/` weekly
- Opens Gitea issues for HIGH/CRITICAL findings

### PM Agent
- Reads this ACTIVE_TASK.md at 9am daily for standup
- Reports phase progress and rule count to Telegram

## Commands
```bash
# Install (editable)
cd vigil && python3 -m venv venv && venv/bin/pip install -e . pytest==8.3.4

# Run tests
venv/bin/pytest tests/ -v

# Scan a file
venv/bin/vigil scan path/to/file.yml

# Scan a directory
venv/bin/vigil scan path/to/project/

# JSON output (for CI integrations)
venv/bin/vigil scan path/to/project/ --format json

# Exit codes: 0=clean, 1=advisory, 2=CRITICAL/HIGH (blocks Claude Code)
```
