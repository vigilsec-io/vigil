# Vigil — Active Task State

> **Purpose:** Read at the START of every session to resume exactly where the last session ended.
> Update whenever a task step completes or a session closes.

---

## Last Updated: 2026-06-26

**Status:** Sprint 0 — Project scaffold complete. Core engine + all 12 rules + 31 tests written. Not yet installed or tested.

---

## What Was Done This Session (2026-06-26)

1. ✅ **PRODUCT_VISION.md** — full product brief (gap evidence, rule catalog, distribution, revenue, moat)
2. ✅ **CLAUDE.md** — workspace rules, agent compatibility, SSM conventions
3. ✅ **ACTIVE_TASK.md** — this file
4. ✅ **pyproject.toml** — Python package with `vigil` CLI entry point
5. ✅ **requirements.txt / requirements-dev.txt** — zero runtime deps; pytest + bandit for dev
6. ✅ **.gitignore** — Python standard + secrets
7. ✅ **src/vigil/__init__.py** — package stub
8. ✅ **src/vigil/rules/base.py** — `Severity`, `Finding`, `Rule` ABC, `SEVERITY_ORDER`
9. ✅ **src/vigil/rules/secrets.py** — 7 rules: AWS key, password, API key, token, eval, shell=True, os.system
10. ✅ **src/vigil/rules/docker.py** — VGL-D001: the unique docker-compose port binding rule
11. ✅ **src/vigil/rules/dockerfile.py** — VGL-DF001 (root user), VGL-DF002 (latest tag)
12. ✅ **src/vigil/rules/deps.py** — VGL-DEP001 (pip-audit), VGL-DEP002 (npm audit)
13. ✅ **src/vigil/rules/__init__.py** — `DEFAULT_RULES` list + all exports
14. ✅ **src/vigil/engine.py** — `Engine.scan()`, `Engine.scan_dir()`, `Engine.blocking()`
15. ✅ **src/vigil/reporter.py** — terminal (colored) + JSON output
16. ✅ **src/vigil/cli.py** — `vigil scan <file|dir>` with --format and --severity flags
17. ✅ **plugin/hook.sh** — Claude Code PostToolUse hook (calls vigil, falls back to shared/scan.sh)
18. ✅ **tests/conftest.py** — fixtures for safe/unsafe compose + dockerfile
19. ✅ **tests/fixtures/** — 4 fixture files (safe_compose, unsafe_compose, safe_dockerfile, unsafe_dockerfile)
20. ✅ **tests/test_rules_docker.py** — 9 tests for VGL-D001
21. ✅ **tests/test_rules_secrets.py** — 11 tests for VGL-S/I rules
22. ✅ **tests/test_engine.py** — 12 tests for Engine class
23. ✅ **.gitea/workflows/test.yml** — CI pipeline (pytest + bandit + trivy)
24. ✅ **shared/GAPS.md** — market gap logged with full evidence
25. ✅ **Workspace CLAUDE.md** — Vigil added to project table
26. ✅ **security_runner.py** — "vigil" added to PROJECTS list
27. ✅ **WORKSPACE_IMPROVEMENTS.md** — Sprint 0 tasks added
28. ✅ **ACTIVE_WORKSPACE.md** — Vigil row added to per-project table

---

## Next Steps (Sprint 1 — in order)

1. **Install and run tests locally**
   ```bash
   cd vigil
   python3 -m venv venv && venv/bin/pip install -e . pytest==8.3.4
   venv/bin/pytest tests/ -v
   ```
   All 32 tests should pass. Fix any failures before proceeding.

2. **Wire the Claude Code hook** (replace shared/scan.sh reference)
   - Update `~/.claude/settings.json` PostToolUse hook to point to `vigil/plugin/hook.sh`
   - Or install vigil into the global venv and let hook.sh find it

3. **Create Gitea repo `fwss/vigil`** and push scaffold
   ```bash
   git init && git add . && git commit -m "vigil: initial scaffold — 12 rules, 32 tests"
   ```

4. **Add trivy IaC deep-scan rule** (VGL-T001)
   - Calls `trivy config <project_dir>` for Dockerfile/Terraform files
   - Deduplicates against existing VGL-DF001/002 findings

5. **Add nginx security header rule** (VGL-N001)
   - Checks nginx.conf for missing security headers (X-Frame-Options, CSP, HSTS)

6. **Add SARIF output format** to reporter.py
   - Enables GitHub Advanced Security annotations in PRs

7. **Write Claude Code plugin manifest** for marketplace submission

---

## Phase Status

| Phase | Status |
|-------|--------|
| Core engine + rules | ✅ Done (12 rules) |
| CLI (`vigil scan`) | ✅ Done |
| Claude Code hook | ✅ Done (plugin/hook.sh) |
| Tests (32 total) | ✅ Written — not yet run |
| CI pipeline | ✅ Done (.gitea/workflows/test.yml) |
| Gitea repo | ⏳ Next |
| Trivy IaC rule | ⏳ Next |
| nginx rule | ⏳ Next |
| SARIF output | ⏳ Future |
| VS Code extension | ⏳ Future |
| Claude Code marketplace | ⏳ Future |
| Team dashboard | ⏳ Future (H1B gated) |

---

## Key Files

| File | Purpose |
|---|---|
| `src/vigil/rules/base.py` | `Severity`, `Finding`, `Rule` ABC |
| `src/vigil/rules/docker.py` | VGL-D001 — the unique port binding rule |
| `src/vigil/engine.py` | Orchestrates all checks; `blocking()` for exit code |
| `src/vigil/cli.py` | `vigil scan` entry point |
| `plugin/hook.sh` | Claude Code PostToolUse hook |
| `PRODUCT_VISION.md` | Market gap, rule catalog, revenue model |

---

## Blockers

None. Ready to install and run tests.
