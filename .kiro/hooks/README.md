# Kiro Hooks Configuration

This directory contains the active hooks that enforce strict SDD (Specification-Driven Development) and TDD (Test-Driven Development) workflows.

## Overview

These hooks automate and enforce industry-standard development practices throughout the entire development lifecycle, from specification creation to code merge. Each hook is defined in a `.kiro.hook` file in this directory.

## Active Hooks

### 1. Documentation Sync
**File:** `docs-sync-on-change.kiro.hook`  
**Trigger:** File edited  
**File Patterns:** `*.py`, `pyproject.toml`, `kiro.yaml`, `specs/**/*.md`

Automatically prompts the agent to update documentation when source files or specifications are modified.

**Actions:**
- Monitors Python source files and project configuration
- Reviews changes and updates README.md for high-level changes
- Updates docs/ folder for detailed documentation
- Ensures documentation reflects current codebase state

### 2. Enforce Atomic Tasks & Issue Creation
**File:** `enforce-atomic-tasks.kiro.hook`  
**Trigger:** File edited  
**File Pattern:** `specs/**/*.md`

Monitors spec files for task headings, validates they follow Conventional Commit format, and automatically creates or updates GitHub issues.

**Valid Conventional Commit Types:**
- `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `perf`, `ci`, `build`

**Examples:**
- `## feat(auth): add magic-link endpoint`
- `## test(user): validate email`

**Actions:**
- Scans for level-2 headings (`## ...`) in modified spec files
- Validates format: `## type(scope): description`
- Creates/updates GitHub issue with labels `["sdd-task", "status:planned"]`
- Links issue back to spec file
- Rejects invalid headings with correction guidance

### 3. Task Start → Auto-branch + Draft PR
**File:** `task-start-atomic-branch.kiro.hook`  
**Trigger:** File created  
**File Pattern:** `specs/tasks/**/*.md`

When a task file is created, automatically creates a Conventional Commit-compliant branch, pushes it upstream, and creates a linked draft PR.

**Branch Naming Rules:**
- Pattern: `^(feat|fix|refactor|test|chore|docs)/[a-z0-9-]+$`
- Max length: 50 characters
- No uppercase, spaces, underscores, special characters, or numbers at start

**Examples:**
- `feat(auth): add magic-link endpoint` → `feat/auth-magic-link-endpoint`
- `test(user): validate email` → `test/user-validate-email`

**Actions:**
- Parses task heading from new file
- Creates and checks out new branch
- Pushes branch with upstream tracking
- Creates draft PR with task heading as title
- Links GitHub issue to PR
- Confirms TDD cycle start

### 4. TDD Red Phase
**File:** `tdd-red-phase.kiro.hook`  
**Trigger:** User triggered (manual)

Enforces test-first development by blocking production code generation until tests are written and explicitly approved.

**Process:**
1. Generate comprehensive failing pytest tests in `tests/`
2. Cover: happy path, edge cases, error conditions, security
3. Show proof tests currently fail
4. PAUSE and request explicit approval (Y/N)
5. Block code generation until user replies Y

**Important:** Production code generation is completely blocked until test approval.

### 5. TDD Green Phase
**File:** `tdd-green-phase.kiro.hook`  
**Trigger:** User triggered (manual, after test approval)

Implements minimal passing code only after user approves the test suite.

**Process:**
1. Write smallest possible code in `src/` to make ALL tests pass
2. Run `uv run pytest -q`
3. Self-correct immediately if any test fails
4. Show final passing test output

### 6. TDD Refactor and Close
**File:** `tdd-refactor-close.kiro.hook`  
**Trigger:** User prompt (manual)

Final quality gate with automated merge sequence.

**Quality Checks:**
1. Run full lint + type check (ruff, mypy --strict)
2. Confirm test coverage ≥90% on new lines
3. Generate clean git diff
4. Suggest exact Conventional Commit message matching task heading

**Final Approval Gate:**
Asks user to confirm:
- Commit with Conventional Commit
- Squash-merge branch to main
- Close PR and GitHub issue
- Delete branch locally and remotely

Only executes auto-merge sequence via GitHub MCP if user replies Y.

### 7. Block Code Without Tests
**File:** `block-code-without-tests.kiro.hook`  
**Trigger:** File edited  
**File Pattern:** `src/**/*.py`

Safety net that warns and pauses when Python source files are created or modified without prior approved tests.

**Actions:**
- Detects code changes in `src/**/*.py`
- Checks for corresponding tests in `tests/`
- Warns: "Code without approved tests detected. This violates strict TDD."
- Prompts to abort or add tests first

## Workflow Summary

```
Spec Creation → GitHub Issue
     ↓
Task Start → Branch + Draft PR
     ↓
RED Phase → Write Tests (approval required)
     ↓
GREEN Phase → Minimal Code (tests pass)
     ↓
REFACTOR → Quality Checks (≥90% coverage)
     ↓
Final Approval → Auto-merge + Cleanup
```

## Conventional Commit Types

- `feat`: New feature
- `fix`: Bug fix
- `refactor`: Code restructuring
- `test`: Test additions/modifications
- `chore`: Maintenance tasks
- `docs`: Documentation updates

## Quality Standards

- **Test Coverage:** ≥90% on new code
- **Type Safety:** MyPy strict mode
- **Linting:** Ruff compliance
- **Commits:** Conventional Commits only
- **Merges:** Squash merge only
- **Branching:** One task per branch

## Configuration File

The complete hook definitions are maintained in `.kiro/hooks.yaml`. This README provides documentation and should be kept in sync with any changes to the YAML configuration.
