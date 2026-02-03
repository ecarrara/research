# Best Practices for Agent Experience

**Detailed practices across 11 categories for optimizing AI agent collaboration**

This document provides in-depth guidance beyond the [mandatory requirements](./PRACTICAL_GUIDE.md). Use these practices to elevate your repository's Agent Experience.

---

## Table of Contents

1. [Repository Structure](#1-repository-structure)
2. [Agent Instructions (AGENTS.md)](#2-agent-instructions-agentsmd)
3. [Documentation for Agents](#3-documentation-for-agents)
4. [Task Design & Decomposition](#4-task-design--decomposition)
5. [Context Management](#5-context-management)
6. [Testing & Verification](#6-testing--verification)
7. [Git Workflow & Version Control](#7-git-workflow--version-control)
8. [CI/CD Integration](#8-cicd-integration)
9. [Security & Sandboxing](#9-security--sandboxing)
10. [Observability & Debugging](#10-observability--debugging)
11. [Human-in-the-Loop Patterns](#11-human-in-the-loop-patterns)

---

## 1. Repository Structure

### Why It Matters

Agents must efficiently locate relevant files without exhausting their context window on exploration. A predictable structure reduces tokens spent on navigation.

### Best Practices

#### Use Conventional Layouts

```
project/
├── AGENTS.md              # Agent instructions (root)
├── README.md              # Human documentation
├── src/                   # Source code
│   ├── components/        # UI components (if applicable)
│   ├── services/          # Business logic
│   ├── utils/             # Shared utilities
│   └── types/             # Type definitions
├── tests/                 # Test files (mirror src/ structure)
├── docs/                  # Extended documentation
├── scripts/               # Build/deployment scripts
└── .github/
    └── workflows/         # CI/CD definitions
```

#### Monorepo Pattern

```
monorepo/
├── AGENTS.md              # Root-level instructions
├── packages/
│   ├── api/
│   │   ├── AGENTS.md      # API-specific instructions
│   │   └── src/
│   ├── web/
│   │   ├── AGENTS.md      # Web-specific instructions
│   │   └── src/
│   └── shared/
│       ├── AGENTS.md      # Shared package instructions
│       └── src/
```

The OpenAI repository contains 88 AGENTS.md files, demonstrating this pattern at scale.

#### File Size Guidelines

| File Type | Recommended Max | Rationale |
|-----------|-----------------|-----------|
| Source files | 300 lines | Fits easily in context |
| Test files | 500 lines | May need more setup |
| Config files | 100 lines | Should be simple |
| Documentation | 1000 lines | Can be skimmed |

Files exceeding these limits should be split by responsibility.

---

## 2. Agent Instructions (AGENTS.md)

### Why It Matters

AGENTS.md is the only file that goes into every single conversation with the agent by default. It's your primary interface for communicating with agents.

### Best Practices

#### Place High-Frequency Information First

Agents reference commands frequently. Put them at the top:

```markdown
# AGENTS.md

## Commands

- Build: `npm run build`
- Test: `npm test -- --coverage`
- Lint: `npm run lint`
- Type check: `npx tsc --noEmit`
- Single test: `npm test -- --testPathPattern="filename"`
- Dev server: `npm run dev`
```

#### Be Specific About Versions

```markdown
## Tech Stack

- Runtime: Node.js 20.11.0
- Language: TypeScript 5.3.3
- Framework: React 18.2.0
- State: Zustand 4.5.0
- Testing: Vitest 1.2.0
- Build: Vite 5.0.12
```

Avoid version ranges like `^18.0.0`—agents may use outdated patterns.

#### Show, Don't Tell

Research from 2,500+ repositories:
> "One real code snippet demonstrating your style proves more effective than paragraphs describing conventions."

```markdown
## Code Style

### Component Pattern

```tsx
// CORRECT - Named export, explicit props type, hooks at top
export function UserCard({ user, onSelect }: UserCardProps) {
  const [isHovered, setIsHovered] = useState(false);
  const theme = useTheme();

  return (
    <Card onMouseEnter={() => setIsHovered(true)}>
      {/* content */}
    </Card>
  );
}

// INCORRECT - Default export, inline types, logic mixed with JSX
export default function({ user }) {
  return (
    <div onClick={() => { /* inline logic */ }}>
      {user?.name || 'Unknown'}
    </div>
  );
}
```

#### Keep It Concise

Frontier LLMs can follow 150-200 instructions consistently. Beyond that, instructions may be ignored or conflict.

**Signs your AGENTS.md is too long:**
- More than 500 lines
- Repeated information
- Edge cases that rarely occur
- Historical context that doesn't affect current work

---

## 3. Documentation for Agents

### Why It Matters

Agents interact with documentation differently than humans. They need machine-parseable formats and quick paths to relevant information.

### Best Practices

#### Create llms.txt

```markdown
# /llms.txt

# Project Name

> Brief one-line description

## Quick Start
- Install: `npm install`
- Run: `npm run dev`
- Test: `npm test`

## Documentation

- [API Reference](/docs/api.md): REST endpoints and schemas
- [Architecture](/docs/architecture.md): System design
- [Contributing](/CONTRIBUTING.md): Development workflow

## Key Concepts

- **Authentication**: JWT-based, tokens expire in 24h
- **Database**: PostgreSQL with Prisma ORM
- **Caching**: Redis for sessions and rate limiting
```

#### OpenAPI Best Practices

For APIs, rich descriptions are critical:

```yaml
paths:
  /users/{id}:
    get:
      summary: Get user by ID
      description: |
        Retrieves a user's complete profile including preferences
        and subscription status. Returns 404 if user doesn't exist
        or has been soft-deleted.

        **Rate Limit:** 100 requests/minute
        **Cache:** Response cached for 5 minutes
      parameters:
        - name: id
          in: path
          required: true
          description: |
            Unique user identifier in UUID v4 format.
            Example: 550e8400-e29b-41d4-a716-446655440000
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: User found and returned
          content:
            application/json:
              example:
                id: "550e8400-e29b-41d4-a716-446655440000"
                name: "Jane Doe"
                email: "jane@example.com"
        '404':
          description: User not found or deleted
```

---

## 4. Task Design & Decomposition

### Why It Matters

> "When AI tools try to handle complex, multi-step conversations, their performance drops significantly."

### Best Practices

#### Categorize Tasks by Type

| Type | Characteristics | Agent Performance | Examples |
|------|-----------------|-------------------|----------|
| Type 1: Narrow | Single-file, well-defined | Excellent | Unit tests, feature flags, boilerplate |
| Type 2: Contextual | Requires specific context | Good with samples | Debugging, refactoring, migrations |
| Type 3: Open-ended | Multi-file, architectural | Poor alone | Full features—decompose first |

#### Decompose Complex Tasks

**Bad:**
```
"Build user authentication with email verification"
```

**Good:**
```
Task 1: Create User model with email, passwordHash, verifiedAt fields
Task 2: Add POST /auth/register endpoint
Task 3: Add email verification token generation
Task 4: Add GET /auth/verify/:token endpoint
Task 5: Add POST /auth/login endpoint
Task 6: Write tests for each endpoint
Task 7: Add rate limiting to auth endpoints
```

#### Write Specifications for Features

```markdown
## Feature: Password Reset

### Requirements
- User can request reset via email
- Token expires after 1 hour
- Rate limit: 3 requests per hour per email
- Must invalidate existing sessions on reset

### API Contract

POST /auth/reset-request
  Body: { email: string }
  Response: { success: boolean, message: string }

POST /auth/reset-confirm
  Body: { token: string, newPassword: string }
  Response: { success: boolean }

### Database Changes
- Add `password_reset_tokens` table
- Fields: id, user_id, token_hash, expires_at, used_at

### Implementation Notes
- Use existing email service in src/services/email.ts
- Store tokens in database, not Redis (audit requirement)
- Log all reset attempts for security monitoring
```

Microsoft research found that explicit specifications reduced back-and-forth refinements by 68%.

---

## 5. Context Management

### Why It Matters

> "A context window is not just storage capacity. It is cognitive load."

Research shows 23% performance degradation when context exceeds 85% capacity.

### Best Practices

#### The Context Budget

| Category | Allocation | Purpose |
|----------|------------|---------|
| Repository overview | 5% (~7.5K tokens) | Orientation |
| Current task context | 20% (~30K tokens) | Immediate work |
| Relevant file contents | 40% (~60K tokens) | Working material |
| Reasoning headroom | 25% (~37.5K tokens) | Thinking space |
| Buffer | 10% (~15K tokens) | Unexpected needs |

**Total practical limit: ~150K tokens** (even with 200K+ context windows)

#### Create Repository Overviews

```markdown
## Repository Overview

### Architecture
- Monolithic Node.js backend with Express
- PostgreSQL database with Prisma ORM
- React frontend with Redux state management
- Redis for caching and session storage

### Request Flow
1. Request → Express middleware (auth, logging)
2. → Route handler
3. → Service layer (business logic)
4. → Repository layer (data access)
5. → Response

### Key Entry Points
- `src/server.ts` - Application bootstrap
- `src/routes/index.ts` - API route definitions
- `src/services/` - Core business logic
- `src/middleware/auth.ts` - Authentication

### Critical Patterns
- All database access through Prisma client
- Authentication via JWT middleware
- Errors bubble up to global handler in src/middleware/error.ts
- Background jobs use Bull queue in src/jobs/
```

#### Keep Files Focused

Split large files by responsibility:

```
# Before: src/services/user.ts (800 lines)

# After:
src/services/user/
├── index.ts          # Re-exports
├── create.ts         # User creation
├── update.ts         # Profile updates
├── auth.ts           # Authentication logic
├── verification.ts   # Email verification
└── types.ts          # User-related types
```

---

## 6. Testing & Verification

### Why It Matters

> "If you skip review, you don't eliminate work—you defer it."

Industry analysis found AI-generated code contained 1.7x more defects than human-written code.

### Best Practices

#### Provide Complete Test Commands

```markdown
## Testing

### Commands

```bash
# Run all tests
npm test

# Run with coverage
npm test -- --coverage

# Run specific file
npm test -- src/services/auth.test.ts

# Run tests matching pattern
npm test -- --testNamePattern="should authenticate"

# Watch mode for development
npm test -- --watch

# Run only changed files
npm test -- --onlyChanged
```

### Coverage Requirements
- Overall: minimum 70%
- New code: must have tests
- Critical paths (auth, payments): 90%+
```

#### Document Test Patterns

```markdown
### Test Structure

```typescript
describe('AuthService', () => {
  // Setup that applies to all tests
  let authService: AuthService;
  let mockUserRepo: MockUserRepository;

  beforeEach(() => {
    mockUserRepo = new MockUserRepository();
    authService = new AuthService(mockUserRepo);
  });

  describe('login', () => {
    it('should return user for valid credentials', async () => {
      // Arrange
      const user = createTestUser({ email: 'test@example.com' });
      mockUserRepo.findByEmail.mockResolvedValue(user);

      // Act
      const result = await authService.login({
        email: 'test@example.com',
        password: 'correctpassword'
      });

      // Assert
      expect(result.success).toBe(true);
      expect(result.user).toEqual(user);
    });

    it('should return error for invalid password', async () => {
      // Arrange
      const user = createTestUser();
      mockUserRepo.findByEmail.mockResolvedValue(user);

      // Act
      const result = await authService.login({
        email: user.email,
        password: 'wrongpassword'
      });

      // Assert
      expect(result.success).toBe(false);
      expect(result.error?.code).toBe('INVALID_CREDENTIALS');
    });
  });
});
```

#### Include E2E Tests

Anthropic's research found browser automation tests caught bugs that unit tests missed:

```markdown
### E2E Tests

```bash
# Run e2e tests
npm run test:e2e

# Run specific e2e test
npm run test:e2e -- --spec "auth.spec.ts"
```

E2E tests verify complete user flows:
- `tests/e2e/auth.spec.ts` - Login, logout, password reset
- `tests/e2e/checkout.spec.ts` - Complete purchase flow
- `tests/e2e/onboarding.spec.ts` - New user setup
```

---

## 7. Git Workflow & Version Control

### Why It Matters

> "Treat each agent session like a developer handoff rather than a continuous workflow."

Git provides essential recovery mechanisms for agents.

### Best Practices

#### Document Commit Conventions

```markdown
## Git Workflow

### Commit Message Format

```
type(scope): brief description

[optional body explaining why, not what]

[optional footer with breaking changes or issue refs]
```

### Types
| Type | Use For |
|------|---------|
| feat | New features |
| fix | Bug fixes |
| docs | Documentation only |
| refactor | Code changes that don't fix bugs or add features |
| test | Adding or updating tests |
| chore | Build, CI, tooling |
| perf | Performance improvements |

### Scopes (project-specific)
- auth, api, ui, db, config, deps

### Examples
```
feat(auth): add password reset flow

Users can now reset their password via email link.
Tokens expire after 1 hour for security.

Closes #123
```

```
fix(api): handle timeout in payment processor

The Stripe API occasionally times out under load.
Added retry logic with exponential backoff.
```
```

#### Define Dangerous Operations

```markdown
### Dangerous Git Commands

These commands can cause data loss. NEVER run without explicit instruction:

| Command | Risk | Alternative |
|---------|------|-------------|
| `git reset --hard` | Loses uncommitted changes | `git stash` first |
| `git push --force` | Overwrites remote history | `git push --force-with-lease` |
| `git checkout .` | Discards all changes | `git stash` |
| `git clean -fd` | Deletes untracked files | Review with `git clean -n` first |
| `git rebase -i` | Rewrites history | Only on local branches |
```

#### Progress Documentation Pattern

For multi-session work:

```markdown
### Progress Tracking

For tasks spanning multiple sessions, maintain PROGRESS.md:

```markdown
# Progress: [Feature Name]

## Current Session
- Working on: API endpoints
- Branch: feature/password-reset
- Status: In progress

## Completed
- [x] Database schema (commit abc123)
- [x] Token generation service (commit def456)

## Next Steps
- [ ] Email sending integration
- [ ] Frontend form

## Blockers
- Need SMTP credentials for email testing

## Notes
- Decided to use database for tokens (not Redis) per audit requirements
- Token format: UUID v4, hashed with bcrypt
```

---

## 8. CI/CD Integration

### Why It Matters

> "Much of the magic of agents comes from their ability to fix their own mistakes and iterate against error messages."

Fast, actionable CI feedback enables agent self-correction.

### Best Practices

#### Fail Fast

Order CI steps from fastest to slowest:

```yaml
jobs:
  quality:
    steps:
      # 1. Fastest checks first (seconds)
      - name: Type Check
        run: npx tsc --noEmit

      - name: Lint
        run: npm run lint

      # 2. Unit tests (seconds to minutes)
      - name: Unit Tests
        run: npm test -- --coverage

      # 3. Integration tests (minutes)
      - name: Integration Tests
        run: npm run test:integration

      # 4. Build (minutes)
      - name: Build
        run: npm run build

      # 5. E2E tests (slowest, minutes)
      - name: E2E Tests
        run: npm run test:e2e
```

#### Produce Actionable Errors

Configure tools for clear output:

```json
// tsconfig.json
{
  "compilerOptions": {
    "pretty": true,
    "noErrorTruncation": true
  }
}
```

```javascript
// eslint.config.js
export default {
  // Use stylish formatter for readable output
  // Set max-warnings to 0 to fail on warnings
};
```

#### Quality Gates

```yaml
- name: Coverage Gate
  run: |
    COVERAGE=$(jq '.total.lines.pct' coverage/coverage-summary.json)
    if (( $(echo "$COVERAGE < 70" | bc -l) )); then
      echo "::error file=coverage::Coverage ${COVERAGE}% below 70% threshold"
      exit 1
    fi
    echo "Coverage: ${COVERAGE}%"
```

---

## 9. Security & Sandboxing

### Why It Matters

> "AI-generated code must be treated as untrusted by default."

### Best Practices

#### Mandatory Security Controls

From the NVIDIA AI Red Team:

| Control | Implementation |
|---------|----------------|
| Network egress | Block arbitrary external connections |
| Filesystem isolation | Restrict writes to workspace only |
| Config protection | Block writes to hooks, CI configs |
| Lifecycle management | Clean up sessions, rotate credentials |

#### Agent Account Configuration

```markdown
## Security

### Agent Account Permissions

Development environment:
- Read/write to repository
- Execute build and test commands
- Access to development database
- No production access

Staging environment:
- Read/write to repository
- Deploy to staging only
- Access to staging database
- No production access

Production:
- No direct access
- Changes go through PR review
- Deployment requires human approval
```

#### Sandbox Configuration

For executing agent-generated code:

```yaml
# docker-compose.sandbox.yml
services:
  sandbox:
    image: your-app:sandbox
    security_opt:
      - no-new-privileges:true
    cap_drop:
      - ALL
    read_only: true
    tmpfs:
      - /tmp:size=100M
    networks:
      - sandbox-net
    deploy:
      resources:
        limits:
          cpus: '1'
          memory: 512M

networks:
  sandbox-net:
    driver: bridge
    internal: true  # No external network access
```

---

## 10. Observability & Debugging

### Why It Matters

> "When an agent fails in production, it rarely crashes outright. Instead, it may enter a loop, select the wrong tool, or produce an incorrect decision."

### Best Practices

#### Structured Logging

```typescript
// Log agent decisions with context
logger.info({
  event: 'agent_decision',
  taskId: task.id,
  step: 'tool_selection',
  selectedTool: 'file_edit',
  alternativesConsidered: ['file_create', 'file_delete'],
  confidence: 0.87,
  contextTokensUsed: 45000,
  reasoning: 'File exists, modification preferred over recreation'
});
```

#### Track Key Metrics

| Metric | Purpose |
|--------|---------|
| Tokens per task | Cost and efficiency |
| Task completion rate | Reliability |
| Time to completion | Performance |
| Retry count | Error patterns |
| Human intervention rate | Autonomy level |

#### Document Failure Modes

```markdown
## Troubleshooting Agent Issues

### Common Failure Patterns

**Loop Detection**
Symptom: Agent repeats same action without progress
Cause: Unclear success criteria or ambiguous state
Fix: Add explicit completion checks, clearer acceptance criteria

**Context Overflow**
Symptom: Agent "forgets" earlier instructions
Cause: Context window exhausted
Fix: Reduce file sizes, add summarization, use progressive disclosure

**Tool Misselection**
Symptom: Agent uses wrong tool for task
Cause: Ambiguous tool descriptions or overlapping capabilities
Fix: Clarify tool purposes in AGENTS.md, add constraints

**Hallucinated Dependencies**
Symptom: Agent imports non-existent modules
Cause: Outdated training data, missing documentation
Fix: Explicitly list available dependencies, pin versions
```

---

## 11. Human-in-the-Loop Patterns

### Why It Matters

> "Human-in-the-loop is not a temporary workaround—it's a long-term pattern for building AI agents we can trust."

### Best Practices

#### Define Risk Levels

```markdown
## Risk Classification

| Level | Operations | Approval Required |
|-------|------------|-------------------|
| Low | Read operations, formatting, comments | Auto-approve |
| Medium | File modifications, test changes | Notify, timeout approval |
| High | New dependencies, API changes | Explicit approval |
| Critical | Production config, auth changes, data migrations | Multi-person approval |
```

#### Gradual Trust Building

**Phase 1: Maximum Oversight (Week 1-2)**
- Approve all file modifications
- Approve all command executions
- Review every commit before push

**Phase 2: Relaxed Read Operations (Week 3-4)**
- Auto-approve file reads
- Auto-approve lint and type checks
- Approve writes and test runs

**Phase 3: Earned Trust (Month 2+)**
- Auto-approve test file changes
- Auto-approve documentation
- Approve production code changes
- Track which auto-approvals succeed

**Phase 4: Context-Aware (Month 3+)**
- Auto-approve based on file path (tests/, docs/)
- Auto-approve small changes (<50 lines)
- Require approval for high-risk patterns
- Maintain audit log

#### Checkpoint Implementation

```typescript
interface Checkpoint {
  operation: string;
  riskLevel: 'low' | 'medium' | 'high' | 'critical';
  description: string;
  autoApproveIf?: () => boolean;
  timeoutMs?: number;
  requiredApprovers?: number;
}

const checkpoints: Checkpoint[] = [
  {
    operation: 'file_write',
    riskLevel: 'medium',
    description: 'Modify source file',
    autoApproveIf: () =>
      currentFile.path.startsWith('tests/') ||
      currentFile.path.endsWith('.md'),
    timeoutMs: 300000
  },
  {
    operation: 'add_dependency',
    riskLevel: 'high',
    description: 'Add new package dependency',
    // Never auto-approve
  },
  {
    operation: 'modify_auth',
    riskLevel: 'critical',
    description: 'Change authentication logic',
    requiredApprovers: 2
  }
];
```

---

*For novel and emerging patterns beyond these established practices, see [Novel Insights](./NOVEL_INSIGHTS.md).*
