# Project Name — Secure Kiro SDD + TDD Template (2025 Production-Grade)

**Zero-trust, atomic, fully-governed development from the very first commit.**

This repository follows a battle-tested, security-first template designed for maximum discipline when using Kiro.
It enforces **Spec-Driven Development (SDD) + strict Test-Driven Development (TDD)** with deliberate, non-bypassable gates at every layer.

### What “secure by default” actually means here

| Feature                                 | Enforced From Commit #1 | How |
|-----------------------------------------|--------------------------|-----|
| Protected `main` (no direct pushes)     | Yes                      | GitHub Repository Ruleset + CODEOWNERS |
| Required PR + self-review               | Yes                      | 1 approval + codeowner rule |
| Required CI (ruff + mypy + ≥90% coverage) | Yes                    | Status check gate |
| Linear, clean history                   | Yes                      | Require linear history + squash-only merges |
| Conventional Commits                    | Yes                      | pre-commit + Kiro hooks |
| Atomic tasks (1 branch = 1 task = 1 PR) | Yes                      | Kiro `hooks.yaml` + branch naming validation |
| TDD Red → Green → Refactor (human gate) | Yes                      | Kiro Supervised mode |
| MyPy strict + Ruff format/lint          | Yes                      | pre-commit + CI |
| No code reaches `main` without your explicit final “Y” | Yes        | Kiro `tddRefactorAndClose` hook |

**Result**: You move at AI speed while shipping production-grade, auditable, type-safe code with 90–100% test coverage.

### Prerequisites (one-time, global)

- **uv** — the all-in-one Python toolchain
  → https://docs.astral.sh/uv/getting-started/installation/
- **Kiro IDE** — logged in with Claude 4.5 Sonnet access
  → https://kiro.ai
- **GitHub CLI** (`gh`) — optional but recommended for templating

### One-Time Project Setup (run immediately after clone)

```bash
# 1. Create and activate the locked environment
uv sync --all-extras --dev
#    → installs ruff, mypy, pytest, pre-commit, etc. exactly as pinned

# 2. Install git hooks (run on every future commit)
uv run pre-commit install --install-hooks --overwrite
#    → ruff format/lint + mypy run automatically, commit is blocked if they fail
```

That’s it. You are now fully locked and loaded.

### Daily Workflow (the loop you will live in)

1. `@kiro Generate spec for <feature>` → creates atomic tasks in `specs/tasks/`
2. Click a task → Kiro automatically:
   - Creates correct branch (`feat/login-magic-link`, `test/user-validation`, etc.)
   - Opens GitHub issue + draft PR
3. TDD cycle:
   - Red phase → approve failing tests → Green phase → Refactor + final gate
4. Type **Y** → Kiro squash-merges, closes PR/issue, deletes branch, returns to `main`

No manual branching. No manual PR creation. No untested code. No formatting drift.

### Repository Structure

```
.
├── specs/                  # Living SDD documents (requirements, design, tasks)
├── src/                    # Your actual code
├── tests/                  # TDD tests (generated first!)
├── .kiro/
│   ├── steering/           # Product OKRs, architecture rules, tech policies
│   ├── hooks.yaml          # Strict TDD + branch + merge orchestration
│   └── mcp.json            # GitHub automation (issue/PR/branch lifecycle)
├── .github/
│   ├── CODEOWNERS          # Forces self-review
│   └── workflows/ci.yml    # Required CI gate
├── pyproject.toml          # Single source of truth (Python version, ruff, mypy)
└── .pre-commit-config.yaml # Local instant enforcement
```

### Want to create a new project from this template?

```bash
gh repo create your-org/new-project --template your-username/kiro-sdd-template --public
# or private:  --private
```

Then run the one-time setup above.

**You now have the tooling, governance, and discipline of the fastest-moving, highest-quality solo AI engineering workflows.**

Let’s ship.
