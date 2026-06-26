# Vigil — Product Vision

**AI coding security co-pilot that blocks insecure code at the moment of generation, not 6 hours later in CI.**

---

## The Problem

AI coding tools (Claude Code, GitHub Copilot, Cursor, Windsurf) generate IaC and application code at high speed. They hallucinate secure-looking but insecure defaults because those defaults dominate training data:

- `"8000:8000"` in docker-compose (binds to 0.0.0.0, bypasses UFW — **no existing IaC tool catches this**)
- `FROM python:latest` (unpinned, supply-chain risk)
- Running containers as root (no USER directive)
- `shell=True` in subprocess calls
- Hardcoded secrets that "look like variables" to the AI

**Confirmed gap (2026-06-26):** shield-ecosystem had PostgreSQL, Redis, and FastAPI bound to 0.0.0.0 while Checkov, Trivy, Snyk, and Semgrep all returned 0 findings. Three live CVEs (Pillow, python-multipart, starlette) also missed by the weekly security runner. Vigil's custom rules caught all four findings in the first scan.

---

## The Solution

Vigil is a **PostToolUse hook engine**: it intercepts every file the AI writes, runs a layered security scan, and **blocks the write** if CRITICAL or HIGH findings are present. Zero lag. Zero CI wait. Zero exposure window.

```
AI writes file → Vigil scans → CRITICAL/HIGH? → Block + show fix
                              ↓
                            Clean → write proceeds silently
```

---

## Market Gap

| Tool | Docker port binding | At-generation | Dep CVE (instant) | AI-pattern rules |
|---|---|---|---|---|
| Checkov | ❌ miss | ❌ post-commit | ❌ | ❌ |
| Trivy config | ❌ miss | ❌ post-commit | ✅ (trivy fs) | ❌ |
| Semgrep | ❌ miss | ❌ post-commit | ❌ | ❌ |
| Snyk | ❌ miss | ❌ post-commit | ✅ | ❌ |
| **Vigil** | **✅ VGL-D001** | **✅ PostToolUse** | **✅ pip-audit** | **✅** |

---

## Rule Catalog

| Rule ID | Severity | What it catches |
|---|---|---|
| VGL-S001 | CRITICAL | AWS access key hardcoded (`AKIA...`) |
| VGL-S002 | CRITICAL | Hardcoded password |
| VGL-S003 | CRITICAL | Hardcoded API key |
| VGL-S004 | CRITICAL | Hardcoded token |
| VGL-I001 | CRITICAL | `eval()` / `exec()` injection |
| VGL-I002 | HIGH | `subprocess(shell=True)` |
| VGL-I003 | HIGH | `os.system()` |
| VGL-D001 | CRITICAL | **Docker `"PORT:PORT"` → 0.0.0.0 bypass** ← unique in market |
| VGL-DF001 | HIGH | Dockerfile running as root (no USER) |
| VGL-DF002 | MEDIUM | Unpinned `:latest` base image |
| VGL-DEP001 | HIGH | Python CVEs via pip-audit |
| VGL-DEP002 | HIGH | Critical npm CVEs via npm audit |

---

## Distribution Strategy

### Phase 1 — Claude Code Plugin (first mover, no competition)
- PostToolUse hook on `Write|Edit` matcher
- `plugin/hook.sh` calls `vigil scan <file>` — blocks on exit code 2
- Publish to Claude Code marketplace

### Phase 2 — VS Code Extension
- `vscode.workspace.onDidSaveTextDocument` → `vigil scan`
- Works with Copilot, Cursor (uses VS Code API), Windsurf
- Largest AI coding market

### Phase 3 — GitHub Actions
- `vigil-scan` action runs on every PR
- Catches AI-generated code even when editor plugin not installed
- SARIF output → GitHub Advanced Security annotations

### Phase 4 — JetBrains Plugin
- IntelliJ Platform SDK (covers all JetBrains IDEs)

---

## Revenue Model

| Tier | Price | Target | What's included |
|---|---|---|---|
| **Free** | $0 | Individual devs | Secrets + injection checks (VGL-S/I rules) |
| **Pro** | $29/mo | Solo founders, freelancers | All rules + dep CVE scanning + blocking mode |
| **Team** | $99/mo | Startup teams | Team dashboard, per-dev scan reports, custom rules, Slack alerts |
| **Enterprise** | Custom | Companies with compliance needs | SOC2 evidence export, SIEM integration, private rule registry, SLA |

---

## Defensible Moat

1. **Timing** — only tool that blocks at generation time. Competitors block at commit/CI.
2. **VGL-D001** — confirmed unique rule. No other tool catches docker-compose port binding. This is the beachhead story: "tool missed by Checkov + Trivy + Snyk + Semgrep, caught by Vigil."
3. **AI-pattern corpus** — grows with every block fired across all customers. Competitors analyzing human-authored code miss AI-specific insecure patterns. Our corpus trains itself.
4. **Zero-dep core** — stdlib-only Python. Install in 5 seconds. Works anywhere Python 3.11+ exists.

---

## H1B-Safe Path to Market

- Open-source the scan engine (rule corpus + CLI + Claude Code hook)
- Closed-source: hosted telemetry dashboard + enterprise rule registry
- No revenue event until LLC formed — open-source product builds corpus, brand, and community
- First paid feature: Team dashboard (Stripe → SSM pattern, already built in other FWSS projects)

---

## Competitive Landscape

| Category | Tools | Vigil advantage |
|---|---|---|
| SAST | Semgrep, CodeQL, Bandit | We run at write-time, not post-commit; we catch IaC patterns they don't |
| IaC scanning | Checkov, Trivy, KICS | We catch docker-compose port binding they miss; we're IDE-native |
| Secret scanning | Gitleaks, Trufflehog, GitGuardian | They scan git history; we block before the secret is ever committed |
| Dep scanning | Snyk, Dependabot, pip-audit | We run immediately on every requirements.txt touch; they run on schedule |
| AI coding guardrails | No existing product | We are the category |

---

## Success Metrics

- **Phase 1:** 100 Claude Code plugin installs; first "caught a real vuln" testimonial
- **Phase 2:** VS Code extension 500 installs; 1 team customer
- **Phase 3:** 1 enterprise pilot (SOC2 compliance use case)
- **Corpus target:** 1,000 blocked writes = statistical dataset of AI-generated insecure patterns
