---
name: tdd-setup
description: Detect test infrastructure, report gaps, and optionally fix them to bootstrap TDD readiness.
allowed-tools: Read, Glob, Grep, Bash, Write, Edit
user-invocable: true
agent-invocable: true
model: sonnet
---

# TDD Setup Wizard

Execute all three phases in order.

## Phase 1 — Detect

Scan the project to build an inventory of test infrastructure. Do NOT modify any files.

### 1a — Detect language and framework

Check for these manifest files (stop at first match per language):

| File | Language |
|------|----------|
| `package.json` | JavaScript / TypeScript |
| `pyproject.toml`, `setup.py`, `setup.cfg` | Python |
| `go.mod` | Go |
| `Cargo.toml` | Rust |
| `pom.xml`, `build.gradle`, `build.gradle.kts` | Java / Kotlin |
| `Gemfile` | Ruby |
| `mix.exs` | Elixir |

### 1b — Detect test runner

Search for installed test runners based on the detected language:

| Language | Check for |
|----------|-----------|
| JS/TS | `jest`, `vitest`, `mocha`, `ava`, `tap` in devDependencies or dependencies |
| Python | `pytest`, `unittest`, `nose2` in pyproject.toml or requirements files |
| Go | Built-in (`go test`) — always present |
| Rust | Built-in (`cargo test`) — always present |
| Java/Kotlin | `junit`, `testng` in build files |
| Ruby | `rspec`, `minitest` in Gemfile |
| Elixir | Built-in (`mix test`) — always present |

### 1c — Detect test directory and files

Search for test files using common conventions:

- Directories: `test/`, `tests/`, `__tests__/`, `spec/`, `test_*/`
- File patterns: `*.test.*`, `*.spec.*`, `test_*.py`, `*_test.go`, `*_test.rs`

Count the total number of test files found.

### 1d — Detect test script

- JS/TS: Check `scripts.test` in `package.json`
- Python: Check for `[tool.pytest.ini_options]` in `pyproject.toml` or `pytest.ini`
- Go/Rust/Elixir: Built-in — always present
- Ruby: Check for `Rakefile` with test task
- General: Check `Makefile` for `test` target

### 1e — Detect coverage config

- JS/TS: Check for `jest --coverage` in test script, `c8`, `istanbul`/`nyc` in devDependencies, or `vitest` coverage config
- Python: Check for `coverage` or `pytest-cov` in dependencies
- Go: Built-in (`go test -cover`)
- Rust: Check for `cargo-tarpaulin` or `cargo-llvm-cov`

## Phase 2 — Report

Print a summary table with the detection results:

```
## TDD Readiness Report

| Check           | Status | Detail                    |
|-----------------|--------|---------------------------|
| Language        | ✓ / ✗  | <detected language>       |
| Test runner     | ✓ / ✗  | <runner name or "none">   |
| Test directory  | ✓ / ✗  | <path or "none">          |
| Test script     | ✓ / ✗  | <script or "none">        |
| Coverage config | ✓ / ✗  | <tool or "none">          |
| Test files      | ✓ / ✗  | <count> files found       |
```

Then apply this decision table:

| Condition | Recommendation |
|-----------|---------------|
| No test runner detected | Suggest installing one appropriate for the language |
| No test script | Offer to add a `test` script to the project manifest |
| No coverage config | Offer to add coverage configuration |
| No test directory | Offer to create the conventional test directory |
| Zero test files | Offer to scaffold a demo TDD test on an existing source function |
| All checks pass | Report "TDD infrastructure is ready. Run `/tdd-setup` again after changes." |

## Phase 3 — Act

**STOP and ask the user for approval before making any changes.**

Present the list of proposed actions from Phase 2 and wait for confirmation. Only proceed with actions the user approves.

Possible actions (apply only those relevant to detected gaps):

| Gap | Action |
|-----|--------|
| No test runner | Print install command (e.g., `npm install -D vitest`) — do NOT run it automatically |
| No test script | Add a `test` script entry to the project manifest |
| No coverage config | Add minimal coverage configuration to the test runner config |
| No test directory | Create the conventional test directory for the language |
| Zero test files | Scaffold one example test file that imports an existing source function, writes a failing test (Red), and includes a comment explaining the next TDD step (Green) |

After completing approved actions, output:

> **Next steps:** Run your test command to verify the setup, then follow `.claude/docs/tdd/QUICK_REFERENCE.md` for the Red-Green-Refactor workflow.
