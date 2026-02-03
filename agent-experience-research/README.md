# Agent Experience (AX) in Software Engineering

**A Guide to Designing Repositories for AI Agent Collaboration**

*Research compiled: February 2026*

---

## What is Agent Experience?

**Agent Experience (AX)** is the holistic experience AI agents have when interacting with a product, platform, or system. It encompasses how easily agents can access, understand, and operate within digital environments to achieve user-defined goals.

The term was coined by Matt Biilmann (Netlify CEO) in early 2025, positioning AX as the third evolution in experience design:

| Era | Term | Focus |
|-----|------|-------|
| 1 | UX (User Experience) | End users interacting with products |
| 2 | DX (Developer Experience) | Developers building on platforms |
| 3 | **AX (Agent Experience)** | AI agents operating within systems |

---

## Why This Matters Now

- **85% of developers** now use AI tools for coding
- **1 billion AI agents** predicted in service by end of FY2026
- **83.8% of Claude Code PRs** are accepted, with ~50% merged without modification
- **60k+ open-source projects** use AGENTS.md for agent instructions

> "Tools optimized for agent integration will become vastly more capable, efficient and popular, while those remaining difficult for LLMs and agents to use will require increasing manual intervention."

---

## Key Findings

### 1. Context is Cognitive Load, Not Storage
Even with 2M-token models, the practical sweet spot is ~150K tokens. Exceeding 85% capacity causes 23% performance degradation. Reserve headroom for reasoning.

### 2. AGENTS.md is the Cross-Tool Standard
One code example outperforms paragraphs of explanation. 60k+ projects, 25+ compatible tools.

### 3. Task Decomposition is Critical
Complex tasks cause significant performance drops. Break work into narrow, verifiable chunks.

### 4. Incremental Progress Beats Monolithic Sessions
Treat each agent session like a developer handoff with clean git commits and progress documentation.

### 5. Human-in-the-Loop is Permanent
Build trust gradually by starting with approvals on risky operations. This is a long-term pattern, not a workaround.

---

## Documents in This Guide

| Document | Audience | Purpose |
|----------|----------|---------|
| [Practical Guide](./PRACTICAL_GUIDE.md) | All Engineers | **Mandatory implementations** for every repository |
| [Audit Checklist](./AUDIT_CHECKLIST.md) | Tech Leads | Evaluate and score repository AX readiness |
| [Best Practices](./BEST_PRACTICES.md) | All Engineers | Detailed practices across 11 categories |
| [Novel Insights](./NOVEL_INSIGHTS.md) | Senior Engineers | Under-documented dimensions and advanced patterns |
| [Open Questions](./OPEN_QUESTIONS.md) | Leadership | Unresolved questions and emerging opportunities |
| [References](./REFERENCES.md) | Anyone | Curated links for further learning |

---

## Quick Start: Minimum Viable AX

If you only do three things, do these:

### 1. Create AGENTS.md (30 minutes)

```markdown
# AGENTS.md

## Commands
- Build: `npm run build`
- Test: `npm test`
- Lint: `npm run lint`

## Tech Stack
- Node.js 20, TypeScript 5.3, React 18

## Boundaries
### Never
- Commit secrets or credentials
- Disable TypeScript strict mode
- Skip tests to make CI pass
```

### 2. Document Your Directory Structure (15 minutes)

```markdown
## Structure
- `src/` - Application source code
- `src/components/` - React components
- `src/services/` - Business logic
- `tests/` - Test files (mirrors src/)
```

### 3. Add One Code Style Example (15 minutes)

```markdown
## Code Style

```typescript
// Good - functional component with explicit types
export function UserCard({ user }: { user: User }) {
  return <div>{user.name}</div>;
}

// Bad - class component, implicit any
class UserCard extends React.Component { ... }
```
```

**Total time: ~1 hour for immediate agent productivity gains.**

---

## High-Impact Changes (Priority Order)

### This Week (Immediate ROI)
1. Create AGENTS.md with build/test commands
2. Add tech stack with versions
3. Document directory structure
4. Add one good/bad code example

### This Month
5. Define boundary categories (Always/Ask/Never)
6. Add repository overview section
7. Configure CI error formatting for actionable feedback

### This Quarter
8. Add E2E tests for critical paths
9. Implement agent action logging
10. Set up sandboxed execution for agent-generated code

See [Practical Guide](./PRACTICAL_GUIDE.md) for complete implementation details.

---

## Additional Resources

- [Research Notes](./notes.md) - Raw research notes and source tracking
- [References](./REFERENCES.md) - All sources and further reading

---

*This guide is based on research from Anthropic, GitHub, Factory.ai, Devin, NVIDIA AI Red Team, OpenTelemetry, and analysis of 2,500+ repositories.*
