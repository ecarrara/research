# Agent Experience (AX) in Software Engineering

**A Comprehensive Guide to Designing Repositories for AI Agent Collaboration**

*Research conducted: February 2026*

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [What is Agent Experience (AX)?](#what-is-agent-experience-ax)
3. [Core Principles](#core-principles)
4. [Best Practices by Category](#best-practices-by-category)
   - [Repository Structure](#1-repository-structure)
   - [Agent Instructions (AGENTS.md)](#2-agent-instructions-agentsmd)
   - [Documentation for Agents](#3-documentation-for-agents)
   - [Task Design & Decomposition](#4-task-design--decomposition)
   - [Context Management](#5-context-management)
   - [Testing & Verification](#6-testing--verification)
   - [Git Workflow & Version Control](#7-git-workflow--version-control)
   - [CI/CD Integration](#8-cicd-integration)
   - [Security & Sandboxing](#9-security--sandboxing)
   - [Observability & Debugging](#10-observability--debugging)
   - [Human-in-the-Loop Patterns](#11-human-in-the-loop-patterns)
5. [Novel Dimensions of AX](#novel-dimensions-of-ax)
6. [AX Audit Checklist](#ax-audit-checklist)
7. [High-Impact Changes (Prioritized)](#high-impact-changes-prioritized)
8. [What We Don't Know Yet](#what-we-dont-know-yet)
9. [Sources & Further Reading](#sources--further-reading)

---

## Executive Summary

Agent Experience (AX) is an emerging discipline focused on designing software systems, repositories, and workflows that AI agents can effectively navigate, understand, and operate within. As 85% of developers now use AI tools for coding, and agentic AI is projected to reach 1 billion agents in service by end of 2026, optimizing for AX has become a competitive necessity.

**Key findings from this research:**

- **Context is cognitive load, not just storage.** Even with 2M-token models, the practical sweet spot is ~150K tokens (75% of 200K). Exceeding 85% capacity causes 23% performance degradation.

- **AGENTS.md is the emerging cross-tool standard** with 60k+ projects and 25+ compatible tools. One code example outperforms paragraphs of explanation.

- **Task decomposition is critical.** Complex tasks cause significant performance drops. Break work into narrow, verifiable chunks.

- **Incremental progress beats monolithic sessions.** Treat each agent session like a developer handoff with clean git commits and progress documentation.

- **Human-in-the-loop is a permanent pattern, not a workaround.** Build trust gradually by starting with approvals on risky operations.

This guide provides actionable practices you can implement today, an audit checklist for evaluating repositories, and a prioritized list of high-ROI changes.

---

## What is Agent Experience (AX)?

### Definition

**Agent Experience (AX)** refers to the holistic experience AI agents have when interacting with a product, platform, or system. It encompasses how easily agents can access, understand, and operate within digital environments to achieve user-defined goals.

The term was coined by Matt Biilmann (Netlify CEO) in early 2025, positioning AX as the third evolution in experience design:

| Era | Term | Focus | Coined |
|-----|------|-------|--------|
| 1 | UX (User Experience) | End users interacting with products | 1993 (Don Norman) |
| 2 | DX (Developer Experience) | Developers building on platforms | 2011 (Jeremiah Lee) |
| 3 | **AX (Agent Experience)** | AI agents operating within systems | 2025 (Matt Biilmann) |

### Why AX Matters

> "Tools optimized for agent integration will become vastly more capable, efficient and popular, while those remaining difficult for LLMs and agents to use will require increasing manual intervention."
> — Matt Biilmann

**Evidence of impact:**
- Over 1,000 sites deploy daily through ChatGPT on Netlify because they optimized for agent needs
- Microsoft's AI code reviewer supports 90% of PRs, impacting 600K+ PRs/month
- 83.8% of Claude Code-generated PRs are accepted, with ~50% merged without modification

### AX vs. DX: Key Differences

| Aspect | DX (Developer Experience) | AX (Agent Experience) |
|--------|---------------------------|----------------------|
| SDKs | Critical for adoption | Less important (agents can write their own) |
| Documentation | Human-readable explanations | Machine-parseable, structured formats |
| Onboarding | Days to weeks acceptable | Milliseconds required |
| Error messages | Helpful for debugging | Must be actionable and structured |
| Authentication | Manual setup acceptable | Fully automated paths required |
| Sales process | "Contact sales" is normal | Agents won't wait—instant access or nothing |

---

## Core Principles

### 1. Treat Context as a Finite Resource

> "A context window is not just storage capacity. It is cognitive load."

Just because a model can ingest 2 million tokens doesn't mean it can pay attention to them equally. Effective agentic systems must treat context the way operating systems treat memory and CPU cycles: as finite resources to be budgeted, compacted, and intelligently paged.

**Practical implications:**
- Reserve 25% of context for reasoning headroom
- Use hierarchical summarization instead of raw file inclusion
- Curate context deliberately rather than stuffing everything in

### 2. Optimize for Agent Readability

Agents learn from your codebase the way a new team member would—picking up naming conventions, preferred libraries, documentation style, and how similar problems were solved before. The more mature and consistent your codebase, the better the agent adapts.

**But there's a critical limitation:** The agent doesn't learn *why* your team does things a certain way, only *that* it does.

### 3. Design for Incremental Progress

Complex tasks should be broken into small, verifiable chunks. Each agent session should:
- Make progress on a single, focused objective
- Leave the codebase in a clean, mergeable state
- Document what was accomplished for the next session

### 4. Provide Immediate Feedback Loops

> "Much of the magic of agents comes from their ability to fix their own mistakes and iterate against error messages."

Agents perform dramatically better when they have access to:
- Type checkers that catch errors immediately
- Linters that enforce style in real-time
- Test suites that validate changes
- CI systems that provide structured feedback

### 5. Make Boundaries Explicit

Agents cannot infer the "stakes" of a task. They may "overthink" a simple request or under-deliver on a critical one. Explicit boundaries—what to always do, what to ask about, what to never do—prevent costly mistakes.

---

## Best Practices by Category

### 1. Repository Structure

#### Why It Matters for AX
Agents must efficiently locate relevant files without exhausting their context window on exploration. A predictable structure reduces the tokens spent on navigation and increases tokens available for actual work.

#### Best Practices

**Use conventional directory layouts:**
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

**For monorepos, include nested AGENTS.md files:**
```
monorepo/
├── AGENTS.md              # Root-level instructions
├── packages/
│   ├── api/
│   │   └── AGENTS.md      # API-specific instructions
│   ├── web/
│   │   └── AGENTS.md      # Web-specific instructions
│   └── shared/
│       └── AGENTS.md      # Shared package instructions
```

The OpenAI repo contains 88 AGENTS.md files, demonstrating this pattern at scale.

**How to adopt:**
1. Audit your current structure against common conventions
2. Create an AGENTS.md file at root with directory overview
3. For monorepos, add nested AGENTS.md files in each package
4. Include a brief description of each major directory's purpose

---

### 2. Agent Instructions (AGENTS.md)

#### Why It Matters for AX
AGENTS.md is the only file that goes into every single conversation with the agent by default. Coding agents know absolutely nothing about your codebase at the beginning of each session—they must be told anything important each time.

#### The Six Essential Sections

Based on analysis of 2,500+ repositories, effective AGENTS.md files cover:

**1. Commands (place early—agents reference these frequently)**
```markdown
## Commands

- Build: `npm run build`
- Test: `npm test -- --coverage`
- Lint: `npm run lint`
- Type check: `npx tsc --noEmit`
- Single test: `npm test -- --testPathPattern="filename"`
```

**2. Tech Stack (be specific with versions)**
```markdown
## Tech Stack

- React 18.2 with TypeScript 5.3
- Vite 5.0 for bundling
- Tailwind CSS 3.4
- Vitest for testing
- Zod for validation
```

**3. Project Structure**
```markdown
## Structure

- `src/components/` - React components (one per file)
- `src/hooks/` - Custom React hooks
- `src/services/` - API and business logic
- `src/types/` - TypeScript type definitions
- `tests/` - Mirrors src/ structure
```

**4. Code Style (examples over explanations)**
```markdown
## Code Style

Use functional components with hooks:

```tsx
// Good
export function UserCard({ user }: { user: User }) {
  const [isExpanded, setIsExpanded] = useState(false);
  return <div>...</div>;
}

// Bad - don't use class components
class UserCard extends React.Component { ... }
```

**5. Git Workflow**
```markdown
## Git

- Commit messages: `type(scope): description`
- Types: feat, fix, docs, refactor, test
- Always run tests before committing
- Keep commits atomic and focused
```

**6. Boundaries (three-tier structure)**
```markdown
## Boundaries

### Always
- Run tests before marking work complete
- Use TypeScript strict mode
- Handle errors explicitly

### Ask First
- Adding new dependencies
- Changing public API signatures
- Modifying database schemas

### Never
- Commit secrets or credentials
- Disable TypeScript checks
- Skip tests to make CI pass
- Use `any` type
```

#### Key Insight

> "One real code snippet demonstrating your style proves more effective than paragraphs describing conventions."

**How to adopt:**
1. Create AGENTS.md at repository root
2. Start with commands and tech stack (highest impact)
3. Add one good/bad code example for your most important pattern
4. Define explicit boundaries for risky operations
5. Iterate based on agent mistakes—update when patterns go wrong

---

### 3. Documentation for Agents

#### Why It Matters for AX
Agents interact with documentation differently than humans. They need machine-parseable formats, structured metadata, and quick paths to relevant information without navigating complex hierarchies.

#### llms.txt Specification

The `/llms.txt` file is a proposed standard for providing LLM-friendly documentation summaries:

```markdown
# Project Name

> Brief one-line description

## Documentation

- [Getting Started](/docs/getting-started.md): Initial setup and installation
- [API Reference](/docs/api.md): Complete API documentation
- [Architecture](/docs/architecture.md): System design overview

## Key Concepts

- **Authentication**: JWT-based, see /docs/auth.md
- **Rate Limiting**: 100 req/min per user
- **Error Codes**: Documented in /docs/errors.md
```

**Two file variants:**
- `/llms.txt` - Index with links and brief descriptions
- `/llms-full.txt` - Complete content in single file (no navigation needed)

#### OpenAPI for APIs

> "A well-structured REST API is the best 'SDK' for an AI Agent."

For APIs, OpenAPI 3.0+ specifications are critical:
- Complete endpoint definitions
- Rich `description` fields for all components
- Example requests and responses
- Error code documentation

```yaml
paths:
  /users/{id}:
    get:
      summary: Get user by ID
      description: |
        Retrieves a user's profile information. Returns 404 if user
        doesn't exist. Requires authentication token in header.
      parameters:
        - name: id
          in: path
          required: true
          description: Unique user identifier (UUID format)
          schema:
            type: string
            format: uuid
```

**How to adopt:**
1. Create `/llms.txt` as an index to your documentation
2. Ensure OpenAPI specs have rich descriptions
3. Include example values for all parameters
4. Document error scenarios explicitly

---

### 4. Task Design & Decomposition

#### Why It Matters for AX

> "When AI tools try to handle complex, multi-step conversations, their performance drops significantly."

Asking an agent to "build user authentication" is like assigning a new team member a massive refactoring project on day one. The agent loses context, makes unfounded assumptions, and produces code that looks plausible but doesn't integrate.

#### The Task Decomposition Framework

**Task Categories:**

| Type | Characteristics | Agent Performance | Examples |
|------|-----------------|-------------------|----------|
| Type 1: Narrow | Single-file, well-defined | Excellent | Unit tests, feature flags, boilerplate |
| Type 2: Contextual | Requires specific context | Good with samples | Debugging, refactoring, migrations |
| Type 3: Open-ended | Multi-file, architectural | Poor alone | Full features—decompose first |

**Decomposition Example:**

```
# Bad: Single complex prompt
"Fix the authentication bug in the user login flow"

# Good: Decomposed tasks
1. "What error is occurring in auth.js line 45?"
2. "What validation is missing in the login function?"
3. "Write a test that reproduces this bug"
4. "Fix the validation in handleLogin()"
5. "Verify the test passes"
```

#### Spec-Driven Development

For larger features, write specifications before code:

```markdown
## Feature: Password Reset

### Requirements
- User can request reset via email
- Token expires after 1 hour
- Rate limit: 3 requests per hour per email

### API Contract
POST /auth/reset-request
  Body: { email: string }
  Response: { success: boolean }

POST /auth/reset-confirm
  Body: { token: string, newPassword: string }
  Response: { success: boolean }

### Implementation Notes
- Use existing email service in src/services/email.ts
- Store tokens in Redis with TTL
- Log all reset attempts for security audit
```

Microsoft's Developer Tools research found that prompts with explicit specifications reduced back-and-forth refinements by 68%.

**How to adopt:**
1. Break Type 3 tasks into Type 1 and Type 2 sub-tasks
2. Define acceptance criteria before starting
3. Use specs for anything touching multiple files
4. Request explicit checkpoints after each phase

---

### 5. Context Management

#### Why It Matters for AX

> "Providing a 2M-token context to an LLM is like handing a new developer 10,000 pages of documentation on day one and expecting them to fix a bug in five minutes."

Research shows:
- 23% performance degradation when context exceeds 85% capacity
- Claude Code triggers auto-compact at 75% usage
- Performance is best when relevant information is at the beginning or end

#### The Context Stack Pattern

Factory.ai's layered approach:

```
┌─────────────────────────────────────────┐
│  1. Repository Overview (always present) │  ~5K tokens
├─────────────────────────────────────────┤
│  2. Relevant File Contents (searched)    │  ~50K tokens
├─────────────────────────────────────────┤
│  3. Task-Specific Context (user input)   │  ~20K tokens
├─────────────────────────────────────────┤
│  4. Reasoning Headroom (reserved)        │  ~75K tokens
└─────────────────────────────────────────┘
                Total: ~150K tokens
```

#### Repository Overviews

Create pre-summarized context that can be injected at session start:

```markdown
## Repository Overview

### Architecture
- Monolithic Node.js backend with Express
- PostgreSQL database with Prisma ORM
- React frontend with Redux state management

### Key Entry Points
- `src/server.ts` - Application bootstrap
- `src/routes/index.ts` - API route definitions
- `src/services/` - Core business logic

### Critical Patterns
- All database access through Prisma client
- Authentication via JWT middleware
- Errors bubble up to global handler in src/middleware/error.ts

### Build & Test
- `npm run dev` - Development server with hot reload
- `npm test` - Jest test suite
- `npm run build` - Production build
```

**How to adopt:**
1. Create a repository overview section in AGENTS.md
2. Document key entry points and patterns
3. Keep individual files focused (under 500 lines)
4. Use hierarchical summarization for large codebases

---

### 6. Testing & Verification

#### Why It Matters for AX

> "If you skip review, you don't eliminate work—you defer it. Developers who succeed with AI at high velocity are those who've built verification systems that catch issues before they reach production."

Industry analysis found AI-generated code contained 1.7x more defects than human-written code. Strong testing practices are essential.

#### Testing Requirements for Agents

**Provide explicit test commands:**
```markdown
## Testing

Run all tests:
```bash
npm test
```

Run specific test file:
```bash
npm test -- src/services/auth.test.ts
```

Run tests matching pattern:
```bash
npm test -- --testNamePattern="login"
```

Check coverage (must be >70%):
```bash
npm test -- --coverage
```
```

**Include test examples:**
```markdown
## Test Patterns

```typescript
describe('AuthService', () => {
  // Arrange-Act-Assert pattern
  it('should reject invalid credentials', async () => {
    // Arrange
    const invalidCreds = { email: 'test@example.com', password: 'wrong' };

    // Act
    const result = await authService.login(invalidCreds);

    // Assert
    expect(result.success).toBe(false);
    expect(result.error).toBe('INVALID_CREDENTIALS');
  });
});
```

**End-to-End Testing:**

Anthropic's research found that agents with browser automation tools (like Puppeteer) demonstrated significantly improved feature verification compared to unit tests alone:

```markdown
## E2E Tests

Verify features work end-to-end:
```bash
npm run test:e2e
```

E2E tests use Playwright. For new features:
1. Add test in `tests/e2e/`
2. Test the happy path first
3. Add edge cases after happy path passes
```

**How to adopt:**
1. Document all test commands with examples
2. Set minimum coverage thresholds (recommend >70%)
3. Add E2E tests for critical user flows
4. Make test failures block agent completion

---

### 7. Git Workflow & Version Control

#### Why It Matters for AX

> "Treat each agent session like a developer handoff rather than a continuous workflow."

Git provides essential recovery mechanisms for agents. Clean commit history enables:
- Reverting problematic changes
- Understanding what was attempted
- Resuming work across sessions

#### Git Configuration for Agents

**In AGENTS.md:**
```markdown
## Git Workflow

### Commit Format
type(scope): brief description

Types:
- feat: New feature
- fix: Bug fix
- refactor: Code restructuring
- test: Test additions
- docs: Documentation

### Rules
- Commit after each logical unit of work
- Keep commits atomic (one concern per commit)
- Never amend commits that are already pushed
- Use descriptive messages explaining WHY, not just WHAT

### Dangerous Commands
NEVER run without explicit instruction:
- `git reset --hard`
- `git push --force`
- `git checkout .` (discards changes)
- `git clean -f`
```

#### Progress Documentation Pattern

From Anthropic's long-running agent research:

```markdown
## Progress Tracking

Maintain a progress file at `PROGRESS.md`:

### Format
```markdown
# Progress Log

## Current Session
- Working on: [feature/task name]
- Started: [timestamp]
- Status: [in-progress/blocked/complete]

## Completed
- [x] Task 1 - commit abc123
- [x] Task 2 - commit def456

## Blocked
- [ ] Task 3 - waiting on [reason]

## Notes
- [Any relevant observations]
```

This enables session handoffs and provides recovery context.
```

**How to adopt:**
1. Add git conventions to AGENTS.md
2. List dangerous commands explicitly
3. Consider a PROGRESS.md pattern for multi-session work
4. Review agent commits before merging

---

### 8. CI/CD Integration

#### Why It Matters for AX

> "Agentic AI is an amplifier of existing technical and organizational disciplines, not a substitute for them."

CI/CD provides the structured feedback loops agents need. Well-configured pipelines catch issues early and provide actionable error messages.

#### CI Requirements for Agent-Friendly Repos

**Fast Feedback:**
```yaml
# .github/workflows/ci.yml
name: CI
on: [push, pull_request]

jobs:
  quick-checks:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      # Type checking - fast, catches many errors
      - name: Type Check
        run: npx tsc --noEmit

      # Linting - fast, enforces style
      - name: Lint
        run: npm run lint

  tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Test
        run: npm test -- --coverage
      - name: Coverage Check
        run: |
          COVERAGE=$(cat coverage/coverage-summary.json | jq '.total.lines.pct')
          if (( $(echo "$COVERAGE < 70" | bc -l) )); then
            echo "Coverage $COVERAGE% below 70% threshold"
            exit 1
          fi
```

**Actionable Error Output:**

Configure tools to produce clear, parseable errors:
```json
// tsconfig.json
{
  "compilerOptions": {
    "pretty": true,
    "noErrorTruncation": true
  }
}
```

**Quality Gates:**
```markdown
## CI Quality Gates

PRs require:
- [ ] All tests passing
- [ ] Coverage >= 70%
- [ ] No TypeScript errors
- [ ] No lint warnings
- [ ] Build succeeds
```

#### AI-Aware CI Patterns

**Self-Healing Capabilities:**
Some teams integrate AI agents into CI to automatically attempt fixes:
```yaml
- name: Auto-fix lint errors
  if: failure()
  run: |
    npm run lint -- --fix
    git commit -am "fix: auto-fix lint errors"
```

**Intelligent Quality Gates:**
```yaml
- name: AI Code Review
  uses: coderabbit/ai-review@v1
  with:
    # Focus review on high-risk areas
    focus: security,performance,breaking-changes
```

**How to adopt:**
1. Ensure CI runs on every push and PR
2. Keep fast checks (lint, types) separate from slow tests
3. Configure clear error output formats
4. Document CI requirements in AGENTS.md

---

### 9. Security & Sandboxing

#### Why It Matters for AX

> "AI-generated code must be treated as untrusted by default. Execution boundaries must be enforced structurally, not heuristically."

#### Mandatory Security Controls

From the NVIDIA AI Red Team:

| Control | Purpose | Implementation |
|---------|---------|----------------|
| Network egress controls | Prevent data exfiltration | Block arbitrary external connections |
| Filesystem isolation | Prevent persistence | Block writes outside workspace |
| Config file protection | Prevent escalation | Block writes to hooks, configs |
| Lifecycle management | Prevent accumulation | Clean up sessions regularly |

#### Agent Account Best Practices

```markdown
## Security Configuration

### Agent Accounts
- Create dedicated agent accounts with limited IAM roles
- Provide development/staging access only—never production
- Use read-only API keys where possible
- Rotate credentials regularly

### Secrets Management
Never include in AGENTS.md or code:
- API keys
- Database credentials
- Private keys
- Auth tokens

Use environment variables or secret managers:
```bash
# Good - reference environment variable
DATABASE_URL=$DATABASE_URL npm run migrate

# Bad - hardcoded credential
DATABASE_URL=postgres://user:pass@host/db npm run migrate
```

### File Boundaries
Agent should NOT access:
- `.env` files
- `credentials/` directory
- `*.pem`, `*.key` files
- `config/production.json`
```

#### Sandboxing Options

| Level | Technology | Use Case |
|-------|------------|----------|
| Basic | Docker with security configs | Development, trusted code |
| Enhanced | gVisor, Container hardening | Shared environments |
| Maximum | Firecracker microVMs | Production, untrusted code |

**Claude Code's Approach:**
- OS-level sandboxing (bubblewrap on Linux, Seatbelt on macOS)
- Filesystem and network isolation
- Child processes inherit security boundaries

**How to adopt:**
1. Create dedicated agent accounts with minimal permissions
2. Document forbidden file paths in AGENTS.md
3. Use environment variables for all secrets
4. Consider sandbox tools for running agent-generated code

---

### 10. Observability & Debugging

#### Why It Matters for AX

> "When an agent fails in production, it rarely crashes outright. Instead, it may enter a loop, select the wrong tool, or produce an incorrect decision based on incomplete or stale context."

Agents are non-deterministic by design. Traditional monitoring (error rates, latency) misses subtle behavioral degradation.

#### The Three Pillars of Agent Observability

**1. Monitoring** - Predefined metrics
- Token usage per session
- Task completion rates
- Time to completion
- Error frequencies

**2. Tracing** - Execution paths
- Decision chains across steps
- Tool selection sequences
- Context at each decision point

**3. Observability** - Behavioral explanation
- Why did the agent choose this approach?
- What alternatives were considered?
- Where did reasoning diverge from expected?

#### Implementation Patterns

**Structured Logging:**
```typescript
// Log agent decisions with context
logger.info('agent_decision', {
  task_id: task.id,
  step: 'tool_selection',
  selected_tool: 'file_edit',
  alternatives_considered: ['file_create', 'file_delete'],
  confidence: 0.87,
  context_tokens_used: 45000,
  reasoning: 'File exists, modification preferred over recreation'
});
```

**OpenTelemetry Integration:**
```typescript
import { trace } from '@opentelemetry/api';

const tracer = trace.getTracer('agent-service');

async function executeAgentTask(task: Task) {
  return tracer.startActiveSpan('agent.execute', async (span) => {
    span.setAttribute('task.type', task.type);
    span.setAttribute('task.complexity', task.complexity);

    // ... agent execution

    span.setAttribute('result.status', result.status);
    span.setAttribute('tokens.used', result.tokensUsed);
    span.end();
  });
}
```

**Agent Action Audit Trail:**
```markdown
## Observability Requirements

All agent actions must be logged:
- File reads/writes with paths
- Commands executed with output
- API calls with request/response
- Decisions with reasoning

Logs are immutable and retained for 30 days.
```

**How to adopt:**
1. Add structured logging for agent decisions
2. Track token usage and task completion metrics
3. Implement tracing for multi-step operations
4. Create dashboards for agent performance visibility

---

### 11. Human-in-the-Loop Patterns

#### Why It Matters for AX

> "Human-in-the-loop is not a temporary workaround—it's a long-term pattern for building AI agents we can trust."

Agents may hallucinate actions, misinterpret prompts, or overstep boundaries. For sensitive operations, human checkpoints are essential.

#### Checkpoint Categories

| Risk Level | Operations | Pattern |
|------------|------------|---------|
| Low | Read-only queries, formatting | Auto-approve |
| Medium | File modifications, test execution | Notify + timeout |
| High | Deployments, data mutations | Require explicit approval |
| Critical | Production changes, credential access | Multi-person approval |

#### Gradual Trust Building

**Phase 1: Maximum Oversight**
```markdown
## Approval Requirements (Phase 1)

Require explicit approval for:
- Any file creation or deletion
- Any external API calls
- Any command execution
- Any git operations
```

**Phase 2: Relaxed Read Operations**
```markdown
## Approval Requirements (Phase 2)

Auto-approved:
- File reads
- Running existing tests
- Lint checks

Require approval:
- File writes
- New dependencies
- Git commits
```

**Phase 3: Context-Aware Trust**
```markdown
## Approval Requirements (Phase 3)

Auto-approved:
- All read operations
- Test file modifications
- Documentation updates
- Commits under 100 lines

Require approval:
- Production config changes
- New dependencies
- Database migrations
- Commits over 100 lines
```

#### Implementation Example

```typescript
interface CheckpointConfig {
  operation: string;
  riskLevel: 'low' | 'medium' | 'high' | 'critical';
  autoApproveConditions?: () => boolean;
  timeoutMs?: number;
}

const checkpoints: CheckpointConfig[] = [
  {
    operation: 'file_write',
    riskLevel: 'medium',
    autoApproveConditions: () =>
      file.path.includes('/tests/') ||
      file.path.endsWith('.md'),
    timeoutMs: 300000 // 5 min timeout
  },
  {
    operation: 'deploy',
    riskLevel: 'critical',
    // Always requires approval, no auto-conditions
  }
];
```

**How to adopt:**
1. List all agent operations and categorize by risk
2. Start with Phase 1 (maximum oversight)
3. Track which approvals are always granted
4. Gradually relax constraints based on data
5. Never auto-approve critical operations

---

## Novel Dimensions of AX

Beyond the established practices above, research revealed several under-documented dimensions of Agent Experience:

### 1. Ambiguity Surfaces

**What it is:** Areas in your codebase or instructions where multiple interpretations are possible, causing agent decisions to cascade in unexpected directions.

**Why it matters:** Microsoft research found explicit specifications reduced back-and-forth refinements by 68%. Ambiguity compounds across agent decision points.

**How to address:**
- Define terms explicitly in AGENTS.md
- Provide concrete examples for edge cases
- Set confidence thresholds that trigger human review
- Use structured formats (JSON, TypeScript types) over prose

```markdown
## Disambiguation

When ambiguous, prefer:
- Creating new files over modifying existing ones
- Adding tests over assuming correctness
- Asking for clarification over guessing
- Smaller changes over comprehensive refactors
```

### 2. Feedback Latency

**What it is:** The time between an agent taking an action and receiving feedback about its correctness.

**Why it matters:** Agents with immediate feedback (type checkers, linters) dramatically outperform those waiting for human review or slow CI.

**Optimal feedback hierarchy:**
1. **<1 second:** Type errors, lint errors (IDE integration)
2. **<10 seconds:** Unit tests (watch mode)
3. **<1 minute:** Integration tests
4. **<5 minutes:** Full CI suite
5. **Avoid:** Requiring human review for routine decisions

### 3. Trust Calibration

**What it is:** The agent's model of how much autonomy it has for different types of operations.

**Why it matters:** Agents cannot infer the "stakes" of a task. They may overthink simple requests or under-deliver on critical ones without explicit calibration.

**Implementation:**
```markdown
## Task Stakes

### Low Stakes (high autonomy)
- Formatting changes
- Documentation updates
- Test additions
- Refactoring without behavior change

### Medium Stakes (verify before completing)
- New feature implementation
- Bug fixes
- Dependency updates

### High Stakes (require explicit approval)
- Security-related changes
- Database schema modifications
- API contract changes
- Production configuration
```

### 4. Recovery State Architecture

**What it is:** Systems that enable agents to recover from failures and resume work across sessions.

**Why it matters:**
> "Starting over is the right answer a lot more often with agents than with humans."

Without recovery architecture, failed sessions lose all progress.

**Components:**
- Git commits as checkpoints
- Progress documentation files
- Clean state requirements before session end
- Explicit handoff notes for next session

### 5. Toolability vs. Documentation

**What it is:** The shift from human-readable documentation to machine-operable interfaces.

**Why it matters:** Agents can write their own SDKs if needed. A well-structured REST API is the best "SDK" for an AI agent.

**Priorities for agents:**
1. OpenAPI specification (complete, with descriptions)
2. Structured error responses (not just messages)
3. Predictable, consistent API patterns
4. Machine-readable configuration formats

### 6. Time-to-First-Action

**What it is:** How quickly an agent can start productive work in your repository.

**Why it matters:**
> "Agents should be able to get up and running in milliseconds. Agents are simply not going to 'contact sales' or 'book a demo'."

**Optimizations:**
- Pre-configured development environments
- No manual authentication steps
- Self-contained setup scripts
- Default configurations that work immediately

### 7. Cognitive Load Budgeting

**What it is:** Actively managing what information is in the agent's context to preserve reasoning capacity.

**Why it matters:** Context window size ≠ capability. Reserving headroom for reasoning is more important than including all possibly-relevant information.

**Budget framework:**
| Category | Allocation | Purpose |
|----------|------------|---------|
| Repository overview | 5% | Orientation |
| Current task context | 20% | Immediate work |
| Relevant file contents | 40% | Working material |
| Reasoning headroom | 25% | Thinking space |
| Buffer | 10% | Unexpected needs |

---

## AX Audit Checklist

Use this checklist to evaluate a repository's Agent Experience:

### Foundation (Must Have)

- [ ] **AGENTS.md exists** at repository root
- [ ] **Build/test commands** are documented and work
- [ ] **Tech stack** is specified with versions
- [ ] **Directory structure** is documented
- [ ] **README.md** provides human-readable overview

### Agent Instructions

- [ ] **Code style** includes actual examples (not just descriptions)
- [ ] **Boundaries** define Always/Ask/Never categories
- [ ] **Dangerous operations** are explicitly listed
- [ ] **Git conventions** are specified
- [ ] **Instructions are concise** (<200 rules total)

### Testing & Verification

- [ ] **Tests exist** and pass
- [ ] **Test commands** are documented in AGENTS.md
- [ ] **Coverage threshold** is defined (recommend >70%)
- [ ] **CI runs** on every push/PR
- [ ] **CI failures** produce actionable error messages

### Context Management

- [ ] **Repository overview** section exists
- [ ] **Key entry points** are documented
- [ ] **Files are focused** (<500 lines typical)
- [ ] **Monorepo has nested** AGENTS.md files

### Security

- [ ] **Secrets** are not in repository
- [ ] **Forbidden paths** are documented
- [ ] **Agent account** has minimal permissions
- [ ] **Sandbox configuration** exists (if executing agent code)

### Observability

- [ ] **Agent actions** are logged
- [ ] **Token usage** is tracked
- [ ] **Decision reasoning** is captured
- [ ] **Failure modes** are documented

### Human-in-the-Loop

- [ ] **Risk levels** are defined for operations
- [ ] **Approval requirements** are documented
- [ ] **Escalation paths** exist for blocked agents
- [ ] **Review checkpoints** are defined for complex tasks

### API/Integration (if applicable)

- [ ] **OpenAPI spec** exists and is complete
- [ ] **Descriptions** exist for all endpoints/parameters
- [ ] **Examples** provided for requests/responses
- [ ] **Error codes** are documented

---

## High-Impact Changes (Prioritized)

Ranked by impact/effort ratio, implement in this order:

### Tier 1: Immediate High ROI (This Week)

| Change | Impact | Effort | Notes |
|--------|--------|--------|-------|
| Create AGENTS.md with commands | Very High | 30 min | Agents can immediately build/test |
| Add tech stack with versions | High | 15 min | Prevents version mismatches |
| Document directory structure | High | 20 min | Reduces exploration overhead |
| Add one good/bad code example | High | 20 min | More effective than paragraphs of rules |

### Tier 2: Quick Wins (This Month)

| Change | Impact | Effort | Notes |
|--------|--------|--------|-------|
| Define boundary categories | High | 30 min | Prevents risky operations |
| Document test commands | High | 15 min | Enables verification loop |
| Add repository overview | Medium | 45 min | Speeds up session starts |
| Configure CI error formatting | Medium | 1 hr | Actionable feedback for agents |

### Tier 3: Foundational (This Quarter)

| Change | Impact | Effort | Notes |
|--------|--------|--------|-------|
| Add E2E tests for critical paths | High | Days | Human-like verification |
| Implement agent logging | Medium | 1-2 days | Debugging visibility |
| Create llms.txt for docs | Medium | 2 hrs | Machine-readable documentation |
| Set up sandboxed execution | Medium | 1-2 days | Security for agent-generated code |

### Tier 4: Advanced (When Ready)

| Change | Impact | Effort | Notes |
|--------|--------|--------|-------|
| MCP server integration | Medium | Days | Dynamic tool access |
| Multi-agent coordination | Medium | Weeks | Parallel task execution |
| Custom agent skills | Variable | Days | Domain-specific capabilities |
| Agent-specific monitoring | Medium | Weeks | Behavioral observability |

---

## What We Don't Know Yet

Agent Experience is an emerging field. These questions remain open:

### Technical Unknowns

**1. Optimal Context Budgeting**
How should context be dynamically allocated between reasoning headroom and retrieved information? Current approaches are largely heuristic.

**2. Multi-Agent Coordination**
When multiple agents work on the same codebase, how should they share context efficiently? What prevents conflicting changes?

**3. Semantic Drift Detection**
How do we detect when agent behavior gradually degrades without obvious error signals? Current observability focuses on failures, not quality degradation.

**4. Agent-Native Testing Patterns**
What testing approaches are specifically suited for agent-generated code? Are current TDD patterns optimal, or do new patterns emerge?

### Organizational Unknowns

**5. Trust Transfer**
Can trust established in one context (e.g., test environment) safely transfer to another (e.g., production)? What verification is needed?

**6. Skill Degradation**
As developers delegate more to agents, do their own skills atrophy? What's the right balance of delegation vs. hands-on work?

**7. Accountability Models**
When agent-generated code causes issues, how should responsibility be attributed? How do audit requirements evolve?

### Ecosystem Unknowns

**8. Standard Fragmentation**
Will AGENTS.md remain the cross-tool standard, or will tools fragment into proprietary formats?

**9. Cost Optimization**
How do teams balance token costs with agent effectiveness? What's the ROI curve for context investment?

**10. Regulatory Evolution**
How will compliance requirements (SOC2, HIPAA, etc.) adapt to agent-generated code? What audit trails will be required?

### Emerging Opportunities

- **Agent-to-agent APIs:** Standards for agents to delegate work to other agents
- **Contextual memory:** Persistent learning from repository patterns across sessions
- **Predictive assistance:** Agents that anticipate needed changes before being asked
- **Quality prediction:** Estimating code quality before human review

---

## Sources & Further Reading

### Primary Sources

1. [Introducing AX: Why Agent Experience Matters](https://biilmann.blog/articles/introducing-ax/) - Matt Biilmann (Netlify CEO)
2. [How to write a great agents.md](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/) - GitHub Blog
3. [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) - Anthropic Engineering
4. [The Context Window Problem](https://factory.ai/news/context-window-problem) - Factory.ai
5. [Coding Agents 101](https://devin.ai/agents101) - Devin.ai
6. [Task Decomposition for AI](https://blog.continue.dev/task-decomposition/) - Continue.dev
7. [AGENTS.md Specification](https://agents.md/) - Official site
8. [AI Agent Observability](https://opentelemetry.io/blog/2025/ai-agent-observability/) - OpenTelemetry
9. [Sandboxing Agentic Workflows](https://developer.nvidia.com/blog/practical-security-guidance-for-sandboxing-agentic-workflows-and-managing-execution-risk/) - NVIDIA AI Red Team
10. [Agent Experience Guide](https://www.speakeasy.com/blog/agent-experience-introduction) - Speakeasy
11. [What is AX and How to Improve It](https://resend.com/blog/agent-experience) - Resend

### Standards & Specifications

- [Model Context Protocol](https://modelcontextprotocol.io/) - Anthropic
- [OpenAPI Specification](https://www.openapis.org/) - OpenAPI Initiative
- [llms.txt Specification](https://llmstxt.org/) - Jeremy Howard / Answer.AI

### Tools & Resources

- [steipete/agent-rules](https://github.com/steipete/agent-rules) - Real-world agent configuration examples
- [GitHub Awesome Copilot](https://github.com/github/awesome-copilot) - Community prompts and configurations
- [OpenTelemetry GenAI Conventions](https://opentelemetry.io/blog/2025/ai-agent-observability/) - Observability standards

### Industry Reports

- Anthropic 2026 Agentic Coding Trends Report
- Microsoft Engineering: AI-Powered Code Reviews at Scale
- Carnegie Mellon: Context Utilization in LLMs
- Meta & Harvard: Confucius Code Agent Research

---

*This research document was compiled in February 2026. The field of Agent Experience is evolving rapidly—practices and standards may change.*
