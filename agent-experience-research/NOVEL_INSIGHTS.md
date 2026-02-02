# Novel Insights: Under-Documented Dimensions of Agent Experience

**Advanced patterns and emerging concepts for senior engineers**

This document explores dimensions of Agent Experience that are not yet widely discussed but emerged consistently across research sources. These represent the frontier of AX thinking.

---

## Table of Contents

1. [Ambiguity Surfaces](#1-ambiguity-surfaces)
2. [Feedback Latency](#2-feedback-latency)
3. [Cognitive Load Budgeting](#3-cognitive-load-budgeting)
4. [Trust Calibration](#4-trust-calibration)
5. [Recovery State Architecture](#5-recovery-state-architecture)
6. [Toolability vs. Documentation](#6-toolability-vs-documentation)
7. [Time-to-First-Action](#7-time-to-first-action)
8. [Semantic Drift](#8-semantic-drift)

---

## 1. Ambiguity Surfaces

### What It Is

**Ambiguity surfaces** are areas in your codebase or instructions where multiple valid interpretations exist. When agents encounter ambiguity, they make decisions that cascade through subsequent choices, often leading to outcomes that technically satisfy the request but miss the intent.

### Why It Matters

Microsoft research found that explicit specifications reduced back-and-forth refinements by 68%. Ambiguity doesn't just slow agents down—it compounds:

```
Initial ambiguity → Agent decision A → Decision B depends on A → Decision C depends on B
                    (2 interpretations)  (2 more each)          (2 more each)
                    = 2 paths            = 4 paths              = 8 divergent paths
```

By the third decision point, you have 8 possible outcomes, most of which weren't intended.

### Common Ambiguity Sources

| Source | Example | Resolution |
|--------|---------|------------|
| Implicit conventions | "Follow best practices" | Document your specific practices |
| Overloaded terms | "service" (API? business logic? daemon?) | Define terms in glossary |
| Missing constraints | "Add validation" | Specify which fields, what rules |
| Assumed context | "Like the other endpoints" | Reference specific file and pattern |

### How to Address

#### 1. Disambiguation Rules in AGENTS.md

```markdown
## Disambiguation

When facing ambiguity, prefer:
- Creating new files over modifying existing ones
- Adding tests over assuming correctness
- Asking for clarification over guessing
- Smaller changes over comprehensive refactors
- Explicit types over inferred types

When unclear which pattern to follow:
1. Check the most recently modified similar file
2. If still unclear, ask before proceeding
```

#### 2. Confidence Thresholds

Define when agents should pause:

```markdown
## Confidence Guidelines

**Proceed without asking** when:
- Task matches an existing pattern exactly
- Only one reasonable interpretation exists
- Changes are easily reversible (tests, docs)

**Ask for clarification** when:
- Multiple valid approaches exist
- Changes affect public APIs
- Requirements seem contradictory
- Task scope is unclear

**Stop and report** when:
- No matching patterns exist
- Requirements conflict with existing code
- Security implications are unclear
```

#### 3. Explicit Glossary

```markdown
## Terminology

| Term | Meaning in This Codebase |
|------|--------------------------|
| Service | Business logic layer (src/services/), NOT API routes |
| Handler | API route handler (src/routes/**/handler.ts) |
| Model | Database entity (Prisma schema), NOT domain model |
| Repository | Data access layer, thin wrapper around Prisma |
| DTO | Data transfer object for API requests/responses |
```

---

## 2. Feedback Latency

### What It Is

**Feedback latency** is the time between an agent taking an action and receiving feedback about its correctness. Agents with immediate feedback dramatically outperform those waiting for slow CI or human review.

### Why It Matters

> "Much of the magic of agents comes from their ability to fix their own mistakes and iterate against error messages."

The feedback loop is the agent's learning mechanism within a session. Slow feedback means:
- More tokens spent on uncertain exploration
- Higher chance of cascading errors
- Reduced ability to self-correct

### Optimal Feedback Hierarchy

| Feedback Type | Target Latency | Purpose |
|--------------|----------------|---------|
| Type errors | <1 second | Catch obvious mistakes immediately |
| Lint errors | <1 second | Enforce style in real-time |
| Unit tests | <10 seconds | Validate behavior |
| Integration tests | <1 minute | Verify component interaction |
| Full CI suite | <5 minutes | Complete validation |
| Human review | Avoid for routine | Reserve for judgment calls |

### How to Optimize

#### 1. IDE-Level Feedback

Ensure type checking runs on every keystroke:

```json
// tsconfig.json
{
  "compilerOptions": {
    "incremental": true,
    "tsBuildInfoFile": ".tsbuildinfo"
  }
}
```

#### 2. Fast Test Subsets

```markdown
## Testing

### Fast Feedback Loop

For immediate feedback during development:
```bash
# Run only tests for changed files (< 5 seconds)
npm test -- --onlyChanged

# Run tests matching current work
npm test -- --testPathPattern="auth"
```

Save full suite for pre-commit:
```bash
npm test -- --coverage
```
```

#### 3. Pre-commit Hooks

```bash
# .husky/pre-commit
#!/bin/sh
npm run lint-staged
npm run typecheck
npm test -- --onlyChanged --passWithNoTests
```

#### 4. Avoid Human-Blocking Loops

Identify operations that don't need human approval:
- Running read-only queries
- Executing tests
- Generating documentation
- Formatting code
- Running lint fixes

---

## 3. Cognitive Load Budgeting

### What It Is

**Cognitive load budgeting** means actively managing what information is in the agent's context to preserve reasoning capacity. It's the recognition that context window size ≠ capability.

### Why It Matters

Research findings:
- 23% performance degradation at 85% context utilization (Carnegie Mellon)
- Claude Code triggers auto-compact at 75% context (Anthropic)
- Performance is best when relevant information is at beginning or end of context

> "Providing a 2M-token context to an LLM is like handing a new developer 10,000 pages of documentation on day one and expecting them to fix a bug in five minutes."

### The Budget Framework

**Target: 150K tokens maximum** (75% of 200K context window)

| Category | Allocation | Tokens | Content |
|----------|------------|--------|---------|
| Repository overview | 5% | ~7.5K | Architecture, entry points, patterns |
| Task instructions | 10% | ~15K | Current task, acceptance criteria |
| Relevant files | 40% | ~60K | Files being modified + dependencies |
| Conversation history | 15% | ~22.5K | Important prior context |
| Reasoning headroom | 25% | ~37.5K | Space for thinking |
| Buffer | 5% | ~7.5K | Unexpected needs |

### How to Implement

#### 1. Progressive Context Disclosure

Don't front-load everything. Provide context as needed:

```markdown
## Context Strategy

### Always Included (7.5K budget)
- AGENTS.md
- Repository overview
- Current task description

### On-Demand (request when needed)
- Specific file contents
- Test files
- Related components
- Historical context

### Never Include Wholesale
- Entire directories
- All test files
- Full git history
- Complete documentation
```

#### 2. Hierarchical Summaries

Create summary layers for large codebases:

```markdown
## Architecture Overview (Level 0 - always available)
- Express backend, React frontend, PostgreSQL database
- Key entry: src/server.ts → routes → services → repositories

## Module Summaries (Level 1 - available on request)

### Auth Module
- Purpose: User authentication and authorization
- Entry: src/services/auth/index.ts
- Key files: login.ts, register.ts, middleware.ts
- Dependencies: bcrypt, jsonwebtoken

### Payments Module
- Purpose: Payment processing via Stripe
- Entry: src/services/payments/index.ts
- Key files: checkout.ts, webhook.ts, refund.ts
- Dependencies: stripe-sdk
```

#### 3. Context Pruning Signals

Tell agents when to drop context:

```markdown
## Context Management

### Drop from context when:
- File hasn't been referenced in 3+ turns
- Test passed and isn't being modified
- Documentation was only for reference

### Keep in context:
- Files actively being modified
- Error messages being addressed
- Requirements under discussion
```

---

## 4. Trust Calibration

### What It Is

**Trust calibration** is the agent's model of how much autonomy it has for different types of operations. Without explicit calibration, agents cannot infer the "stakes" of a task.

### Why It Matters

Uncalibrated agents exhibit two failure modes:
1. **Over-caution**: Asking permission for trivial operations, wasting human attention
2. **Over-confidence**: Making risky changes without verification

Both waste resources and erode trust in the system.

### The Stakes Framework

```markdown
## Task Stakes

### Low Stakes (High Autonomy)
Operations that are:
- Easily reversible
- Affect only developer experience
- Have no production impact
- Match established patterns exactly

Examples:
- Formatting changes
- Adding comments
- Renaming local variables
- Adding test cases
- Documentation updates

**Agent behavior**: Proceed without asking, commit freely

### Medium Stakes (Verify Before Completing)
Operations that are:
- Somewhat reversible
- Affect code behavior
- Require testing to validate
- Extend existing patterns

Examples:
- New feature implementation
- Bug fixes
- Refactoring
- Dependency updates (patch versions)

**Agent behavior**: Complete work, run tests, request review before merge

### High Stakes (Explicit Approval Required)
Operations that are:
- Difficult to reverse
- Affect multiple systems
- Have compliance implications
- Require architectural judgment

Examples:
- Database schema changes
- API contract changes
- Authentication/authorization changes
- Dependency updates (major versions)
- New external service integrations

**Agent behavior**: Propose approach, wait for approval, implement with oversight

### Critical Stakes (Multi-Person Approval)
Operations that:
- Cannot be reversed easily
- Affect production data
- Have legal/compliance implications
- Could cause data loss or security breach

Examples:
- Production deployment
- Data migrations on production
- Security credential changes
- Deleting production resources

**Agent behavior**: Never proceed autonomously, require explicit human execution
```

### Implementation

```typescript
type Stakes = 'low' | 'medium' | 'high' | 'critical';

function classifyOperation(op: Operation): Stakes {
  // File path-based classification
  if (op.path.includes('/tests/') || op.path.endsWith('.md')) {
    return 'low';
  }

  if (op.path.includes('/migrations/') || op.path.includes('/auth/')) {
    return 'high';
  }

  // Operation type-based classification
  if (op.type === 'delete' && op.lineCount > 100) {
    return 'high';
  }

  if (op.type === 'create_file' && op.path.includes('/config/')) {
    return 'high';
  }

  // Default
  return 'medium';
}
```

---

## 5. Recovery State Architecture

### What It Is

**Recovery state architecture** refers to systems that enable agents to recover from failures and resume work across sessions. Unlike human developers who maintain mental context, agents start fresh each session.

### Why It Matters

> "Starting over is the right answer a lot more often with agents than with humans."

Without recovery architecture:
- Failed sessions lose all progress
- Debugging requires re-discovering context
- Long tasks cannot span multiple sessions
- Errors compound without clean restart points

### Core Components

#### 1. Git as Recovery Mechanism

```markdown
## Recovery Protocol

### Every Meaningful Change
1. Verify tests pass
2. Commit with descriptive message
3. Push to feature branch

### Recovery Commands
```bash
# See what was done
git log --oneline -10

# Revert last change if broken
git revert HEAD

# Go back to known good state
git reset --soft HEAD~1  # Keep changes unstaged
git checkout -- .         # Discard changes
```

### Commit Checkpoints
Create commits at natural stopping points:
- After each function is complete and tested
- Before attempting risky refactors
- When switching between components
```

#### 2. Progress Documentation

```markdown
# PROGRESS.md Template

## Session: [Date/ID]

### Objective
[What we're trying to accomplish]

### Completed This Session
- [x] Task 1 (commit: abc123)
- [x] Task 2 (commit: def456)
- [ ] Task 3 (in progress)

### Current State
- Branch: feature/xyz
- Last successful test run: [commit]
- Build status: passing/failing

### Blockers
- [Any issues preventing progress]

### Context for Next Session
- Currently working on: [specific file/function]
- Key decisions made: [architectural choices]
- Watch out for: [known issues, gotchas]

### Files Modified
- src/services/auth.ts - Added password reset
- tests/auth.test.ts - Added test cases
```

#### 3. Clean State Requirements

Each session should end with:
- All tests passing
- No uncommitted changes
- Progress documented
- Clear next steps identified

```markdown
## Session End Checklist

Before ending a session:
- [ ] All changes committed
- [ ] Tests pass
- [ ] PROGRESS.md updated
- [ ] Branch pushed to remote
- [ ] No temporary debug code
- [ ] No TODO comments without ticket references
```

---

## 6. Toolability vs. Documentation

### What It Is

The shift from optimizing for human-readable documentation to machine-operable interfaces. Agents interact with systems differently than developers reading docs.

### Why It Matters

> "A well-structured REST API is the best 'SDK' for an AI Agent."

Key insight: Agents can write their own SDKs if needed. What they can't do is infer undocumented behavior.

### The Hierarchy of Agent Accessibility

| Level | Format | Agent Usability |
|-------|--------|-----------------|
| 1 | OpenAPI spec with rich descriptions | Excellent |
| 2 | Structured data (JSON, YAML) | Very Good |
| 3 | Markdown with consistent patterns | Good |
| 4 | Prose documentation | Moderate |
| 5 | Comments in code | Poor |
| 6 | Tribal knowledge | Unusable |

### How to Optimize

#### 1. OpenAPI First

```yaml
# Rich descriptions, not just field names
parameters:
  - name: status
    in: query
    description: |
      Filter orders by status. Multiple statuses can be provided
      as comma-separated values.

      Valid values:
      - pending: Order received, not yet processed
      - processing: Payment confirmed, preparing shipment
      - shipped: Order dispatched, in transit
      - delivered: Order received by customer
      - cancelled: Order cancelled (see cancellation_reason)

      Example: ?status=pending,processing
    schema:
      type: string
      enum: [pending, processing, shipped, delivered, cancelled]
```

#### 2. Structured Error Responses

```typescript
// Instead of: throw new Error("Invalid input")

// Use structured errors:
interface ApiError {
  code: string;           // Machine-readable: "INVALID_EMAIL_FORMAT"
  message: string;        // Human-readable: "Email address is not valid"
  field?: string;         // Which field: "email"
  suggestion?: string;    // How to fix: "Use format: user@domain.com"
  documentation?: string; // Where to learn more
}
```

#### 3. Machine-Readable Configuration

```json
// package.json - good
{
  "scripts": {
    "test": "vitest",
    "test:coverage": "vitest --coverage",
    "test:watch": "vitest --watch"
  }
}

// vs README - less good
"To run tests, use vitest. For coverage, add the --coverage flag..."
```

---

## 7. Time-to-First-Action

### What It Is

**Time-to-first-action** measures how quickly an agent can start productive work in your repository. It's the agent equivalent of developer onboarding time.

### Why It Matters

> "Agents should be able to get up and running in milliseconds. Agents are simply not going to 'contact sales' or 'book a demo'."

Slow time-to-first-action means:
- Wasted tokens on setup and exploration
- Higher chance of wrong assumptions
- Frustrated users waiting for results

### Optimization Checklist

| Factor | Target | Optimization |
|--------|--------|--------------|
| Clone to build | <60 seconds | Cached dependencies, minimal setup |
| Build to test | <30 seconds | Incremental builds, fast test subset |
| Context acquisition | <10 seconds | AGENTS.md at root, clear structure |
| First meaningful action | <2 minutes | Clear task decomposition |

### How to Achieve

#### 1. Zero-Config Setup

```bash
# Ideal: single command setup
npm install && npm run dev

# Document any prerequisites clearly
## Prerequisites
- Node.js 20+ (check: node --version)
- PostgreSQL 15+ (check: psql --version)
- Redis 7+ (check: redis-cli ping)
```

#### 2. Pre-configured Environments

```yaml
# docker-compose.dev.yml
services:
  app:
    build: .
    volumes:
      - .:/app
    environment:
      - DATABASE_URL=postgres://dev:dev@db:5432/dev
      - REDIS_URL=redis://redis:6379
    depends_on:
      - db
      - redis

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: dev
      POSTGRES_PASSWORD: dev
      POSTGRES_DB: dev

  redis:
    image: redis:7
```

#### 3. Sensible Defaults

```typescript
// config/default.ts
export const config = {
  // Works out of the box for development
  database: {
    url: process.env.DATABASE_URL || 'postgres://localhost:5432/dev'
  },
  server: {
    port: process.env.PORT || 3000
  },
  // Don't require manual configuration for basic usage
};
```

---

## 8. Semantic Drift

### What It Is

**Semantic drift** occurs when agent behavior gradually degrades without obvious error signals. The agent continues to function but produces increasingly misaligned outputs.

### Why It Matters

Traditional monitoring catches crashes and errors. Semantic drift produces:
- Technically correct but contextually wrong code
- Subtle style inconsistencies
- Gradual deviation from project conventions
- Slow erosion of code quality

### Detection Patterns

#### 1. Output Quality Metrics

Track beyond pass/fail:
```typescript
interface QualityMetrics {
  // Basic
  testsPass: boolean;
  typeChecksPass: boolean;
  lintPass: boolean;

  // Quality signals
  cyclomaticComplexity: number;
  codeduplication: number;
  testCoverage: number;

  // Style consistency
  namingConventionScore: number;
  patternMatchScore: number;  // How well does output match existing patterns?
}
```

#### 2. Pattern Matching

```typescript
// Define expected patterns
const expectedPatterns = {
  componentStructure: /export function \w+\([^)]*\): JSX\.Element/,
  errorHandling: /Result<.*,.*Error>/,
  testStructure: /describe\(['"].*['"], \(\) => \{[\s\S]*it\(/
};

// Check agent output against patterns
function checkPatternCompliance(code: string): PatternReport {
  return Object.entries(expectedPatterns).map(([name, pattern]) => ({
    pattern: name,
    matches: pattern.test(code)
  }));
}
```

#### 3. Historical Comparison

```typescript
// Compare current output to recent human-approved code
function detectDrift(newCode: string, approvedSamples: string[]): DriftReport {
  const metrics = {
    avgLineLength: calculateAvgLineLength(newCode),
    importStyle: detectImportStyle(newCode),
    namingConvention: detectNamingConvention(newCode)
  };

  const baseline = aggregateMetrics(approvedSamples);

  return {
    deviation: calculateDeviation(metrics, baseline),
    alerts: identifySignificantDrifts(metrics, baseline)
  };
}
```

### Mitigation Strategies

#### 1. Regular Pattern Reinforcement

Periodically remind agents of core patterns:
```markdown
## Pattern Reminders

Every 10 tasks, review these core patterns:
1. Component structure (see src/components/Button.tsx)
2. Service pattern (see src/services/UserService.ts)
3. Test structure (see tests/unit/services/auth.test.ts)
```

#### 2. Drift Alerts

```yaml
# In CI
- name: Pattern Compliance Check
  run: |
    npm run check-patterns
    if [ $? -ne 0 ]; then
      echo "::warning::Code pattern drift detected"
    fi
```

#### 3. Calibration Sessions

Periodically:
1. Review recent agent-generated code
2. Identify drift from conventions
3. Update AGENTS.md with corrections
4. Add failing examples to "Bad" section

---

## Summary: The AX Maturity Model

| Level | Focus | Indicators |
|-------|-------|------------|
| 1: Basic | Functional | AGENTS.md exists, commands work |
| 2: Documented | Clear | Good examples, boundaries defined |
| 3: Optimized | Efficient | Fast feedback, context managed |
| 4: Calibrated | Aligned | Trust levels, stakes framework |
| 5: Resilient | Robust | Recovery architecture, drift detection |

Most teams should aim for Level 3 immediately, Level 4 within a quarter, and Level 5 for critical systems.

---

*For unresolved questions and emerging opportunities, see [Open Questions](./OPEN_QUESTIONS.md).*
