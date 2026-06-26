# Vigil — Active Task State

> **Purpose:** Read at the START of every session to resume exactly where the last session ended.
> Update whenever a task step completes or a session closes.

---

## Last Updated: 2026-06-26

**Status:** Phase 1 in progress. 65/65 tests passing. 5 new rules + SARIF output + `vigil init` + plugin manifest done. Remaining Phase 1: push to Gitea, wire Claude Code hook.

---

## Phase 0 — Completed This Session (2026-06-26)

### Code
1. ✅ **src/vigil/rules/base.py** — `Severity`, `Finding`, `Rule` ABC, `SEVERITY_ORDER`
2. ✅ **src/vigil/rules/secrets.py** — VGL-S001–S004 (key/password/api/token), VGL-I001–I003 (eval/shell/os.system)
3. ✅ **src/vigil/rules/docker.py** — VGL-D001: unique docker-compose port binding rule (confirmed gap across Checkov/Trivy/Snyk/Semgrep)
4. ✅ **src/vigil/rules/dockerfile.py** — VGL-DF001 (root user), VGL-DF002 (unpinned :latest)
5. ✅ **src/vigil/rules/deps.py** — VGL-DEP001 (pip-audit), VGL-DEP002 (npm audit)
6. ✅ **src/vigil/rules/__init__.py** — `DEFAULT_RULES` list + all exports
7. ✅ **src/vigil/engine.py** — `Engine.scan()`, `Engine.scan_dir()`, `Engine.blocking()`
8. ✅ **src/vigil/reporter.py** — colored terminal + JSON output
9. ✅ **src/vigil/cli.py** — `vigil scan <file|dir>` CLI (exit 0/1/2)
10. ✅ **plugin/hook.sh** — Claude Code PostToolUse hook (calls vigil, falls back to shared/scan.sh)
11. ✅ **31 tests** across test_engine.py (12), test_rules_docker.py (9), test_rules_secrets.py (11) — all passing
12. ✅ **pyproject.toml** — `build-backend = "setuptools.build_meta"` (not legacy); `vigil` CLI entry point
13. ✅ **.gitea/workflows/test.yml** — CI (pytest + bandit + trivy)

### Docs + Workspace
14. ✅ **PRODUCT_VISION.md** — problem, market gap table, rule catalog, 4-phase roadmap, distribution strategy, revenue model, moat, H1B path, competitive landscape, success metrics
15. ✅ **CLAUDE.md** — session protocol, project structure, rule naming convention, agent compatibility
16. ✅ **Workspace CLAUDE.md** — Vigil added to project table
17. ✅ **security_runner.py** — "vigil" added to PROJECTS list
18. ✅ **WORKSPACE_IMPROVEMENTS.md** — Sprint 0 tasks marked done; Sprint 1 tasks listed
19. ✅ **ACTIVE_WORKSPACE.md** — Vigil row added to per-project table
20. ✅ **shared/GAPS.md** — market gap logged with full evidence (confirmed no other tool catches VGL-D001)

### Infra
21. ✅ **Gitea repo** `fwss/vigil` created and pushed — http://100.80.161.44:3000/fwss/vigil
22. ✅ **venv installed** at `vigil/venv/` with Python 3.12, vigil editable install, pytest

---

## CLI Verified Working

```bash
# From vigil/ directory:
venv/bin/vigil scan tests/fixtures/docker-compose-unsafe.yml
# → BLOCKED: 3 CRITICAL findings (ports 8000, 5432, 6379 exposed)
# → exit code 2

venv/bin/vigil scan tests/fixtures/docker-compose-safe.yml
# → clean, exit code 0

venv/bin/pytest tests/ -v
# → 31 passed in 0.03s
```

---

## Phase 1 — Completed This Session (2026-06-26)

1. ✅ **VGL-DF003** — `ENV/ARG` secret layer baking rule added to `dockerfile.py`
2. ✅ **VGL-N001** — nginx security headers + weak TLS rule (`src/vigil/rules/nginx.py`)
3. ✅ **VGL-T001** — trivy IaC deep-scan rule (`src/vigil/rules/trivy.py`)
4. ✅ **SARIF output** — `report_sarif()` in `reporter.py` + `--format sarif` CLI flag
5. ✅ **`vigil init` command** — wires PostToolUse hook into `.claude/settings.json`
6. ✅ **Plugin manifest** — `plugin/manifest.json` + `plugin/README_INSTALL.md`
7. ✅ **34 new tests** — 65 total (was 31); all passing in 0.10s
8. ✅ **`__init__.py`** — DEFAULT_RULES updated with 3 new rules (DF003, N001, T001)

---

## Next Steps — Phase 1 Remaining + Phase 2

**Resume instruction:** Start with step 1.

1. **Push Phase 1 to Gitea** — `git add -A && git commit && git push gitea main`

2. **Wire Claude Code hook** — run `vigil init --global` to activate for all projects
   - Then verify: write an unsafe docker-compose.yml and confirm Claude Code blocks it

3. **Phase 2: `.vigilrc` config file** — allow per-project rule overrides
   - Schema: `{ "disabled_rules": ["VGL-T001"], "min_severity": "HIGH" }`
   - Loaded by Engine at startup if `.vigilrc` exists in project root or parents

4. **Phase 2: K8s/Helm YAML rule** (VGL-K001) — scan for `privileged: true`, `hostNetwork: true`, no resource limits
   - `applies_to`: YAML files containing `apiVersion:` and `kind:` keys

5. **Phase 2: IAM policy rule** (VGL-IAM001) — scan for `"Action": "*"` or `"Resource": "*"` wildcards in policy JSON/YAML

---

## Phase Status

| Phase | Status | Details |
|-------|--------|---------|
| Phase 0 — Core engine | ✅ Complete | 12 rules, 31 tests, CLI, hook, Gitea |
| Phase 1 — Rule expansion + Claude Code marketplace | ⏳ Next | VGL-T001, N001, DF003, SARIF, plugin manifest |
| Phase 2 — VS Code extension + config | ⏳ Future | `.vigilrc`, custom rule DSL, K8s/IAM rules |
| Phase 3 — GitHub Actions + Team dashboard | ⏳ Future | **H1B gated** — build now, revenue after LLC |
| Phase 4 — Enterprise + JetBrains | ⏳ Future | SOC2, SIEM, on-prem |

---

## Key Files

| File | Purpose |
|---|---|
| `src/vigil/rules/base.py` | `Severity`, `Finding`, `Rule` ABC — extend this for every new rule |
| `src/vigil/rules/docker.py` | VGL-D001 — the unique port binding rule; the beachhead story |
| `src/vigil/engine.py` | Orchestrates all checks; `blocking()` determines exit code |
| `src/vigil/cli.py` | `vigil scan` entry point |
| `plugin/hook.sh` | Claude Code PostToolUse hook |
| `PRODUCT_VISION.md` | Full 4-phase roadmap, market gap evidence, revenue model |

---

## Blockers

None. Phase 1 ready to start.
