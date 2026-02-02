# What We Don't Know Yet

**Open questions and emerging opportunities in Agent Experience**

*For leadership and strategic planning*

Agent Experience is an emerging field. This document captures unresolved questions, active debates, and opportunities where early investment may yield significant advantages.

---

## Table of Contents

1. [Technical Unknowns](#technical-unknowns)
2. [Organizational Unknowns](#organizational-unknowns)
3. [Ecosystem Unknowns](#ecosystem-unknowns)
4. [Emerging Opportunities](#emerging-opportunities)
5. [Research to Watch](#research-to-watch)

---

## Technical Unknowns

### 1. Optimal Context Budgeting

**Question:** How should context be dynamically allocated between reasoning headroom and retrieved information?

**Current State:** Rules of thumb (75% max utilization, 25% reasoning headroom) based on empirical observation, not principled optimization.

**Why It Matters:** Context is the primary constraint on agent capability. Better budgeting directly improves output quality.

**What We're Missing:**
- Formal models of context-performance tradeoffs
- Adaptive algorithms for different task types
- Metrics for "context efficiency" (output quality per token)

**Potential Research Directions:**
- Experiment with different allocation ratios per task type
- Measure quality degradation curves as context fills
- Build instrumentation to track context utilization patterns

---

### 2. Multi-Agent Coordination

**Question:** When multiple agents work on the same codebase, how should they share context efficiently?

**Current State:** Most systems use single-agent architectures. Multi-agent systems often duplicate context or use naive handoff protocols.

**Why It Matters:** Complex projects may benefit from specialized agents (code, tests, docs, review) working in parallel.

**What We're Missing:**
- Efficient context sharing protocols
- Conflict resolution for simultaneous edits
- Optimal task distribution strategies
- Coordination overhead measurements

**Known Challenges:**
- Agents may make conflicting assumptions
- Shared context risks context window bloat
- No standard protocol for agent-to-agent communication

---

### 3. Semantic Drift Detection

**Question:** How do we detect when agent behavior gradually degrades without obvious error signals?

**Current State:** Monitoring focuses on binary outcomes (pass/fail, error/success). Subtle quality degradation goes unnoticed.

**Why It Matters:** Accumulated drift erodes code quality over time, creating technical debt that's hard to attribute.

**What We're Missing:**
- Quality metrics beyond pass/fail
- Baseline establishment for "normal" agent output
- Drift detection algorithms
- Automated remediation triggers

**Potential Approaches:**
- Style consistency scoring against human-approved examples
- Complexity trend monitoring
- Pattern compliance tracking over time

---

### 4. Agent-Native Testing Patterns

**Question:** What testing approaches are specifically suited for agent-generated code?

**Current State:** We apply human-centric testing practices to agent output. These may not be optimal.

**Why It Matters:** AI-generated code may have different failure modes than human-written code (1.7x more defects in some studies).

**Open Questions:**
- Are certain test types more effective for agent code?
- Should test coverage thresholds differ?
- How do we test for "plausible but wrong" patterns?
- What's the right balance of unit vs. integration vs. E2E?

**Hypothesis to Test:**
- Property-based testing may catch agent blind spots better than example-based
- Mutation testing may be more valuable for agent code
- E2E tests catching more issues than with human code

---

### 5. Context Persistence Across Sessions

**Question:** How can agents maintain useful context across sessions without explicit handoff?

**Current State:** Each session starts fresh. Continuity requires explicit documentation (PROGRESS.md) or conversation history.

**Why It Matters:** Long-running projects suffer from repeated context rebuilding, wasting tokens and time.

**Approaches Being Explored:**
- Vector databases for semantic retrieval
- Summarization chains for conversation compression
- Checkpoint systems for agent state
- Repository-embedded memory (commit messages, comments)

**Challenges:**
- What to remember vs. forget?
- How to handle stale information?
- Privacy and security of persistent memory

---

## Organizational Unknowns

### 6. Trust Transfer

**Question:** Can trust established in one context safely transfer to another?

**Current State:** Trust is typically reset per session, per repository, per environment.

**Why It Matters:** Rebuilding trust is expensive. Reusing proven trust would accelerate adoption.

**Scenarios:**
- Agent trusted in staging → production?
- Trust in repo A → similar repo B?
- Trust for user X → trust for user Y's tasks?

**Risk Factors:**
- Contexts may differ in subtle, important ways
- Trust gaming if boundaries are known
- Accountability unclear when trust transfers

**Possible Approach:**
- Trust profiles with explicit scope
- Portable credentials with verification
- Graduated trust transfer with monitoring

---

### 7. Skill Degradation

**Question:** As developers delegate more to agents, do their own skills atrophy?

**Current State:** Anecdotal reports of developers becoming "reviewers" rather than "writers." No systematic study.

**Why It Matters:** If agents become unavailable or fail, teams need capable humans. Skill maintenance has long-term implications.

**Concerns:**
- Debugging skills may decline without practice
- Deep architectural understanding may fade
- Junior developers may never develop certain skills

**Counter-Arguments:**
- Skills shift to higher-level concerns (architecture, review)
- New skills emerge (prompt engineering, agent supervision)
- Historical parallel: did calculators degrade math skills?

**What to Monitor:**
- Time to resolution when agents unavailable
- Quality of human code review
- Architectural decision quality
- Incident response capability

---

### 8. Accountability Models

**Question:** When agent-generated code causes issues, how should responsibility be attributed?

**Current State:** Unclear. The human who approved? The team that configured the agent? The agent provider?

**Why It Matters:** Accountability affects behavior. Unclear accountability leads to diffusion of responsibility.

**Scenarios to Consider:**
- Security vulnerability in agent-generated code
- Agent misinterprets requirement, ships wrong feature
- Agent-generated code causes production outage
- Compliance violation in agent output

**Emerging Patterns:**
- "Human in the loop" as accountability transfer
- Audit logs of agent decisions
- Required human sign-off for certain change types
- Insurance and liability frameworks

---

### 9. Team Structure Evolution

**Question:** How do team structures change when agents handle significant coding work?

**Current State:** Teams are adding agents to existing structures. Fundamental reorganization hasn't happened.

**Possible Futures:**
- Fewer developers, each supervising multiple agents
- Specialized "agent whisperer" roles
- Quality/review roles grow relative to coding roles
- Architecture/design emphasis increases

**Questions to Answer:**
- Optimal human:agent ratio for different work types?
- Which roles grow, shrink, or transform?
- How does hiring change?
- How does career progression adapt?

---

## Ecosystem Unknowns

### 10. Standard Fragmentation

**Question:** Will AGENTS.md remain the cross-tool standard, or will tools fragment into proprietary formats?

**Current State:** AGENTS.md has strong adoption (60k+ projects, 25+ tools). But competitive pressures may drive differentiation.

**Why It Matters:** Fragmentation increases maintenance burden and reduces portability.

**Forces Toward Standardization:**
- Network effects (more adoption = more value)
- Developer preference for portability
- Open-source community norms

**Forces Toward Fragmentation:**
- Vendor lock-in incentives
- Feature differentiation
- Optimization for specific agent architectures

**What to Watch:**
- Do major players (GitHub, OpenAI, Anthropic) maintain compatibility?
- Does a formal specification emerge?
- Do alternative standards gain traction?

---

### 11. Cost Economics

**Question:** How do teams balance token costs with agent effectiveness?

**Current State:** Most teams don't track agent costs systematically. "It's cheaper than developers" is the common justification.

**Why It Matters:** As usage scales, costs become significant. Optimization without measurement is guesswork.

**Cost Factors:**
- Token usage per task
- Retry/iteration costs
- Context window size choices
- Model tier selection (GPT-4 vs. 3.5, Claude Opus vs. Sonnet)

**Questions:**
- What's the cost-quality curve for different tasks?
- When is a smaller model good enough?
- How do context management strategies affect cost?
- What's the ROI measurement framework?

---

### 12. Regulatory Evolution

**Question:** How will compliance requirements adapt to agent-generated code?

**Current State:** Most regulations don't distinguish between human and AI authorship. This will change.

**Relevant Domains:**
- Financial services (SOC2, PCI-DSS)
- Healthcare (HIPAA)
- Government contracting
- Safety-critical systems

**Potential Requirements:**
- Disclosure of AI involvement
- Audit trails of agent decisions
- Human review certification
- AI-specific testing requirements

**Preparation:**
- Build comprehensive audit logging now
- Document human oversight processes
- Track AI involvement percentage
- Maintain attribution records

---

## Emerging Opportunities

### Agent-to-Agent APIs

**Opportunity:** Standards for agents to delegate work to other agents.

**Current State:** Agent orchestration is ad-hoc. No standard protocol for agent interoperability.

**Potential Value:**
- Specialized agents collaborating on complex tasks
- Agent marketplaces for specific capabilities
- Composable agent workflows

**Early Signals:**
- MCP (Model Context Protocol) provides foundation
- Multi-agent frameworks emerging (AutoGen, CrewAI)
- Interest from large players (OpenAI, Anthropic, Google)

---

### Contextual Memory

**Opportunity:** Persistent learning from repository patterns across sessions.

**Current State:** Agents start fresh each session. Learning doesn't persist.

**Potential Value:**
- Reduced context rebuilding
- Improved pattern matching over time
- Personalization to codebase style

**Challenges:**
- Privacy concerns
- Staleness of learned patterns
- Storage and retrieval efficiency

---

### Predictive Assistance

**Opportunity:** Agents that anticipate needed changes before being asked.

**Current State:** Agents are reactive. They wait for human prompts.

**Potential Value:**
- Proactive code maintenance
- Automatic dependency updates
- Anticipatory refactoring

**Examples:**
- Detect deprecated API usage, suggest migration
- Identify test coverage gaps, propose tests
- Notice code duplication, suggest refactoring

---

### Quality Prediction

**Opportunity:** Estimating code quality before human review.

**Current State:** Quality is assessed post-hoc through review. No predictive models.

**Potential Value:**
- Prioritize review effort
- Flag likely-problematic code
- Reduce review cycles

**Potential Signals:**
- Task complexity vs. output length
- Deviation from established patterns
- Iteration count during generation
- Confidence scores from agent

---

## Research to Watch

### Academic

| Topic | Why It Matters | Key Venues |
|-------|----------------|------------|
| Context utilization in LLMs | Directly impacts agent effectiveness | NeurIPS, ICML, ACL |
| Multi-agent coordination | Foundation for complex workflows | AAAI, AAMAS |
| Code generation evaluation | Better metrics for agent output | ICSE, FSE, ASE |
| Human-AI collaboration | Informs team structure evolution | CHI, CSCW |

### Industry

| Source | Focus | Why Follow |
|--------|-------|------------|
| Anthropic Engineering Blog | Agent harnesses, Claude Code | Cutting-edge agent patterns |
| GitHub Engineering | Copilot evolution, AGENTS.md | Standards and scale insights |
| DeepMind | Code generation research | Fundamental capabilities |
| Microsoft Research | Developer productivity | Enterprise adoption patterns |

### Communities

| Community | Focus |
|-----------|-------|
| r/LocalLLaMA | Open-source agent development |
| AI Engineering (Latent Space) | Practitioner experiences |
| MCP Discord | Protocol evolution |
| Agent Protocol working group | Standardization efforts |

---

## Discussion Questions for Leadership

1. **Investment Priority:** Given these unknowns, where should we invest in learning vs. wait for the field to mature?

2. **Risk Tolerance:** Which unknowns pose existential risk vs. manageable uncertainty?

3. **Competitive Position:** Which emerging opportunities, if we moved early, would create lasting advantage?

4. **Talent Strategy:** How do we prepare our team for organizational changes we can't yet predict?

5. **Standards Engagement:** Should we participate in standards bodies, or adopt standards as they emerge?

---

*This document should be revisited quarterly as the field evolves rapidly.*
