# Coding Standards

## **IMPORTANT**

These rules are **mandatory** and must be applied **strictly and consistently** across the entire codebase.

---

## 1. Code Quality & Structure

1. **DRY Principle (Don't Repeat Yourself)**
   - Eliminate duplicated logic, constants, and patterns.
   - Extract reusable logic into functions, classes, hooks, or utilities.
   - If code looks similar in more than one place, refactor it.

2. **Clarity Over Cleverness**
   - Write code that is easy to read and reason about.
   - Prefer explicit, understandable logic over short or "smart" solutions.

3. **Single Responsibility**
   - Each function, method, or class must have **one clear responsibility**.
   - Avoid large functions that perform multiple unrelated tasks.

---

## 2. Testing Requirements

**📖 See `testing.md` for test commands and AI testing workflow.**

4. **All Tests Must Pass**
   - No code may be merged or delivered if **any test fails**.
   - If existing behavior changes, tests must be updated accordingly.
   - Run `clear && yarn build && yarn test` before completing any task.

5. **Tests Are Part of the Code**
   - Tests must be readable, meaningful, and maintained with the same care as production code.
   - Avoid flaky or unclear tests.

6. **Maximum Test Coverage**
   - Always create the maximum amount of possible tests.
   - Sophisticated and granular tests are essential.
   - Each function, method, and component should have multiple test cases covering:
     - Normal/happy path scenarios
     - Edge cases and boundary conditions
     - Error conditions and invalid inputs
     - Integration with other components

7. **End-to-End Testing**
   - Always create end-to-end tests where possible.
   - Test complete workflows (e.g., user action → API call → database → response → UI update).
   - Ensure the entire system works together correctly.

8. **Test Granularity**
   - Write granular, focused tests that test one thing at a time.
   - Each test should have a clear purpose and failure message.
   - Small, specific tests are easier to debug when they fail.

---

## 3. Documentation & Comments

9. **Mandatory Code Comments**
   - Comment _why_ something is done, not just _what_ is done.
   - Complex logic, edge cases, and non-obvious decisions must always be explained.

10. **Assume a Junior Developer as the Reader**
    - Write comments so that a junior developer can understand:
      - The intent of the code
      - The workflow
      - Any assumptions or constraints

11. **JSDoc Is Required**
    - Every public and protected class, method, function, and component must have JSDoc.
    - JSDoc must describe:
      - Purpose
      - Parameters
      - Return values
      - Side effects (if any)

---

## 4. Object-Oriented Rules

12. **No Private Class Members**
    - Do **not** use `private` methods or properties.
    - Methods and properties must be either:
      - `public`, or
      - `protected` (used instead of `private`)

13. **Encapsulation Through Convention**
    - `protected` members are considered internal and must not be accessed outside subclasses.
    - Document protected members clearly in JSDoc.

---

## 5. Maintainability First

14. **Code Must Be Easy to Maintain and Extend**
    - Optimize for long-term maintainability, not short-term speed.
    - Future changes should be easy and safe to implement.

15. **Consistency Is Non-Negotiable**
    - Follow existing patterns, naming conventions, and architecture.
    - Do not introduce new styles or patterns without a strong reason.

16. **Imports**
    - Never do dynamic imports like `var module = require('....')` or `{......} = require('.....')`
    - Always use ES module `import` statements at the top of the file
    - Use `import type { ... }` for type-only imports

---

## 6. Monorepo & Package Rules

> **Note:** These rules apply to monorepo projects (Yarn workspaces + Turbo). For single-repo projects, adapt the package boundary concept to module/directory boundaries.

17. **Respect Package Boundaries**
    - Import from package names (`@myorg/utils`), NOT from relative `dist/` paths
    - Never import from `../../other-package/dist/...`
    - Each package's `index.ts` defines its public API — only import what's exported there

    ❌ **Wrong:**
    ```typescript
    import { formatDate } from '../../utils/dist/formatters/date.js';
    ```

    ✅ **Correct:**
    ```typescript
    import { formatDate } from '@myorg/utils';
    ```

18. **Versioning**
    - Follow the project's versioning strategy (independent or lockstep)
    - Never manually change version numbers unless the project requires it
    - Use the project's configured version management tooling

19. **Package Dependencies**
    - `dependencies` = packages needed at runtime by consumers
    - `devDependencies` = packages needed only for development/testing
    - TypeScript, Vitest, build tools belong in `devDependencies`
    - Never put `typescript` or `prettier` in `dependencies`

---

## 7. Inheritance Chain Architecture

20. **MUST Use Inheritance Chains WHEN Implementation Exceeds 500 Lines**
    - When any implementation WILL exceed **500 lines** OR has multiple logical concerns
    - Break into inheritance chain: `Base → Layer1 → Layer2 → ... → Concrete`
    - Each layer: **200-500 lines maximum**
    - Natural dependency flow (each layer builds on previous)

21. **Inheritance Chain Design Principles**
    - **Foundation First**: Base class contains core utilities and infrastructure
    - **Logical Layers**: Each layer adds one primary concern
    - **Clean Dependencies**: Upper layers can use everything below them
    - **Protected Methods**: Use `protected` for inheritance, not composition
    - **Single Concrete**: Only the final class in chain should be concrete

22. **File Naming Conventions for Inheritance Chains**
    - `base.ts` — Foundation class with core utilities
    - `[feature].ts` — Specialized layers
    - `[main].ts` — Final concrete class
    - `index.ts` — Exports all layers for external use

    **Example (service layer):**
    ```
    base-service.ts → cacheable-service.ts → user-service.ts
    base-service.ts → cacheable-service.ts → product-service.ts
    base-controller.ts → auth-controller.ts → admin-controller.ts
    ```

23. **When to Use Inheritance Chains**
    - ✅ Service layers, middleware chains, controller hierarchies
    - ✅ Complex systems with natural layer dependencies
    - ✅ Any class approaching 500+ lines
    - ✅ Systems that will grow significantly over time
    - ❌ Simple utilities or data structures
    - ❌ Classes with single, focused responsibilities
    - ❌ React components (prefer composition with hooks instead)

---

## 8. TypeScript Best Practices

24. **No Inline Dynamic Imports for Types**
    - Always add proper import statements at the top of the file

    ❌ **Wrong:**
    ```typescript
    function example(expr: import('../core/types.js').Expression): void
    ```

    ✅ **Correct:**
    ```typescript
    import type { Expression } from '../core/types.js';
    function example(expr: Expression): void
    ```

25. **Use Proper Type Guards**
    - Use `instanceof` or custom type guard functions for type narrowing
    - Avoid `as any` type casting in production code

    ❌ **Wrong:**
    ```typescript
    if ((node as any).tableName) { ... }
    ```

    ✅ **Correct:**
    ```typescript
    function isTableNode(node: ASTNode): node is TableNode {
        return 'tableName' in node && typeof node.tableName === 'string';
    }

    if (isTableNode(node)) {
        node.tableName; // TypeScript knows this exists
    }
    ```

26. **No `as any` in Production Code**
    - Do **not** use `as any` to bypass TypeScript type checking
    - Use proper type guards, generics, or fix the underlying type issue
    - Test files may use `as any` sparingly for test setup, but prefer proper typing

27. **Complete Interface Compliance**
    - When creating objects that implement an interface, provide ALL required fields
    - Never use partial objects where full interfaces are expected without `Partial<T>`

---

## 9. React & Component Rules

> **Note:** These rules apply when the project includes React/UI components. Skip if the project is backend-only.

28. **Component Structure**
    - Use functional components with hooks — never class components
    - Keep components small and focused (under 200 lines)
    - Extract complex logic into custom hooks
    - Separate presentation from business logic

29. **Component Typing**
    - Always define explicit prop interfaces for components
    - Use `React.FC<Props>` or explicit return types
    - Export prop interfaces for reuse by consumers

    ```typescript
    /** Props for the UserCard component */
    interface UserCardProps {
        /** The user to display */
        user: User;
        /** Called when the edit button is clicked */
        onEdit?: (userId: string) => void;
    }

    /** Displays a user's profile card with optional edit action */
    export const UserCard: React.FC<UserCardProps> = ({ user, onEdit }) => {
        // ...
    };
    ```

30. **Hook Rules**
    - Custom hooks must start with `use` prefix
    - Keep hooks focused on a single concern
    - Document hook parameters and return values with JSDoc

---

## 10. Testing Integrity Rules

31. **Prefer Real Objects Over Mocks**
    - Use real implementations in tests when the real object exists
    - Helper functions for simple test data are acceptable
    - Only mock external services (databases, HTTP APIs) or when the real implementation is too complex to set up

    ❌ **Wrong (mocking what exists):**
    ```typescript
    const mockService = { getUser: () => ({}) } as any;
    ```

    ✅ **Correct (use real or proper test double):**
    ```typescript
    // For unit tests: test the logic directly
    const service = new UserService(testConfig);
    const result = await service.validateEmail('test@example.com');
    expect(result).toBe(true);

    // For integration tests: use real dependencies
    const db = new TestDatabase(testConfig);
    const service = new UserService(db);
    ```

---

## 11. Final Rule

32. **If in Doubt, Be Explicit**
    - Prefer more readable code, more comments, and clearer structure over fewer lines of code.

---

## **Cross-References**

- See **plans.md** for task-level testing breakdowns and implementation planning
- See **agents.md** for verification procedures and task completion criteria
- See **testing.md** for test commands and AI testing workflow
- See **git-commands.md** for git workflow instructions
- See **agents.md** for debugging rules (NO inline `node -e` scripts — ALWAYS create script files)
