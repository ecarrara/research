# References & Further Reading

**Curated resources for learning more about Agent Experience**

---

## Table of Contents

1. [Foundational Articles](#foundational-articles)
2. [Agent Instructions & Standards](#agent-instructions--standards)
3. [Context Management](#context-management)
4. [Task Design & Decomposition](#task-design--decomposition)
5. [Security & Sandboxing](#security--sandboxing)
6. [Observability](#observability)
7. [Human-in-the-Loop](#human-in-the-loop)
8. [Tools & Platforms](#tools--platforms)
9. [Specifications & Protocols](#specifications--protocols)
10. [Community Resources](#community-resources)
11. [Academic Research](#academic-research)

---

## Foundational Articles

### Defining Agent Experience

- **[Introducing AX: Why Agent Experience Matters](https://biilmann.blog/articles/introducing-ax/)**
  Matt Biilmann (Netlify CEO) • The article that coined "Agent Experience" • Explains why AX is the natural evolution from UX and DX

- **[Agent Experience (AX): Designing for AI Agents as First-Class Users](https://valanor.co/agent-experience/)**
  Valanor • Comprehensive overview of AX principles • Good starting point for newcomers

- **[What is AX (Agent Experience) and how to improve it](https://resend.com/blog/agent-experience)**
  Resend • Practical perspective on API design for agents • Focused on email/communications

- **[Designing agent experience: A practical guide](https://www.speakeasy.com/blog/agent-experience-introduction)**
  Speakeasy • API-focused AX guide • Covers toolability, recoverability, traceability

- **[The Rise of AI-First Developer Experience (DX 2.0)](https://kenneth.io/post/the-rise-of-ai-first-developer-experience-dx-20)**
  Kenneth Auchenberg • How DX evolves with AI • Bridge between DX and AX thinking

### Agentic Coding Overview

- **[Agentic Coding: Autonomous Code Engineering](https://www.emergentmind.com/topics/agentic-coding)**
  EmergentMind • Research synthesis on agentic coding • Good for understanding the landscape

- **[What is agentic coding? How it works and use cases](https://cloud.google.com/discover/what-is-agentic-coding)**
  Google Cloud • Enterprise perspective on agentic coding • Use cases and considerations

- **[From Code Completion to Autonomous Development](https://deniskisina.dev/posts/agentic-coding-revolution/)**
  Denis Kisina • Evolution of coding assistance • Historical context

---

## Agent Instructions & Standards

### AGENTS.md

- **[AGENTS.md Official Specification](https://agents.md/)**
  Official site • Format definition • Compatible tools list

- **[How to write a great agents.md](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/)**
  GitHub Blog • Analysis of 2,500+ repositories • Best practices and common patterns

- **[steipete/agent-rules](https://github.com/steipete/agent-rules)**
  GitHub Repository • Real-world agent configuration examples • Multiple tool configs

### llms.txt

- **[llms.txt Specification](https://llmstxt.org/)**
  Jeremy Howard / Answer.AI • Proposed standard for LLM-friendly documentation

### Writing Effective Specs

- **[How to write a good spec for AI agents](https://addyosmani.com/blog/good-spec/)**
  Addy Osmani • Detailed guide on writing agent-parseable specifications

---

## Context Management

### Context Windows

- **[The Context Window Problem: Scaling Agents Beyond Token Limits](https://factory.ai/news/context-window-problem)**
  Factory.ai • Deep dive on context management • The "context stack" pattern

- **[Understanding LLM Context Windows](https://medium.com/@tahirbalarabe2/understanding-llm-context-windows-tokens-attention-and-challenges-c98e140f174d)**
  Tahir Balarabe • Technical explanation of context windows • Attention mechanics

- **[The 2M Token Trap: Why Context Stuffing Kills Reasoning](https://dev.to/xhack/the-2m-token-trap-why-context-stuffing-kills-reasoning-1kck)**
  Dev.to • Why more context isn't always better • Practical implications

- **[Context Window Management Strategies](https://www.getmaxim.ai/articles/context-window-management-strategies-for-long-context-ai-agents-and-chatbots/)**
  Maxim • Strategies for managing context in production

### Long-Running Agents

- **[Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)**
  Anthropic Engineering • How to maintain progress across sessions • Progress tracking patterns

---

## Task Design & Decomposition

- **[Stop Asking AI to Build the Whole Feature](https://blog.continue.dev/task-decomposition/)**
  Continue.dev • The art of focused task decomposition • Task categorization framework

- **[Coding Agents 101: The Art of Getting Things Done](https://devin.ai/agents101)**
  Devin.ai • Practical guide to working with coding agents • Common mistakes to avoid

---

## Security & Sandboxing

### Security Guidance

- **[Practical Security Guidance for Sandboxing Agentic Workflows](https://developer.nvidia.com/blog/practical-security-guidance-for-sandboxing-agentic-workflows-and-managing-execution-risk)**
  NVIDIA AI Red Team • Mandatory security controls • Defense in depth patterns

- **[How Code Execution Drives Key Risks in Agentic AI Systems](https://developer.nvidia.com/blog/how-code-execution-drives-key-risks-in-agentic-ai-systems/)**
  NVIDIA • Risk analysis of agent code execution

- **[Hardening Best Practices: Sandboxing, Least Privilege](https://skywork.ai/blog/ai-agent/hardening-best-practices-sandboxing-least-privilege-data-exfiltration/)**
  Skywork • Comprehensive hardening guide

### Sandboxing Tools

- **[Claude Code Sandboxing Documentation](https://code.claude.com/docs/en/sandboxing)**
  Anthropic • How Claude Code implements sandboxing

- **[What's the best code execution sandbox for AI agents](https://northflank.com/blog/best-code-execution-sandbox-for-ai-agents)**
  Northflank • Comparison of sandbox options • Selection criteria

- **[The Complete Guide to Sandboxing Autonomous Agents](https://www.ikangai.com/the-complete-guide-to-sandboxing-autonomous-agents-tools-frameworks-and-safety-essentials/)**
  IkangAI • Tools, frameworks, and safety essentials

- **[Code Sandboxes for LLMs and AI Agents](https://amirmalik.net/2025/03/07/code-sandboxes-for-llm-ai-agents)**
  Amir Malik • Technical deep-dive on sandbox implementations

- **[awesome-sandbox](https://github.com/restyler/awesome-sandbox)**
  GitHub • Curated list of code sandboxing solutions

---

## Observability

### Standards & Best Practices

- **[AI Agent Observability: Evolving Standards and Best Practices](https://opentelemetry.io/blog/2025/ai-agent-observability/)**
  OpenTelemetry • Official guidance on agent observability • Semantic conventions

- **[Mastering AI agent observability](https://medium.com/online-inference/mastering-ai-agent-observability-a-comprehensive-guide-b142ed3604b1)**
  Online Inference • Comprehensive observability guide

- **[AI Agent Observability Explained](https://uptrace.dev/blog/ai-agent-observability)**
  Uptrace • Key concepts and implementation patterns

- **[Observability in Agentic AI: Core Tracing, Budgets, and Caching](https://servicesground.com/blog/observability-in-agentic-ai-guide/)**
  ServicesGround • Practical observability patterns

### Platforms

- **[Fiddler Agentic Observability](https://www.fiddler.ai/agentic-observability)**
  Fiddler • Commercial observability platform for agents

- **[AI Agent Observability Platform - Openlayer](https://www.openlayer.com/products/ai-agent-observability)**
  Openlayer • Observability and debugging tools

---

## Human-in-the-Loop

- **[Human-in-the-Loop AI: Complete Guide](https://parseur.com/blog/human-in-the-loop-ai)**
  Parseur • Benefits, best practices, and trends for 2026

- **[Human-in-the-Loop for AI Agents: Best Practices](https://www.permit.io/blog/human-in-the-loop-for-ai-agents-best-practices-frameworks-use-cases-and-demo)**
  Permit.io • Frameworks and use cases • Implementation patterns

- **[Trustworthy AI Agents: Human-in-the-Loop Governance](https://www.sakurasky.com/blog/missing-primitives-for-trustworthy-ai-part-16/)**
  SakuraSky • Governance perspective on HITL

- **[Human-in-the-loop in AI workflows: Patterns](https://zapier.com/blog/human-in-the-loop/)**
  Zapier • Practical patterns for HITL automation

- **[Implement human-in-the-loop with Amazon Bedrock Agents](https://aws.amazon.com/blogs/machine-learning/implement-human-in-the-loop-confirmation-with-amazon-bedrock-agents/)**
  AWS • Implementation guide for Bedrock

---

## Tools & Platforms

### Coding Agents

| Tool | Description | Link |
|------|-------------|------|
| Claude Code | Anthropic's CLI coding agent | [Documentation](https://code.claude.com/) |
| GitHub Copilot | AI pair programmer | [Website](https://github.com/features/copilot) |
| Cursor | AI-first code editor | [Website](https://cursor.sh/) |
| Continue | Open-source coding assistant | [GitHub](https://github.com/continuedev/continue) |
| Devin | Autonomous AI software engineer | [Website](https://devin.ai/) |
| Cody | Sourcegraph's AI coding assistant | [Website](https://sourcegraph.com/cody) |

### Agent Frameworks

| Framework | Focus | Link |
|-----------|-------|------|
| LangChain | LLM application framework | [Website](https://langchain.com/) |
| LangGraph | Stateful agent workflows | [Documentation](https://langchain-ai.github.io/langgraph/) |
| AutoGen | Multi-agent conversations | [GitHub](https://github.com/microsoft/autogen) |
| CrewAI | Multi-agent orchestration | [Website](https://www.crewai.com/) |
| Semantic Kernel | Microsoft's AI orchestration | [GitHub](https://github.com/microsoft/semantic-kernel) |

### Sandboxing Solutions

| Solution | Type | Link |
|----------|------|------|
| E2B | Cloud sandbox | [Website](https://e2b.dev/) |
| Firecracker | MicroVM | [GitHub](https://github.com/firecracker-microvm/firecracker) |
| gVisor | Container sandbox | [Website](https://gvisor.dev/) |
| Modal | Serverless sandbox | [Website](https://modal.com/) |

---

## Specifications & Protocols

- **[Model Context Protocol (MCP)](https://modelcontextprotocol.io/)**
  Anthropic • Open standard for connecting AI to external tools • Donated to Linux Foundation

- **[OpenAPI Specification](https://www.openapis.org/)**
  OpenAPI Initiative • Standard for API documentation • Critical for agent toolability

- **[JSON Schema](https://json-schema.org/)**
  JSON Schema Organization • Data validation standard • Used in tool definitions

- **[OpenTelemetry](https://opentelemetry.io/)**
  CNCF • Observability framework • GenAI semantic conventions

---

## Community Resources

### Discussion Forums

- **[r/LocalLLaMA](https://reddit.com/r/LocalLLaMA)**
  Reddit • Open-source LLM development community

- **[Latent Space Discord](https://discord.gg/latentspace)**
  AI Engineering community • Practitioner discussions

- **[LangChain Discord](https://discord.gg/langchain)**
  LangChain community • Agent development discussions

### Newsletters & Podcasts

- **[Latent Space Podcast](https://www.latent.space/)**
  AI Engineering podcast • Interviews with practitioners

- **[The AI Engineer](https://theaiengineering.substack.com/)**
  Newsletter on AI engineering practices

- **[Simon Willison's Weblog](https://simonwillison.net/)**
  LLM tools and techniques • Practical insights

### Example Repositories

- **[anthropics/anthropic-cookbook](https://github.com/anthropics/anthropic-cookbook)**
  Anthropic • Examples and best practices

- **[openai/openai-cookbook](https://github.com/openai/openai-cookbook)**
  OpenAI • Examples and guides

- **[microsoft/semantic-kernel](https://github.com/microsoft/semantic-kernel)**
  Microsoft • AI orchestration examples

---

## Academic Research

### Key Papers

- **"Lost in the Middle: How Language Models Use Long Contexts"**
  Liu et al., 2023 • Stanford • Performance degradation in long contexts

- **"AI Agentic Programming: A Survey"**
  arXiv 2508.11126 • Comprehensive survey of agentic techniques

- **"Agentic Software Engineering: Foundational Pillars"**
  arXiv 2509.06216 • Research roadmap for agentic SE

- **"AI IDEs or Autonomous Agents? Measuring Impact"**
  arXiv 2601.13597 • Empirical study of coding agent impact

### Relevant Venues

| Venue | Focus |
|-------|-------|
| NeurIPS | Machine learning foundations |
| ICML | Machine learning methods |
| ACL/EMNLP | Natural language processing |
| ICSE | Software engineering |
| FSE | Software engineering foundations |
| CHI | Human-computer interaction |

---

## Industry Reports

- **[Anthropic 2026 Agentic Coding Trends Report](https://resources.anthropic.com/hubfs/2026%20Agentic%20Coding%20Trends%20Report.pdf)**
  Anthropic • State of agentic coding • Trends and predictions

- **[GitHub Octoverse](https://github.blog/news-insights/octoverse/)**
  GitHub • Developer trends and AI adoption data

- **[Stack Overflow Developer Survey](https://stackoverflow.com/survey)**
  Stack Overflow • Annual developer trends including AI tool usage

---

## Quick Reference Card

**Start Here:**
1. [AGENTS.md Specification](https://agents.md/) - The standard
2. [GitHub's AGENTS.md Guide](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/) - Best practices
3. [Factory.ai Context Management](https://factory.ai/news/context-window-problem) - Critical concept

**Go Deeper:**
4. [Anthropic's Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) - Advanced patterns
5. [NVIDIA Security Guidance](https://developer.nvidia.com/blog/practical-security-guidance-for-sandboxing-agentic-workflows-and-managing-execution-risk) - Security essentials
6. [OpenTelemetry AI Observability](https://opentelemetry.io/blog/2025/ai-agent-observability/) - Monitoring standards

---

*Last updated: February 2026*

*Found a useful resource? The field is evolving rapidly—keep this list updated.*
