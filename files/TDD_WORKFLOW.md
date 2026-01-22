# TDD Workflow

A systematic approach to test-driven development: write the test first, then make it pass.

## The Red-Green-Refactor Cycle

```
1. RED: Write a failing test
2. GREEN: Write minimal code to pass
3. REFACTOR: Clean up while green
4. Repeat
```

## Phase 1: RED (Write Failing Test)

### Before Writing the Test

- [ ] Understand the requirement clearly
- [ ] Identify one specific behavior to test
- [ ] Know what inputs and outputs to expect

### Write the Test

- [ ] Test describes the behavior (not the implementation)
- [ ] Test is small and focused
- [ ] Test will fail for the right reason

### Verify the Failure

- [ ] Run the test
- [ ] Confirm it fails
- [ ] Confirm it fails for the expected reason (not syntax error)

**Test doesn't fail?** Either the behavior already exists, or your test is wrong.

## Phase 2: GREEN (Make It Pass)

### Write Minimal Code

- [ ] Only enough code to pass the test
- [ ] No extra features
- [ ] No premature optimization
- [ ] Hardcoding is acceptable temporarily

### Run and Verify

- [ ] Test passes
- [ ] No other tests broke
- [ ] Commit this working state

**Tests still failing?** Focus only on the current test. Don't fix multiple things at once.

## Phase 3: REFACTOR (Clean Up)

### Only While Green

- [ ] All tests currently pass
- [ ] Changes don't alter behavior
- [ ] Run tests after every change

### Common Refactorings

- Remove duplication
- Extract methods or functions
- Rename for clarity
- Simplify conditionals

**Tests go red during refactor?** Revert. Try a smaller step.

## When to Use TDD

### Use TDD For

- [ ] New features with clear requirements
- [ ] Bug fixes (write test that reproduces bug first)
- [ ] Complex logic or algorithms
- [ ] Code that will be maintained long-term

### Skip TDD For

- [ ] Exploratory prototypes you'll throw away
- [ ] Simple CRUD with no business logic
- [ ] UI layout and styling
- [ ] One-off scripts

**Unsure?** Default to TDD. It's easier to delete tests than debug untested code.

## Test Granularity Decision Tree

```
Is it a single function/method with no dependencies?
  YES -> Unit test

Does it involve multiple components working together?
  YES -> Integration test

Does it test user-facing behavior end-to-end?
  YES -> E2E test

Default -> Start with unit test, add integration if needed
```

### Unit Tests

- Fast (milliseconds)
- No external dependencies
- Test one thing in isolation
- Run frequently

### Integration Tests

- Test component interactions
- May use real databases or services
- Slower than unit tests
- Run before commits

### E2E Tests

- Test full user workflows
- Use real or near-real environment
- Slowest to run
- Run before releases

## Example: TDD in Action

**Requirement**: Order total with 10% discount over $100

```
RED:   test "order 110 gets 10% off" -> assert total == 99
       Run: FAILS (Order doesn't exist)

GREEN: class Order { total() = sum > 100 ? sum * 0.9 : sum }
       Run: PASSES

REFACTOR: Extract DISCOUNT_THRESHOLD, DISCOUNT_RATE constants
          Run: STILL PASSES -> Commit
```
