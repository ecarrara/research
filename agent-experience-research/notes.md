# Agent Experience (AX) Research Notes

## Research Log

### Session: 2026-02-02

**Objective:** Comprehensive research on Agent Experience (AX) in software engineering - emerging practices, tooling patterns, and actionable best practices for repository design.

---

## Phase 1: Research Topics Covered

- [x] Agent Experience definitions and emerging terminology
- [x] Developer Experience (DX) analogies and what transfers to AX
- [x] Coding agents (GitHub Copilot, Claude Code, Cursor, Devin, etc.)
- [x] CI/CD agents and automation patterns
- [x] Code review agents
- [x] Repository structure patterns for agent consumption
- [x] Documentation patterns for agents (AGENTS.md, llms.txt)
- [x] Task design and decomposition for agents
- [x] Agent cognitive load and context management
- [x] Failure recovery and error handling patterns
- [x] Trust calibration and verification workflows (HITL)
- [x] Security and sandboxing patterns
- [x] Model Context Protocol (MCP)
- [x] OpenAPI and machine-readable APIs
- [x] Observability and tracing

---

## Key Research Sources

### Primary Sources Consulted

1. **Matt Biilmann (Netlify CEO)** - "Introducing AX: Why Agent Experience Matters" - coined the term AX in early 2025
2. **GitHub Blog** - "How to write a great agents.md: Lessons from over 2,500 repositories"
3. **Anthropic Engineering** - "Effective harnesses for long-running agents"
4. **Factory.ai** - "The Context Window Problem: Scaling Agents Beyond Token Limits"
5. **Devin.ai** - "Coding Agents 101: The Art of Actually Getting Things Done"
6. **Continue.dev** - "Stop Asking AI to Build the Whole Feature: The Art of Focused Task Decomposition"
7. **agents.md** - Official specification site
8. **OpenTelemetry Blog** - "AI Agent Observability: Evolving Standards and Best Practices"
9. **NVIDIA AI Red Team** - Sandboxing and security guidance
10. **Speakeasy** - "Designing agent experience: A practical guide for the era of AX"
11. **Resend** - "What is AX (Agent Experience) and how to improve it"
12. **steipete/agent-rules** - Real-world agent configuration examples

### Academic/Industry Reports

- Meta & Harvard: Confucius Code Agent (CCA) research
- Carnegie Mellon: Context utilization performance studies
- Anthropic 2026 Agentic Coding Trends Report
- Microsoft Engineering: AI-powered code reviews at scale

---

## Core Findings

### 1. Definition of Agent Experience (AX)

**Origin:** Term coined by Matt Biilmann (Netlify CEO) in early 2025

**Definition:** "The holistic experience AI agents have when interacting with a product, platform, or system"

**Relationship to UX/DX:**
- UX (1993, Don Norman) → experience for users
- DX (2011, Jeremiah Lee) → experience for developers building on platforms
- AX (2025, Matt Biilmann) → experience for AI agents operating within systems

**Key Insight:** AX doesn't replace DX; it extends it. AX bridges UX and DX because agents may need to understand and serve users while also interfacing reliably with APIs and tools.

### 2. Context Window as Cognitive Load

**Critical Finding:** "A context window is not just storage capacity. It is cognitive load."

**Practical Sweet Spot:** Despite 2M-token models existing, 200K is the practical sweet spot. 150K tokens (75% of 200K) is roughly one technical book - the largest coherent "project state" both humans and LLMs can manage.

**Context Stuffing Problem:** "Providing a 2M-token context to an LLM is like handing a new developer 10,000 pages of documentation on day one and expecting them to fix a bug in five minutes."

**Research Evidence:**
- Claude Code triggers auto-compact at 75% context usage
- 23% performance degradation when context exceeds 85% capacity (Carnegie Mellon)
- Filling context to capacity degrades output quality

### 3. AGENTS.md as Cross-Tool Standard

**Adoption:** 60k+ open-source projects, 25+ compatible tools including Claude Code, Cursor, GitHub Copilot, OpenAI Codex, Google Jules

**Key Sections:**
1. Commands - executable tools with flags
2. Testing practices - framework-specific guidance
3. Project structure - file organization
4. Code style - real examples (not explanations)
5. Git workflow - commit conventions
6. Boundaries - "Always," "Ask," "Never" categories

**Insight from 2,500 repos:** "One real code snippet demonstrating your style proves more effective than paragraphs describing conventions"

### 4. Task Decomposition Critical for Success

**Problem:** "When AI tools try to handle complex, multi-step conversations, their performance drops significantly"

**Solution Categories:**
- Type 1 (Narrow): Feature flags, unit tests, boilerplate - AI excels
- Type 2 (Contextual): Debugging, refactoring - needs relevant code samples
- Type 3 (Open-ended): Full features - break into Type 1 and 2 first

**Key Benefit:** "Rather than spending hours reviewing massive code dumps, each small interaction requires only seconds of validation"

### 5. Incremental Progress Pattern (Anthropic)

**Two-Phase Architecture:**
1. Initializer agent - sets up environment on first run
2. Coding agent - makes incremental progress each session

**Clean State Requirements:**
- Descriptive git commits
- Progress file updates
- Ability to revert via git history

**Key Insight:** "Treat each agent session like a developer handoff rather than a continuous workflow"

### 6. Human-in-the-Loop (HITL) as Long-term Pattern

**Purpose:** "Human-in-the-loop is not a temporary workaround—it's a long-term pattern for building AI agents we can trust"

**Gradual Trust Building:**
- Phase 1: Wrap medium/high-risk operations with approval requirements
- Phase 2: Remove approvals from read-only operations
- Phase 3: Auto-approve certain operations for specific contexts

**Key Checkpoints:** Access approvals, configuration changes, destructive actions

### 7. Security & Sandboxing

**Core Principle:** "AI-generated code must be treated as untrusted by default. Execution boundaries must be enforced structurally, not heuristically."

**Mandatory Controls (NVIDIA AI Red Team):**
- Network egress controls
- Block file writes outside workspace
- Block writes to configuration files

**Isolation Technologies:**
- Production: Firecracker microVMs
- Development: Docker with security configs, gVisor
- Claude Code: OS-level sandboxing (bubblewrap on Linux, Seatbelt on macOS)

### 8. Model Context Protocol (MCP)

**Definition:** Open standard for connecting AI apps to external tools/data (Anthropic, Nov 2024)

**Adoption:** OpenAI, Google DeepMind, donated to Linux Foundation (Dec 2025)

**Analogy:** "Think of MCP like a USB-C port for AI applications"

**Benefit:** Reduces hallucination by providing clear paths to external, reliable data sources

### 9. AI Code Review at Scale

**Microsoft Results:**
- 90% of PRs supported by AI code reviewer
- 600K+ PRs/month impacted
- 10-20% median PR completion time improvement

**Caution:** Industry analysis found AI-generated code contained 1.7x more defects than human-written code

**Pattern:** Human reviewer role shifts from line-by-line gatekeeping to editor/architect role

### 10. Observability for Agents

**Definition:** "The ability to monitor and understand the behavior of AI-driven agents in detail"

**Why Critical:** "Agents are non-deterministic by design. They reason over intermediate steps, choose tools dynamically, and adapt their behavior at runtime."

**Three Components:**
1. Monitoring - measures predefined metrics (latency, error rates, tokens)
2. Tracing - follows execution path across steps
3. Observability - enables explanation without predefining every failure

**Standard:** OpenTelemetry GenAI semantic conventions

---

## Novel/Under-Documented Dimensions Identified

### 1. Ambiguity Surfaces
- Prompts have underspecification that cascades across decision points
- Microsoft research: explicit specifications reduced back-and-forth by 68%
- Need confidence thresholds that trigger human review

### 2. Feedback Latency
- Agents perform best with immediate feedback loops
- Access to type checkers, linters, tests, CI is critical
- "Much of the magic comes from their ability to fix their own mistakes and iterate against error messages"

### 3. Agent Cognitive Load
- Context window ≠ capability
- Need disciplined context management even with larger windows
- "Effective agentic systems must treat context the way operating systems treat memory and CPU cycles"

### 4. Trust Calibration
- Agents cannot infer "stakes" of a task
- May "overthink" simple requests or under-deliver on critical ones
- Need explicit stake signaling in task design

### 5. Recovery State Architecture
- Agents need clean recovery points (git, progress files)
- "Starting over is the right answer a lot more often with agents than with humans"
- Progress documentation enables session handoffs

### 6. Toolability vs. Documentation
- Agents can write their own SDKs if needed
- Well-structured REST API is the best "SDK" for agents
- OpenAPI spec more important than traditional docs

### 7. Time-to-First-Action
- "Agents should be able to get up and running in milliseconds"
- Won't wait for manual review/authorization
- Won't "contact sales" or "book a demo"

---

## Open Questions / What We Don't Know Yet

1. **Optimal context budgeting** - How to dynamically allocate context between reasoning headroom and retrieval?

2. **Multi-agent coordination** - How do agents share context efficiently without duplication?

3. **Trust transfer** - Can trust built in one context safely transfer to another?

4. **Semantic drift** - How do we detect when agent behavior gradually degrades without obvious error signals?

5. **Agent-native testing** - What testing patterns emerge specifically for agent-generated code?

6. **Cost optimization** - How to balance token costs with agent effectiveness?

7. **Cross-tool consistency** - Will AGENTS.md remain the standard or fragment?

8. **Regulatory compliance** - How do audit requirements evolve for agent-generated code?

---

## Key Statistics

- 85% of developers use AI tools for coding (end of 2025)
- 83.8% of Claude Code PRs accepted, ~50% merged without modification
- 1 billion AI agents predicted in service by end of FY2026 (Salesforce)
- 1,000+ sites deployed daily through ChatGPT on Netlify
- 88 AGENTS.md files in main OpenAI repo
- 150-200 instructions: what frontier LLMs can follow consistently
