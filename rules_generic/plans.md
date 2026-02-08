# A.I Agent Instructions for Creating Implementation Plans

## **IMPORTANT**

These rules are **mandatory** and must be applied **strictly and consistently** when creating implementation plans.

---

## **Rules for Implementation Plans**

### **Rule 1: Split Plans into Logical Phases**

When asked to create implementation plans, always split the plan into **logical phases** that can be implemented sequentially.

**What Makes a Good Phase:**

- ✅ Represents a complete, cohesive unit of work
- ✅ Has clear start and end points
- ✅ Can be implemented and tested independently
- ✅ Builds upon previous phases
- ✅ Typically takes 2-5 tasks to complete

**Examples:**

❌ **Bad Phase Breakdown:**

- Phase 1: "Build everything"
- Phase 2: "Test and deploy"

✅ **Good Phase Breakdown (backend feature):**

- Phase 1: Define TypeScript interfaces and types
- Phase 2: Implement core service classes
- Phase 3: Add API routes and validation
- Phase 4: Create unit tests
- Phase 5: Add integration/E2E tests

✅ **Good Phase Breakdown (UI feature):**

- Phase 1: Define component props and types
- Phase 2: Implement base components
- Phase 3: Add state management and hooks
- Phase 4: Integrate with API layer
- Phase 5: Create component and integration tests

---

### **Rule 2: Define Phase Dependencies**

For each phase, explicitly define dependencies from the previous phase.

**How to Document Dependencies:**

```markdown
## Phase 2: Implement Core Service Classes

**Dependencies:**
- Phase 1 must be complete (TypeScript interfaces defined)
- Types from Phase 1.2 must be tested
- Base abstract classes from Phase 1.3 must be documented

**What This Phase Provides for Next Phase:**
- Complete service infrastructure
- Data validation capabilities
- Error handling patterns
```

---

### **Rule 3: Provide Context and Reasoning**

Provide detailed context and reasoning for each phase.

**What to Include:**

- **Why this phase is needed** — Business/technical justification
- **What problem it solves** — Specific issues being addressed
- **Key decisions made** — Architecture choices and rationale
- **Potential challenges** — Known risks or complexities
- **Success criteria** — How to verify phase completion

**Example:**

```markdown
## Phase 1: Authentication Service Type Safety

**Context:**
The current auth service lacks proper generic type constraints,
allowing invalid token payloads to pass type checking.

**Reasoning:**
Starting with type definitions allows us to:
1. Define clear contracts between the auth service and consuming components
2. Enable IDE autocomplete for token payload fields
3. Catch invalid token access patterns at compile-time

**Key Decision:**
Use TypeScript generics with conditional types to enforce
payload structure at the type level rather than runtime validation.
```

---

### **Rule 4: Define Clear Deliverables**

Each phase must have **clear, measurable deliverables**.

**Examples:**

❌ **Vague Deliverables:**

- "Backend improvements"
- "Better error handling"
- "Code cleanup"

✅ **Clear Deliverables:**

- Complete TypeScript interfaces for all service layer types
- User authentication endpoint with JWT token generation
- Error handling middleware that returns structured JSON errors
- 90%+ test coverage for the validation module

---

### **Rule 5: Create Granular Tasks**

**IMPORTANT:** Create small, **granular**, and manageable tasks. More tasks are better than a few large tasks.

**Task Granularity Guidelines:**

- Each task should be completable within **2-4 hours** of work
- Each task should touch **5-15 files maximum**
- Each task should have **one clear objective**
- Each task should produce **testable output**

**Examples:**

❌ **Too Large (Bad):**

- "Implement the user management system" (too broad)
- "Add authentication and authorization" (too vague)
- "Build data service layer with caching" (too complex)

✅ **Properly Granular (Good):**

- "Create UserService interface with generic type parameter"
- "Implement password hashing utility with bcrypt"
- "Add JWT token generation and validation methods"
- "Create unit tests for authentication middleware"
- "Write integration test for login → token → protected route flow"

---

### **Rule 6: Task Numbering Convention**

Tasks **must** have a sequence number in the format: `Task [Phase].[Number]`

**Format:**

```
Task 1.1, Task 1.2, Task 1.3  (Phase 1, tasks 1-3)
Task 2.1, Task 2.2, Task 2.3  (Phase 2, tasks 1-3)
```

**Example:**

```markdown
### Phase 1: Authentication Service

- Task 1.1: Create auth types and interfaces
- Task 1.2: Implement JWT token generation
- Task 1.3: Add password hashing utilities
- Task 1.4: Write unit tests for auth utilities

### Phase 2: API Routes

- Task 2.1: Create login endpoint with validation
- Task 2.2: Add protected route middleware
- Task 2.3: Integration test for auth flow
```

---

### **Rule 7: Task Presentation Format**

**IMPORTANT:** Place all tasks in a **table format** at the end of each plan with completion checkboxes.

**Required Format:**

```markdown
## Task Implementation Checklist

| Task | Description                          | Dependencies     | Status |
| ---- | ------------------------------------ | ---------------- | ------ |
| 1.1  | Create auth types and interfaces     | None             | [ ]    |
| 1.2  | Implement JWT token generation       | 1.1              | [ ]    |
| 1.3  | Add password hashing utilities       | 1.1              | [ ]    |
| 1.4  | Unit tests for auth utilities        | 1.1, 1.2, 1.3   | [ ]    |
| 2.1  | Create login endpoint                | Phase 1 complete | [ ]    |
| 2.2  | Add protected route middleware       | 2.1              | [ ]    |

**Legend:**
- [ ] Not started
- [x] Complete
```

---

### **Rule 8: Granular Testing Requirements**

**IMPORTANT:** It is critical to have **granular tests and testing** for each task.

**Testing Guidelines:**

1. **Each task must specify its testing requirements**

   ```markdown
   Task 1.2: Implement JWT token generation
   Tests: Unit tests for token creation, expiry validation, payload extraction
   Coverage: 100% for new public methods
   ```

2. **Test types per task:**
   - **Unit tests** — Test individual functions/classes (Vitest, no external services)
   - **Integration tests** — Test cross-module interactions (may need Docker for databases)
   - **End-to-end tests** — Test complete workflows (e.g., login → token → API call → response)
   - **Component tests** — Test React components in isolation (with Testing Library)

3. **Test granularity:**
   - Each task should add 5-20 test cases
   - Tests should be specific to that task's functionality
   - Tests should be automated and reproducible with `yarn test`

---

### **Rule 9: Pre-Implementation Re-evaluation**

**IMPORTANT:** Always re-evaluate the implementation plan before implementing, to be absolutely sure nothing was missed.

**Re-evaluation Checklist:**

1. **✅ Completeness** — Are all requirements covered?
2. **✅ Task Granularity** — Are tasks small enough (2-4 hours each)?
3. **✅ Dependencies** — Are all dependencies documented and logical?
4. **✅ Testing Coverage** — Does every task have testing requirements?
5. **✅ Consistency** — Do task numbers follow convention?
6. **✅ Feasibility** — Can this be implemented with current project infrastructure?
7. **✅ Module/Package Boundaries** — Are changes properly scoped to the right areas?

---

### **Rule 10: File Creation & Large Class Architecture**

**IMPORTANT:** When planning implementations, always consider AI context limitations.

**File Creation Rules:**

- ✅ Split files into smaller, logically grouped files to prevent AI context limits
- ✅ If creating a large class (>500 lines), use multi-chain inheritance technique
- ✅ Each layer in an inheritance chain: 200-500 lines maximum
- ✅ Maximum AI output limit: **60K tokens**. Maximum AI input limit: **200K tokens**

**Inheritance Chain Planning:**

```markdown
Phase 1: BaseService (core utilities, 200-300 lines)
Phase 2: AuthenticatedService extends BaseService (auth support, 200-300 lines)
Phase 3: CacheableService extends AuthenticatedService (caching, 200-300 lines)
Phase 4: UserService extends CacheableService (user-specific, 200-300 lines)
```

**When to Apply:**

- ✅ Any class approaching 500+ lines
- ✅ Complex systems with multiple concerns
- ✅ Systems that will grow significantly over time
- ❌ Simple utilities or data structures
- ❌ Classes with single, focused responsibilities
- ❌ React components (use composition with hooks instead)

---

## **Summary: Creating Effective Plans**

**Every implementation plan must include:**

1. 📋 **Logical phases** — Sequential, buildable units of work
2. 🔗 **Dependencies** — Clear phase and task dependencies
3. 💡 **Context & reasoning** — Why this approach, key decisions
4. ✅ **Clear deliverables** — Measurable outcomes for each phase
5. 🔨 **Granular tasks** — Small, focused, testable tasks (2-4 hours each)
6. 🔢 **Numbered tasks** — Format: Task [Phase].[Number]
7. 📊 **Table format** — All tasks in a table with checkboxes
8. 🧪 **Testing requirements** — Specific tests for each task
9. 🔍 **Pre-implementation review** — Verify completeness and consistency
10. 📦 **File size limits** — Split large files, use inheritance chains

---

## **Cross-References**

- See **agents.md** for task granularity requirements and verification rules
- See **code.md** for testing standards and quality guidelines
- See **code.md Rules 20-23** for inheritance chain architecture requirements
- See **testing.md** for test commands and workflow
- See **make_plan.md** for plan creation trigger and execution protocol
- See **git-commands.md** for commit workflow during plan execution
