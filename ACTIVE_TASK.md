# Vigil — Active Task State

> **Purpose:** Read at the START of every session to resume exactly where the last session ended.
> Update whenever a task step completes or a session closes.

---

## Last Updated: 2026-06-27

**Status:** 36 rules, 193 tests, all passing. vigilsec 0.1.2 on PyPI + VS Code Marketplace. Fix messages cloud-agnostic (AWS SSM → all providers). vigil triage stripped from public package → moved to shared/vigil_triage.py. Public/workspace boundary rule added to CLAUDE.md. Pre-publish checklist enforced. Next: VGL-PKG rules (hallucination/staleness/CVE at generation time) + public GitHub repo.

---

## Phase 0 — Completed (2026-06-26)

1. ✅ Core engine + 12 rules (VGL-S/I/D/DF/DEP), CLI, hook, Gitea
2. ✅ 31 tests, pyproject.toml, .gitea/workflows/test.yml, PRODUCT_VISION.md

---

## Phase 1 — Completed (2026-06-26)

1. ✅ VGL-DF003 (ENV secret baking), VGL-N001 (nginx headers), VGL-T001 (trivy)
2. ✅ SARIF output, `vigil init` command, plugin manifest + README_INSTALL.md
3. ✅ 65 tests total

---

## Phase 2 — Completed (2026-06-26/27)

1. ✅ **`.vigilrc` config** — `src/vigil/config.py`; disabled_rules, min_severity, exclude_paths, telemetry
2. ✅ **VGL-D002** — docker-compose `environment:` hardcoded secrets; HIGH severity
3. ✅ **VGL-K001** — K8s `privileged: true` (CRITICAL), `hostNetwork/hostPID/hostIPC` (HIGH)
4. ✅ **VGL-IAM001** — IAM `"Action": "*"` (CRITICAL), `"Resource": "*"` (HIGH); multi-line list
5. ✅ **VGL-DF003 URL extension** (ticket #1) — credential-embedded URLs in ENV/ARG values
6. ✅ **VGL-S005–S010** (ticket #3) — JWT secret, PEM key, DB credential URLs, Stripe, Slack, provider keys
7. ✅ **VGL-PI001–PI004** (ticket #5) — prompt injection patterns in AI-calling code
8. ✅ **VGL-MCP001–MCP003** (ticket #10) — MCP server security (tool poisoning, dynamic descriptions, shell tools)
9. ✅ **VGL-A001–A004** (ticket #12) — excessive agency (LLM→shell, auto-approve, unbounded loops, LLM→file)
10. ✅ **`# vigil: ignore`** — inline suppression in Engine.scan() (mirrors `# noqa` / `# nosec`)
11. ✅ **`vigil feedback` command** — opens thefwss.com/vigil; dim footer in terminal output
12. ✅ **Anonymous telemetry** — `src/vigil/telemetry.py`; records only rule_id + severity + file_ext + ts; local ~/.vigil/events.jsonl; opt-out via VIGIL_NO_TELEMETRY=1 or `telemetry = false` in .vigilrc; 11 tests
13. ✅ **35 rules total** — 169 tests, all passing in 0.17s; pushed to Gitea (c4bf936)
14. ✅ **Gitea tickets closed** — #1, #3, #5, #10, #12 all closed with fix references
15. ✅ **docs/PROJECT_STATE.md** — ASCII architecture diagram + state snapshot
16. ✅ **docs/MEDIUM_ARTICLE.md** — full Medium article draft (3 title variants)
17. ✅ **VGL-S011** — shell script secret inline on subprocess/SSH (`ps aux` leak); `src/vigil/rules/shell.py`; 16 tests; closes Gitea #15
18. ✅ **Typeform waitlist** — `thefwss.com/vigil` Cloudflare redirect → `https://form.typeform.com/to/AEs5yinT`; `vigil feedback` command wired
19. ✅ **README.md** — full install guide, 36-rule catalog, `.vigilrc` reference, `# vigil: ignore` docs, telemetry opt-out, "Adding a Rule" section
20. ✅ **PyPI published** — `vigilsec` 0.1.0 on PyPI; `pip install vigilsec`; `pyproject.toml` updated (BUSL-1.1, project URLs, keywords)
21. ✅ **Medium article reformatted** — `docs/MEDIUM_ARTICLE.md` rewritten for Medium's native editor: no markdown tables, H2/H3 headings, block quotes, prose competitive comparison; 3 title variants; Typeform CTA

---

## Phase 3 — Completed (2026-06-28)

1. ✅ **VS Code extension** — published as `vigilsec.vigil-security` v0.1.2
   - URL: https://marketplace.visualstudio.com/items?itemName=vigilsec.vigil-security
2. ✅ **PyPI** — `vigilsec` 0.1.2 live; `pip install vigilsec`
3. ✅ **GitHub Actions** — `vigil-action/workflow-template.yml`; SARIF upload
4. ✅ **`vigil stats`** — local scan history, ASCII bar chart, severity %, file type
5. ✅ **Medium article** — https://medium.com/@rjbdjnf/i-built-12-apps-with-ai-every-one-had-the-same-security-hole-so-i-built-a-tool-that-fixes-it-edf42a9aa546
6. ✅ **Fix messages cloud-agnostic** — AWS SSM → all providers (VGL-S001–S010, VGL-DF003)
7. ✅ **vigil triage stripped** — moved to shared/vigil_triage.py; public CLI clean
8. ✅ **Public/workspace boundary rule** — added to CLAUDE.md; pre-publish checklist enforced

## Next Steps — Phase 4

**Resume instruction:** Start with step 1.

1. ⏳ **VGL-PKG rules** — package hallucination/staleness/CVE at AI generation time
   - VGL-PKG001: known CVE in pinned version (OSV.dev batch API)
   - VGL-PKG002: package doesn't exist on registry (slopsquatting/hallucination)
   - VGL-PKG003: version significantly behind latest (stale AI training data)
   - VGL-PKG004: newly registered package with suspicious signals (supply chain)
   - Inline in hook; 24h local cache at ~/.vigil/pkg_cache.json; fail-open on network error

2. ⏳ **Public GitHub repo** — mirror vigil/src/vigil/ to github.com/vigilsec/vigil
   - Unlocks: GitHub Actions Marketplace, stars/forks, contributors
   - H1B-safe: open-source publishing ≠ revenue

3. ⏳ **Open VSX** — publish for Cursor/Windsurf users (Eclipse account friction parked)

4. ⏳ **pre-commit hooks registry** — `.pre-commit-hooks.yaml` + submit to pre-commit.com

---

## Remaining Open Tickets (future sprints)

| # | Title | Priority |
|---|-------|----------|
| #2 | GitHub Actions integration | HIGH |
| #4 | `--watch` mode | HIGH |
| #6 | VGL-D003: secrets in Docker Compose volumes | MEDIUM |
| #7 | VGL-DEP003: lockfile integrity check | MEDIUM |
| #8 | VGL-CF001: Cloudflare Tunnel config | MEDIUM |
| #9 | VGL-TF001: Terraform open security groups | MEDIUM |
| #11 | VGL-NET001: raw IP + port in code | LOW |
| #13 | `vigil stats` command | LOW |
| #14 | Pre-commit hook integration | LOW |

---

## Phase Status

| Phase | Status | Details |
|-------|--------|---------|
| Phase 0 — Core engine | ✅ Complete | 12 rules, 31 tests, CLI, hook, Gitea |
| Phase 1 — Rule expansion | ✅ Complete | +3 rules, SARIF, plugin manifest, 65 tests |
| Phase 2 — Config + AI-era rules | ✅ Complete | 35 rules, 169 tests, telemetry, tickets closed |
| Phase 3 — Distribution (VS Code + PyPI) | ✅ Complete | vigilsec 0.1.2: PyPI ✅, VS Code Marketplace ✅, GitHub Actions template ✅, vigil stats ✅ |
| Phase 4 — GitHub Actions + Team dashboard | ⏳ Future | H1B gated on revenue features |
| Phase 5 — Enterprise + JetBrains | ⏳ Future | SOC2, SIEM, on-prem |

---

## Key Numbers

| Metric | Value |
|--------|-------|
| Total rules | 36 |
| Total tests | 188 |
| Test runtime | 0.18s |
| Gitea commit | c4bf936 (pre-S011/telemetry/README/PyPI) |
| PyPI package | `vigilsec` 0.1.2 |
| Lines of source | ~1,600 |

---

## Key Files

| File | Purpose |
|---|---|
| `src/vigil/rules/base.py` | `Severity`, `Finding`, `Rule` ABC |
| `src/vigil/rules/docker.py` | VGL-D001 — the unique port binding rule |
| `src/vigil/rules/agency.py` | VGL-A001–A004 — excessive agency (novel AI-era category) |
| `src/vigil/rules/mcp_security.py` | VGL-MCP001–MCP003 — MCP server security |
| `src/vigil/rules/prompt_injection.py` | VGL-PI001–PI004 — prompt injection in AI code |
| `src/vigil/engine.py` | Orchestrates all checks; `blocking()`; `# vigil: ignore` |
| `src/vigil/telemetry.py` | Anonymous local event collection |
| `src/vigil/config.py` | `.vigilrc` loader; VigilConfig dataclass |
| `src/vigil/cli.py` | `vigil scan` + `vigil init` + `vigil feedback` |
| `plugin/hook.sh` | Claude Code PostToolUse hook |
| `docs/MEDIUM_ARTICLE.md` | Ready to publish (needs PyPI link first) |
| `PRODUCT_VISION.md` | Full roadmap, market gap evidence, revenue model |

---

## Blockers

None.
