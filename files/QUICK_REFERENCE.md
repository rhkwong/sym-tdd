# TDD Quick Reference

## The Golden Rule

**Red → Green → Refactor**

## The Cycle (Every Feature)

1. Write a failing test (RED)
2. Write minimal code to pass (GREEN)
3. Clean up while tests pass (REFACTOR)
4. Commit and repeat

## When to TDD

| Scenario | TDD? |
|----------|------|
| New feature with clear requirements | Yes |
| Bug fix | Yes (write test that reproduces bug) |
| Complex logic/algorithms | Yes |
| Exploratory prototype | No |
| Simple CRUD, no business logic | No |
| UI layout/styling | No |

## Test Types

| Type | Speed | Mocking | Run When |
|------|-------|---------|----------|
| Unit | ms | Everything | Every save |
| Integration | seconds | Minimal | Before commit |
| E2E | minutes | None | Before release |

## AAA Pattern

```
Arrange - Set up test data
Act     - Execute ONE action
Assert  - Verify outcome
```

## FIRST Principles

- **F**ast - Milliseconds, not seconds
- **I**ndependent - No test depends on another
- **R**epeatable - Same result every time
- **S**elf-validating - Clear pass/fail
- **T**imely - Written before/with code

## Test Doubles

| Type | Use For |
|------|---------|
| Stub | Canned return values |
| Spy | Verifying calls happened |
| Mock | Specific interaction expectations |
| Fake | Simplified working implementation |

## Naming Tests

Pattern: `[unit]_[scenario]_[expectedResult]`

```
addItem_emptyCart_increasesCountToOne
applyDiscount_expiredCode_throwsError
```

## Anti-Patterns

- Testing implementation instead of behavior
- Over-mocking (mock boundaries, not everything)
- Flaky tests (use proper async handling)
- Multiple acts per test
- Skipping the RED phase

## Pre-Commit Checklist

- [ ] Test fails first (RED verified)
- [ ] Code is minimal (no extras)
- [ ] All tests pass
- [ ] Test names describe behavior
- [ ] AAA pattern followed
- [ ] No flaky tests
