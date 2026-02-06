# Code Review Checklist

A systematic guide for performing thorough code reviews. Use this checklist to ensure consistent, high-quality reviews across all pull requests.

## Before You Start

- Read the PR description and linked issues first
- Understand the intent and scope of the change
- Check if there are related PRs that should be reviewed together
- Pull the branch locally if the change is large or complex

## 1. Correctness

### Logic and Behavior

- [ ] Does the code do what the PR description says it does?
- [ ] Are edge cases handled (empty arrays, null values, zero, negative numbers)?
- [ ] Are boundary conditions correct (off-by-one errors, inclusive vs exclusive ranges)?
- [ ] Is error handling comprehensive? Are all failure modes accounted for?
- [ ] Are async operations properly awaited? No fire-and-forget promises?
- [ ] Are race conditions possible with concurrent operations?

### Data Integrity

- [ ] Are inputs validated before use?
- [ ] Are database transactions used where atomicity is needed?
- [ ] Is data sanitized before output (HTML escaping, SQL parameterization)?
- [ ] Are number operations safe from overflow or floating-point issues?

```typescript
// Bad - floating point comparison
if (total === 0.1 + 0.2) { /* ... */ }

// Good - use epsilon comparison or integer math
if (Math.abs(total - 0.3) < Number.EPSILON) { /* ... */ }
// Or: work in cents (integers) instead of dollars (floats)
```

## 2. Security

### Authentication and Authorization

- [ ] Are all endpoints properly authenticated?
- [ ] Is authorization checked (not just authentication)?
- [ ] Are sensitive operations protected against CSRF?
- [ ] Are JWT tokens validated correctly (expiry, issuer, audience)?

### Data Protection

- [ ] Are secrets stored in environment variables, not in code?
- [ ] Is sensitive data excluded from logs?
- [ ] Are passwords hashed with a proper algorithm (bcrypt, argon2)?
- [ ] Is PII handled according to data protection requirements?

### Input Validation

- [ ] Are all user inputs validated and sanitized?
- [ ] Are SQL queries parameterized (no string concatenation)?
- [ ] Is file upload content-type and size validated?
- [ ] Are URLs validated before redirects (open redirect prevention)?

```typescript
// Bad - SQL injection risk
const query = `SELECT * FROM users WHERE id = '${userId}'`;

// Good - parameterized query
const query = 'SELECT * FROM users WHERE id = $1';
const result = await db.query(query, [userId]);
```

## 3. Performance

### Algorithmic Efficiency

- [ ] Is the time complexity appropriate for the expected data size?
- [ ] Are there unnecessary nested loops (O(n^2) or worse)?
- [ ] Are database queries inside loops (N+1 query problem)?
- [ ] Is pagination used for potentially large result sets?

### Resource Management

- [ ] Are database connections/file handles properly closed?
- [ ] Are large objects released when no longer needed?
- [ ] Is caching used appropriately (and invalidated correctly)?
- [ ] Are expensive computations memoized where beneficial?

```typescript
// Bad - N+1 query problem
const users = await db.getUsers();
for (const user of users) {
  const orders = await db.getOrdersByUserId(user.id); // N queries
}

// Good - batch query
const users = await db.getUsers();
const userIds = users.map(u => u.id);
const orders = await db.getOrdersByUserIds(userIds); // 1 query
```

### Frontend Specific

- [ ] Are large lists virtualized?
- [ ] Are images optimized and lazy-loaded?
- [ ] Is bundle size impact considered (check for large dependency additions)?
- [ ] Are expensive re-renders prevented (proper memoization, key usage)?

## 4. Maintainability

### Readability

- [ ] Are variable and function names descriptive and consistent?
- [ ] Is the code self-documenting? (Comments explain "why", not "what")
- [ ] Are complex algorithms accompanied by explanatory comments?
- [ ] Is the code formatted consistently with the project style?

### Structure

- [ ] Does the change follow existing patterns in the codebase?
- [ ] Is the single responsibility principle followed?
- [ ] Are functions a reasonable length (under ~50 lines as a guideline)?
- [ ] Is there unnecessary code duplication?
- [ ] Are magic numbers replaced with named constants?

```typescript
// Bad - magic numbers
if (password.length < 8) { /* ... */ }
if (retryCount > 3) { /* ... */ }

// Good - named constants
const MIN_PASSWORD_LENGTH = 8;
const MAX_RETRY_ATTEMPTS = 3;

if (password.length < MIN_PASSWORD_LENGTH) { /* ... */ }
if (retryCount > MAX_RETRY_ATTEMPTS) { /* ... */ }
```

### Dependencies

- [ ] Are new dependencies justified? Is the package well-maintained?
- [ ] Could the functionality be implemented without adding a dependency?
- [ ] Are dependency versions pinned appropriately?
- [ ] Are there known security vulnerabilities in new dependencies?

## 5. Testing

### Coverage

- [ ] Are new features covered by tests?
- [ ] Are edge cases tested?
- [ ] Are error paths tested (not just happy paths)?
- [ ] Do tests cover the behavior described in the PR?

### Quality

- [ ] Are tests testing behavior, not implementation details?
- [ ] Are test names descriptive (describe what, not how)?
- [ ] Are tests independent (no shared mutable state between tests)?
- [ ] Are assertions specific (not just `toBeTruthy()` when exact value matters)?

```typescript
// Bad - testing implementation details
expect(component.state.isLoading).toBe(true);

// Good - testing behavior
expect(screen.getByRole('progressbar')).toBeInTheDocument();

// Bad - vague assertion
expect(result).toBeTruthy();

// Good - specific assertion
expect(result).toEqual({ id: '123', name: 'Alice', status: 'active' });
```

## 6. API Design

- [ ] Are API contracts backward compatible (or is breaking change documented)?
- [ ] Are error responses consistent and informative?
- [ ] Is the API documented (OpenAPI, JSDoc, or comments)?
- [ ] Are HTTP methods and status codes used correctly?
- [ ] Is versioning strategy followed?

## 7. Documentation

- [ ] Are public APIs documented with JSDoc or equivalent?
- [ ] Is the README updated if the change affects setup or usage?
- [ ] Are breaking changes documented in the changelog?
- [ ] Are complex business rules documented?

## Review Etiquette

### As a Reviewer

- Explain the "why" behind suggestions, not just the "what"
- Distinguish between blockers, suggestions, and nitpicks (prefix with labels)
- Ask questions instead of making assumptions about intent
- Acknowledge good solutions and patterns you learn from
- Be timely - aim to review within one business day

### Labels for Comments

- `blocker:` Must be fixed before merging
- `suggestion:` Recommended improvement, not required
- `nit:` Minor style or preference issue
- `question:` Seeking clarification about the approach
- `praise:` Highlighting particularly good code

### As an Author

- Keep PRs small and focused (under 400 lines changed is ideal)
- Write a thorough PR description explaining the change and testing done
- Respond to all comments (even if just to acknowledge)
- Do not take feedback personally - the goal is better code
