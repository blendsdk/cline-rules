# Git Commands & Workflow

## **IMPORTANT**

These rules are **mandatory** and must be applied **strictly and consistently**.

---

## **Cline Git Commands**

When the user provides these keywords, Cline should perform the following actions:

---

### `gitcm` — Git Commit with Message

**Steps:**

1. Stage all changes (`git add .`)
2. Create a detailed, descriptive commit message
3. Write commit message to temporary file (`/tmp/git_commit_msg.txt`)
4. Commit using the file (`git commit -F /tmp/git_commit_msg.txt`)
5. Clean up temporary file (`rm /tmp/git_commit_msg.txt`)

**Implementation:**

```bash
# Stage changes
clear && git add .

# Create commit message file
# (write the message using write_to_file tool to /tmp/git_commit_msg.txt)

# Commit using the file
git commit -F /tmp/git_commit_msg.txt

# Clean up
rm /tmp/git_commit_msg.txt
```

**Commit Message Format:**

```
feat(scope): brief description of change

- Specific change 1
- Specific change 2
- Tests added/updated
```

**Commit Prefixes:**

| Prefix | Usage |
|--------|-------|
| `feat(scope)` | New feature |
| `fix(scope)` | Bug fix |
| `refactor(scope)` | Code refactoring |
| `test(scope)` | Adding/updating tests |
| `docs(scope)` | Documentation changes |
| `chore(scope)` | Build, config, tooling |

**Determining Commit Scope:**

Use the package name, module name, or directory as the commit scope:

| Context | Scope Example |
|---------|---------------|
| Single package in monorepo | `feat(auth)`, `fix(utils)`, `refactor(api)` |
| React component | `feat(user-profile)`, `fix(nav-bar)` |
| Shared library | `feat(shared)`, `fix(common)` |
| Root/multiple packages | `chore(monorepo)` or `refactor(project)` |
| Single-repo project | `feat(core)`, `fix(api)`, `test(services)` |

---

### `gitcmp` — Git Commit, Rebase, and Push

**Steps:**

1. Perform full `gitcm` workflow (stage + commit with detailed message)
2. Pull and rebase if needed (`git pull --rebase`)
3. If no conflicts, push to remote (`git push`)
4. Report any conflicts for manual resolution

**Implementation:**

```bash
# Stage changes
clear && git add .

# Create commit message file
# (write the message using write_to_file tool to /tmp/git_commit_msg.txt)

# Commit using the file
git commit -F /tmp/git_commit_msg.txt

# Clean up
rm /tmp/git_commit_msg.txt

# Rebase and push
git pull --rebase
git push
```

---

## **Important Notes**

1. **Always perform `gitcmp` or `gitcm` in a new Cline task with context** when possible — this creates a clean task boundary for git operations.

2. **Always run tests before committing:**
   ```bash
   clear && yarn build && yarn test
   ```
   Only commit if tests pass.

3. **Never force-push** unless explicitly asked by the user.

4. **Multi-package/module changes** — When a commit spans multiple areas, use a broader scope:
   ```
   refactor(monorepo): update TypeScript target across all packages
   feat(project): add ESLint configuration
   ```

---

## **Cross-References**

- See **testing.md** for test commands to run before committing
- See **agents.md** for task completion and verification rules
- See **make_plan.md** for auto-commit rules during plan execution
