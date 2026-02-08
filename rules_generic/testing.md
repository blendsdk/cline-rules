# Testing Standards & Rules

## **IMPORTANT**

These rules are **mandatory** and must be applied **strictly and consistently** when working on this project.

---

## **Project Toolchain**

This project uses:
- **Yarn** — Package manager (workspaces for monorepos)
- **Turbo** — Build orchestration and caching
- **Vite** — Bundler and dev server
- **Vitest** — Test framework

### Project Structure

**Monorepo (Yarn workspaces + Turbo):**

```
packages/
├── core/          # Core business logic
├── utils/         # Shared utilities
├── api/           # API/backend layer
├── ui/            # UI components (React)
└── app/           # Main application
```

**Single repo:**

```
src/
├── components/    # React components
├── hooks/         # Custom React hooks
├── services/      # Business logic / API services
├── utils/         # Shared utilities
└── types/         # TypeScript type definitions
tests/
├── unit/          # Unit tests
├── integration/   # Integration tests
└── e2e/           # End-to-end tests
```

> **Note:** Adapt the structure above to match the actual project layout.

---

## **Rule 1: Test Commands**

### Standard Commands

| Command | Description |
|---------|-------------|
| `clear && yarn test` | Run ALL tests across all packages (via Turbo) |
| `clear && yarn build && yarn test` | Build then test (recommended for clean runs) |
| `clear && yarn workspace @myorg/<pkg> test` | Run tests for a specific package (monorepo) |
| `clear && yarn test:unit` | Run only unit tests (if configured) |
| `clear && yarn test:e2e` | Run only E2E tests (if configured) |

### Monorepo Package-Specific Commands

```bash
# Run tests for a specific package
clear && yarn workspace @myorg/<package-name> test

# Run fast tests (skip Docker/external services)
clear && yarn workspace @myorg/<package-name> test:fast

# Run full tests (with Docker if needed)
clear && yarn workspace @myorg/<package-name> test
```

### Important Notes

- **Always prefix commands with `clear &&`** for clean terminal output
- **Use YARN exclusively** — NEVER use `npm`, `npx`, or `pnpm`
- **Build before testing** when you've changed source files: `clear && yarn build && yarn test`
- Turbo caches builds — use `clear && yarn clean && yarn build && yarn test` for a fully clean run

---

## **Rule 2: When to Use Targeted vs Full Tests**

### Use Targeted Tests (single package/module) When:

- ✅ Working on a specific package or module
- ✅ Quick iteration during development
- ✅ Debugging a failing test in one area

### Use Full Tests (all packages) When:

- ✅ Before completing any task (`attempt_completion`)
- ✅ Before any git commit
- ✅ After changes that cross package/module boundaries
- ✅ After modifying shared packages (utils, core, types)
- ✅ Final verification of any implementation

**CRITICAL:** Always run `clear && yarn build && yarn test` before marking a task complete!

---

## **Rule 3: Test Framework — Vitest**

This project uses **Vitest** for all testing.

### Configuration Hierarchy

- **Root**: `vitest.config.ts` — shared defaults (typecheck enabled)
- **Package-level**: Some packages may have their own `vitest.config.ts`
- Package configs override root settings when running package-specific tests

### Test File Conventions

- Test files use the `.test.ts` or `.test.tsx` extension
- Standard test location: `tests/` directory (outside `src/`)
- Component tests may live alongside components: `Component.test.tsx`

### Writing Tests

```typescript
import { describe, test, expect } from 'vitest';

describe('ComponentName', () => {
    test('should do expected behavior', () => {
        // Arrange
        const input = createInput();

        // Act
        const result = processInput(input);

        // Assert
        expect(result).toBe(expectedValue);
    });
});
```

---

## **Rule 4: Docker-Based Integration Tests**

Some features require **Docker** for integration testing (databases, message queues, etc.):

### Docker Test Workflow

```bash
# Start services, run tests, stop services (handled by package test script)
clear && yarn workspace @myorg/<package-name> test

# Or manually:
clear && yarn workspace @myorg/<package-name> db:up
clear && yarn workspace @myorg/<package-name> test:fast
clear && yarn workspace @myorg/<package-name> db:down
```

### When Docker Is NOT Available

- Run only unit tests: `clear && yarn workspace @myorg/<pkg> test:fast`
- Skip Docker-dependent packages entirely
- Document that integration tests were skipped

---

## **Rule 5: Test Coverage Requirements**

When implementing new features:

1. **Unit Tests**: Required for all new functions/methods/components
2. **Integration Tests**: Required for component/module interactions
3. **End-to-End Tests**: Required for complete user workflows

### Coverage Goals

| Area | Minimum Coverage |
|------|-----------------|
| Core business logic | 90%+ |
| Shared utilities | 90%+ |
| API routes/controllers | 80%+ |
| React components | 80%+ |
| Integration/E2E | 60%+ |

Refer to `code.md` Rules 4-8 for detailed testing standards.

---

## **Rule 6: Test-Driven Development Workflow**

**Recommended workflow for AI agents:**

1. **Understand** the change needed
2. **Write/update tests first** (if adding functionality)
3. **Run targeted tests** during development: `clear && yarn workspace @myorg/<pkg> test`
4. **Implement the change**
5. **Verify targeted tests pass**
6. **Build all packages**: `clear && yarn build`
7. **Run full test suite**: `clear && yarn test`
8. **Only then** call `attempt_completion`

---

## **Rule 7: Debugging Test Failures**

When tests fail:

1. **Read the error message** — Vitest provides clear output with diffs
2. **Isolate the failure** — Run the specific package's tests
3. **Check related packages** — If you changed a shared package, test dependents
4. **Fix and verify** — Run targeted tests until passing
5. **Full verification** — Run `clear && yarn build && yarn test` before completing

### Dependency Chain Awareness

When working in a monorepo, always test downstream packages when changing upstream/shared code:

```
Example chain:
  utils → core → api → app
  utils → ui → app

If you change `utils`, test: utils, core, api, ui, app
If you change `ui`, test: ui, app
```

---

## **Rule 8: Test Patterns**

### Testing a Utility Function

```typescript
import { describe, test, expect } from 'vitest';
import { formatCurrency } from '../src/utils/format.js';

describe('formatCurrency', () => {
    test('formats positive amounts', () => {
        expect(formatCurrency(1234.56)).toBe('$1,234.56');
    });

    test('formats zero', () => {
        expect(formatCurrency(0)).toBe('$0.00');
    });

    test('handles negative amounts', () => {
        expect(formatCurrency(-50)).toBe('-$50.00');
    });
});
```

### Testing an API Endpoint (with supertest)

```typescript
import { describe, test, expect } from 'vitest';
import supertest from 'supertest';
import { createApp } from '../src/app.js';

describe('GET /api/users', () => {
    test('returns list of users', async () => {
        const app = createApp({ env: 'test' });
        const response = await supertest(app)
            .get('/api/users')
            .expect(200);

        expect(response.body).toBeInstanceOf(Array);
        expect(response.body[0]).toHaveProperty('id');
        expect(response.body[0]).toHaveProperty('name');
    });

    test('returns 401 without auth token', async () => {
        const app = createApp({ env: 'test' });
        await supertest(app)
            .get('/api/users')
            .expect(401);
    });
});
```

### Testing a React Component (with Testing Library)

```typescript
import { describe, test, expect } from 'vitest';
import { render, screen, fireEvent } from '@testing-library/react';
import { UserCard } from '../src/components/UserCard.js';

describe('UserCard', () => {
    const mockUser = { id: '1', name: 'John Doe', email: 'john@example.com' };

    test('renders user name', () => {
        render(<UserCard user={mockUser} />);
        expect(screen.getByText('John Doe')).toBeInTheDocument();
    });

    test('calls onEdit when edit button is clicked', () => {
        const onEdit = vi.fn();
        render(<UserCard user={mockUser} onEdit={onEdit} />);

        fireEvent.click(screen.getByRole('button', { name: /edit/i }));
        expect(onEdit).toHaveBeenCalledWith('1');
    });
});
```

### Testing a Custom Hook

```typescript
import { describe, test, expect } from 'vitest';
import { renderHook, act } from '@testing-library/react';
import { useCounter } from '../src/hooks/useCounter.js';

describe('useCounter', () => {
    test('initializes with default value', () => {
        const { result } = renderHook(() => useCounter(0));
        expect(result.current.count).toBe(0);
    });

    test('increments the counter', () => {
        const { result } = renderHook(() => useCounter(0));

        act(() => {
            result.current.increment();
        });

        expect(result.current.count).toBe(1);
    });
});
```

---

## **Summary**

| Situation | Command |
|-----------|---------|
| Quick dev iteration (single package) | `clear && yarn workspace @myorg/<pkg> test` |
| Quick dev iteration (single repo) | `clear && yarn test` |
| Before task completion | `clear && yarn build && yarn test` |
| Before git commit | `clear && yarn build && yarn test` |
| Clean rebuild + test | `clear && yarn clean && yarn build && yarn test` |
| Docker integration tests | `clear && yarn workspace @myorg/<pkg> test` |

**Remember:** Always use `yarn` commands. Never use `npm` or `npx`. Always prefix with `clear &&`.

---

## **Cross-References**

- See **code.md** for testing standards (Rules 4-8)
- See **agents.md** for shell command rules and task completion criteria
- See **git-commands.md** for git workflow instructions
