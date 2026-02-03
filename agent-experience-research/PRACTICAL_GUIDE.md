# Practical Guide: Mandatory AX Implementations

**What every repository must implement for effective AI agent collaboration**

This document contains mandatory requirements. All repositories should implement these practices.

---

## Table of Contents

1. [AGENTS.md File](#1-agentsmd-file)
2. [Repository Structure Documentation](#2-repository-structure-documentation)
3. [Code Style Examples](#3-code-style-examples)
4. [Boundary Definitions](#4-boundary-definitions)
5. [Testing Configuration](#5-testing-configuration)
6. [CI/CD Requirements](#6-cicd-requirements)
7. [Security Boundaries](#7-security-boundaries)
8. [Git Workflow Standards](#8-git-workflow-standards)

---

## 1. AGENTS.md File

**Priority: Critical | Effort: 30 minutes**

Every repository MUST have an `AGENTS.md` file at the root. This is the primary way agents understand your project.

### Minimum Required Sections

```markdown
# AGENTS.md

## Commands

Build and test commands that agents can execute:

- Build: `npm run build`
- Test: `npm test`
- Lint: `npm run lint`
- Type check: `npx tsc --noEmit`
- Single test file: `npm test -- path/to/file.test.ts`

## Tech Stack

Be specific with versions:

- Runtime: Node.js 20.x
- Language: TypeScript 5.3
- Framework: React 18.2
- Build: Vite 5.0
- Testing: Vitest 1.0
- Package Manager: pnpm 8.x

## Project Structure

- `src/` - Application source code
- `src/components/` - React components (one component per file)
- `src/hooks/` - Custom React hooks
- `src/services/` - API clients and business logic
- `src/types/` - TypeScript type definitions
- `src/utils/` - Shared utility functions
- `tests/` - Test files (mirrors src/ structure)
- `public/` - Static assets
- `scripts/` - Build and deployment scripts
```

### Why This Matters

- Agents read AGENTS.md at the start of every session
- Without it, agents spend tokens exploring your codebase
- Commands enable immediate build/test feedback loops
- Tech stack prevents version mismatches and deprecated patterns

---

## 2. Repository Structure Documentation

**Priority: High | Effort: 20 minutes**

Document your directory layout so agents know where to find and place code.

### Required: Directory Overview

Add to your AGENTS.md:

```markdown
## Directory Structure

```
project/
├── src/
│   ├── components/     # UI components
│   │   ├── common/     # Shared components (Button, Input, etc.)
│   │   ├── features/   # Feature-specific components
│   │   └── layouts/    # Page layouts
│   ├── hooks/          # Custom React hooks
│   ├── services/       # API clients, external integrations
│   ├── stores/         # State management (Zustand/Redux)
│   ├── types/          # TypeScript types and interfaces
│   └── utils/          # Pure utility functions
├── tests/
│   ├── unit/           # Unit tests
│   ├── integration/    # Integration tests
│   └── e2e/            # End-to-end tests
├── docs/               # Extended documentation
└── scripts/            # Build and deployment scripts
```
```

### Required: Key Entry Points

```markdown
## Key Files

- `src/main.tsx` - Application entry point
- `src/App.tsx` - Root component with routing
- `src/services/api.ts` - API client configuration
- `src/types/index.ts` - Shared type exports
- `vite.config.ts` - Build configuration
- `tsconfig.json` - TypeScript configuration
```

### For Monorepos

Include nested AGENTS.md files in each package:

```
monorepo/
├── AGENTS.md                 # Root instructions
├── packages/
│   ├── api/
│   │   └── AGENTS.md         # API-specific instructions
│   ├── web/
│   │   └── AGENTS.md         # Web app instructions
│   └── shared/
│       └── AGENTS.md         # Shared package instructions
```

---

## 3. Code Style Examples

**Priority: High | Effort: 20 minutes**

One code example is worth a thousand words of explanation. Show, don't tell.

### Required: Good/Bad Examples

Add to your AGENTS.md:

```markdown
## Code Style

### Components

```tsx
// GOOD - Explicit types, functional component
export function UserProfile({ userId }: { userId: string }) {
  const { data: user, isLoading } = useUser(userId);

  if (isLoading) return <Skeleton />;
  if (!user) return <NotFound />;

  return (
    <Card>
      <Avatar src={user.avatar} />
      <Text>{user.name}</Text>
    </Card>
  );
}

// BAD - Avoid these patterns
export default function({ userId }) {  // No default exports, no implicit any
  const [user, setUser] = useState();  // No untyped state
  useEffect(() => { /* fetch in useEffect */ }, []);  // Use React Query/SWR
}
```

### API Calls

```typescript
// GOOD - Use the API client with proper error handling
const user = await api.users.get(userId);

// BAD - Don't use fetch directly
const response = await fetch(`/api/users/${userId}`);
```

### Error Handling

```typescript
// GOOD - Explicit error types
function processPayment(amount: number): Result<Payment, PaymentError> {
  if (amount <= 0) {
    return err(new PaymentError('INVALID_AMOUNT', 'Amount must be positive'));
  }
  // ...
}

// BAD - Generic throws
function processPayment(amount: number) {
  if (amount <= 0) throw new Error('Invalid amount');  // No error type
}
```
```

### Why Examples Matter

Research from GitHub's analysis of 2,500+ repositories found:
> "One real code snippet demonstrating your style proves more effective than paragraphs describing conventions."

---

## 4. Boundary Definitions

**Priority: High | Effort: 15 minutes**

Explicitly define what agents should always do, should ask about, and should never do.

### Required: Three-Tier Boundaries

```markdown
## Boundaries

### Always (Do these without asking)
- Run tests before marking work complete
- Use TypeScript strict mode
- Handle errors explicitly with typed errors
- Add JSDoc comments for public functions
- Follow existing patterns in the codebase

### Ask First (Get approval before doing)
- Adding new dependencies
- Changing public API signatures
- Modifying database schemas or migrations
- Creating new top-level directories
- Changing authentication/authorization logic
- Modifying CI/CD configuration

### Never (Do not do under any circumstances)
- Commit secrets, credentials, or API keys
- Disable TypeScript strict checks
- Use `any` type (use `unknown` if truly needed)
- Skip tests to make CI pass
- Delete or modify test files without corresponding code changes
- Push directly to main/master branch
- Modify .env files
- Run commands with `--force` flags
```

### Why Boundaries Matter

Agents cannot infer the "stakes" of a task. Without explicit boundaries:
- They may skip tests to "save time"
- They may add dependencies that violate security policies
- They may make breaking API changes without realizing it

---

## 5. Testing Configuration

**Priority: High | Effort: 15 minutes**

Agents need clear testing commands and expectations.

### Required: Test Commands

```markdown
## Testing

### Commands

Run all tests:
```bash
npm test
```

Run tests in watch mode (during development):
```bash
npm test -- --watch
```

Run specific test file:
```bash
npm test -- src/services/auth.test.ts
```

Run tests matching pattern:
```bash
npm test -- --testNamePattern="should validate"
```

Check coverage (minimum 70%):
```bash
npm test -- --coverage
```

### Test Structure

Tests mirror the src/ directory:
- `src/services/auth.ts` → `tests/unit/services/auth.test.ts`
- `src/components/Button.tsx` → `tests/unit/components/Button.test.tsx`

### Test Pattern

Use Arrange-Act-Assert:

```typescript
describe('AuthService', () => {
  it('should reject invalid credentials', async () => {
    // Arrange
    const credentials = { email: 'test@example.com', password: 'wrong' };

    // Act
    const result = await authService.login(credentials);

    // Assert
    expect(result.success).toBe(false);
    expect(result.error?.code).toBe('INVALID_CREDENTIALS');
  });
});
```

### Coverage Requirements

- Minimum overall coverage: 70%
- New code must have tests
- Critical paths (auth, payments) require 90%+ coverage
```

---

## 6. CI/CD Requirements

**Priority: Medium | Effort: 1 hour**

Configure CI to provide fast, actionable feedback.

### Required: Basic CI Pipeline

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      # Fast checks first (fail fast)
      - name: Type Check
        run: npx tsc --noEmit

      - name: Lint
        run: npm run lint

      # Then tests
      - name: Test
        run: npm test -- --coverage

      - name: Coverage Check
        run: |
          COVERAGE=$(cat coverage/coverage-summary.json | jq '.total.lines.pct')
          if (( $(echo "$COVERAGE < 70" | bc -l) )); then
            echo "::error::Coverage ${COVERAGE}% is below 70% threshold"
            exit 1
          fi

      - name: Build
        run: npm run build
```

### Required: Actionable Error Output

Configure TypeScript for clear errors:

```json
// tsconfig.json
{
  "compilerOptions": {
    "pretty": true,
    "noErrorTruncation": true
  }
}
```

Configure ESLint for machine-readable output:

```json
// package.json
{
  "scripts": {
    "lint": "eslint . --format stylish --max-warnings 0"
  }
}
```

---

## 7. Security Boundaries

**Priority: Critical | Effort: 30 minutes**

Protect sensitive areas from agent access.

### Required: .gitignore for Secrets

```gitignore
# .gitignore - Secrets and credentials
.env
.env.*
!.env.example

*.pem
*.key
*.p12

credentials/
secrets/
.secrets/

# IDE and local config
.idea/
.vscode/settings.json
*.local
```

### Required: Document Forbidden Paths

```markdown
## Security

### Files Agents Must Not Access
- `.env*` files (except .env.example)
- `credentials/` directory
- Any `*.pem`, `*.key`, or `*.p12` files
- `config/production.json`
- `secrets/` directory

### Secrets Management
All secrets must be accessed via environment variables:

```typescript
// GOOD
const apiKey = process.env.API_KEY;

// BAD - Never hardcode
const apiKey = 'sk-1234567890abcdef';
```

### Agent Account Permissions
If using agent accounts:
- Development/staging access only
- No production deployment permissions
- Read-only access to monitoring systems
- No access to customer data
```

---

## 8. Git Workflow Standards

**Priority: Medium | Effort: 15 minutes**

Standardize how agents commit and manage code.

### Required: Commit Conventions

```markdown
## Git

### Commit Message Format

```
type(scope): brief description

[optional body with more details]
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `refactor`: Code restructuring (no behavior change)
- `test`: Adding or updating tests
- `chore`: Build, CI, or tooling changes

Examples:
- `feat(auth): add password reset flow`
- `fix(api): handle timeout errors in user service`
- `test(payments): add edge cases for refund logic`

### Rules
- Keep commits atomic (one logical change per commit)
- Run tests before committing
- Never use `--no-verify` flag
- Never force push to shared branches

### Dangerous Commands - NEVER Run
- `git reset --hard` (without explicit instruction)
- `git push --force`
- `git checkout .` (discards all changes)
- `git clean -fd`
```

---

## Implementation Checklist

Use this to verify your repository meets all requirements:

### Tier 1: Must Have Today
- [ ] AGENTS.md exists at repository root
- [ ] Build command documented and works
- [ ] Test command documented and works
- [ ] Tech stack listed with versions
- [ ] Directory structure documented

### Tier 2: Must Have This Week
- [ ] At least one good/bad code example
- [ ] Boundary definitions (Always/Ask/Never)
- [ ] Key entry points documented
- [ ] Test pattern example provided

### Tier 3: Must Have This Month
- [ ] CI pipeline with type check + lint + test
- [ ] Coverage threshold enforced (>70%)
- [ ] Security boundaries documented
- [ ] Git workflow standards defined
- [ ] Forbidden paths listed

---

## Template: Complete AGENTS.md

Copy and customize this template:

```markdown
# AGENTS.md

## Commands

- Build: `npm run build`
- Test: `npm test`
- Lint: `npm run lint`
- Type check: `npx tsc --noEmit`
- Dev server: `npm run dev`

## Tech Stack

- Node.js 20.x
- TypeScript 5.3
- [Your framework and version]
- [Your test framework and version]

## Project Structure

- `src/` - Source code
- `tests/` - Test files
- [Add your directories]

## Key Files

- `src/index.ts` - Entry point
- [Add your key files]

## Code Style

[Add one good/bad example for your most important pattern]

## Testing

Run tests:
```bash
npm test
```

Coverage requirement: 70%

## Boundaries

### Always
- Run tests before completing work
- Use TypeScript strict mode

### Ask First
- Adding new dependencies
- Changing public APIs

### Never
- Commit secrets
- Disable type checking
- Skip tests

## Git

Commit format: `type(scope): description`

Types: feat, fix, docs, refactor, test, chore

## Security

Do not access:
- `.env*` files
- `credentials/` directory
```

---

*For detailed best practices beyond these mandatory requirements, see [Best Practices](./BEST_PRACTICES.md).*
