# Test Writing Patterns

Patterns and principles for writing effective, maintainable tests.

## AAA Pattern (Arrange-Act-Assert)

Every test follows three phases:

```
1. Arrange - Set up test data and conditions
2. Act - Execute the code under test
3. Assert - Verify the expected outcome
```

### Example

```javascript
// WRONG: Phases mixed together
test('adds item to cart', () => {
  const cart = new Cart();
  cart.add({ id: 1, price: 10 });
  expect(cart.items.length).toBe(1);
  cart.add({ id: 2, price: 20 });
  expect(cart.total).toBe(30);
});

// RIGHT: Clear AAA separation
test('adds item to cart', () => {
  // Arrange
  const cart = new Cart();
  const item = { id: 1, name: 'Widget', price: 10 };

  // Act
  cart.add(item);

  // Assert
  expect(cart.items).toContain(item);
  expect(cart.total).toBe(10);
});
```

### Guidelines

- [ ] Arrange section creates all necessary preconditions
- [ ] Act section contains ONE action (the thing being tested)
- [ ] Assert section verifies the outcome
- [ ] Comments optional if sections are obvious

## FIRST Principles

Tests should be:

### Fast

Tests run in milliseconds, not seconds.

**Wrong**: Test hits real database, takes 5 seconds.

**Right**: Test uses in-memory store, runs in 5ms.

### Independent

Tests don't depend on other tests or shared state.

**Wrong**: Test B fails if Test A doesn't run first.

**Right**: Each test sets up its own state, cleans up after.

### Repeatable

Tests produce the same result every time.

**Wrong**: Test fails on Mondays (uses real date).

**Right**: Test injects a fixed date, passes always.

### Self-Validating

Tests report pass/fail automatically.

**Wrong**: Test logs output you must manually check.

**Right**: Test has explicit assertions that fail clearly.

### Timely

Tests are written before or alongside production code.

**Wrong**: Write tests weeks after shipping.

**Right**: Write test first (TDD) or immediately after implementation.

## Test Naming Conventions

### Use Descriptive Names

Name pattern: `[unit]_[scenario]_[expectedResult]`

```javascript
// WRONG: Vague names
test('cart test', () => {});
test('it works', () => {});
test('test1', () => {});

// RIGHT: Descriptive names
test('addItem_emptyCart_increasesItemCountToOne', () => {});
test('removeItem_lastItem_setsCartToEmpty', () => {});
test('applyDiscount_expiredCode_throwsError', () => {});
```

### Alternative Patterns

```javascript
// BDD style (describe/it)
describe('Cart', () => {
  describe('when adding an item', () => {
    it('should increase the item count', () => {});
    it('should update the total', () => {});
  });
});

// Should-style
test('should throw error when item not found', () => {});
test('should return empty array for new user', () => {});
```

### Naming Checklist

- [ ] Name describes what is being tested
- [ ] Name describes the scenario/conditions
- [ ] Name describes expected outcome
- [ ] Name is readable without seeing the code

## Test Doubles

Use fake implementations to isolate the code under test.

### Types of Test Doubles

| Type | Purpose | Verifies Behavior? |
|------|---------|-------------------|
| **Stub** | Returns canned responses | No |
| **Spy** | Records calls for later verification | Yes |
| **Mock** | Pre-programmed with expectations | Yes |
| **Fake** | Working implementation (simplified) | No |

### When to Use Each

**Stub**: When you need predictable return values.

```javascript
// Stub: Always returns the same user
const userService = {
  getUser: () => ({ id: 1, name: 'Test User' })
};
```

**Spy**: When you need to verify a method was called.

```javascript
// Spy: Tracks if sendEmail was called
const emailSpy = jest.spyOn(emailService, 'send');
resetPassword(user);
expect(emailSpy).toHaveBeenCalledWith(user.email);
```

**Mock**: When behavior depends on specific interactions.

```javascript
// Mock: Expects specific calls in specific order
const paymentMock = jest.fn()
  .mockReturnValueOnce({ success: true })
  .mockReturnValueOnce({ success: false });
```

**Fake**: When you need realistic but simple behavior.

```javascript
// Fake: In-memory database
class FakeUserRepository {
  users = [];
  save(user) { this.users.push(user); }
  findById(id) { return this.users.find(u => u.id === id); }
}
```

### Test Double Checklist

- [ ] Use stubs for simple return values
- [ ] Use spies to verify interactions
- [ ] Use mocks sparingly (they couple tests to implementation)
- [ ] Use fakes for complex dependencies (databases, APIs)

## Anti-Patterns to Avoid

### Testing Implementation, Not Behavior

**Wrong**: Assert internal state or private methods.

```javascript
// BAD: Testing internals
test('cart uses array internally', () => {
  const cart = new Cart();
  cart.add(item);
  expect(cart._internalItems[0]).toBe(item); // Private!
});
```

**Right**: Assert observable behavior.

```javascript
// GOOD: Testing behavior
test('cart contains added item', () => {
  const cart = new Cart();
  cart.add(item);
  expect(cart.contains(item)).toBe(true);
});
```

### Over-Mocking

**Wrong**: Mock everything, test nothing real.

```javascript
// BAD: Mocking the thing you're testing
const cart = { add: jest.fn(), total: jest.fn(() => 30) };
cart.add(item);
expect(cart.total()).toBe(30); // This tests nothing!
```

**Right**: Mock boundaries, test real logic.

```javascript
// GOOD: Mock external dependency, test real cart
const pricingService = { getPrice: () => 10 };
const cart = new Cart(pricingService);
cart.add(item);
expect(cart.total).toBe(10);
```

### Flaky Tests

**Wrong**: Tests that sometimes pass, sometimes fail.

```javascript
// BAD: Race condition, timing-dependent
test('async operation completes', () => {
  startAsyncOperation();
  expect(result).toBeDefined(); // Might not be done!
});
```

**Right**: Deterministic tests with proper async handling.

```javascript
// GOOD: Properly awaited
test('async operation completes', async () => {
  const result = await startAsyncOperation();
  expect(result).toBeDefined();
});
```

### Large Arrange Sections

**Wrong**: 50 lines of setup obscure what's being tested.

**Right**: Extract helpers, use factories, show only relevant setup.

```javascript
// GOOD: Factory hides irrelevant details
const user = createUser({ role: 'admin' }); // Only show what matters
```

### Multiple Acts

**Wrong**: Testing multiple behaviors in one test.

```javascript
// BAD: Testing add AND remove
test('cart operations', () => {
  cart.add(item);
  expect(cart.items.length).toBe(1);
  cart.remove(item);
  expect(cart.items.length).toBe(0);
});
```

**Right**: One test per behavior.

```javascript
// GOOD: Separate tests
test('add increases item count', () => {
  cart.add(item);
  expect(cart.items.length).toBe(1);
});

test('remove decreases item count', () => {
  cart.add(item);
  cart.remove(item);
  expect(cart.items.length).toBe(0);
});
```

