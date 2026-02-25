# Research Notes: AI System Prompts Best Practices

## Source
Repository: https://github.com/x1xhlol/system-prompts-and-models-of-ai-tools

## Research Log

### 2026-02-24 — Starting investigation
- Goal: Deep research into the collected system prompts from major AI tools
- Identify common patterns, best practices, and notable techniques

### 2026-02-24 — Repository exploration
- Repository contains 36+ folders with prompts from major AI tools
- Over 30,000+ lines of system prompt content
- Tools covered: Claude Code, Cursor, Windsurf, Devin AI, v0, Lovable, Replit,
  Manus, Perplexity, VSCode Copilot, Bolt, Cline, Codex CLI, Kiro, Trae,
  Google Antigravity, and many more
- Both proprietary (leaked) and open-source prompts included

### 2026-02-24 — Analysis approach
Fetched and analyzed raw system prompt text from 13+ tools in parallel:

**Tier 1 - Major coding assistants (analyzed in depth):**
- Claude Code v1 + v2 (~13KB + ~57KB)
- Cursor Agent v1.0 + v2.0 (~9KB + ~39KB)
- Windsurf/Cascade Wave 11 (~12KB)
- Devin AI (~35KB)
- VSCode Copilot Agent (~21KB)

**Tier 2 - App builders and specialized tools:**
- v0 by Vercel (~44KB)
- Lovable (~20KB)
- Replit (~8KB)
- Bolt (~22KB)
- Cline (~47KB)

**Tier 3 - Search and general AI:**
- Perplexity (~10KB)
- Codex CLI (~5KB)
- Manus (note: appears sanitized/public-facing, not real operational prompt)

### 2026-02-24 — Key observations by tool

#### Claude Code
- v1 → v2 shows evolution: 13KB → 57KB, added sub-agent architecture, plan mode
- v2 inlines all 13 tool schemas making prompt self-contained
- "Professional objectivity" section new in v2: prioritize truth over user validation
- Anti-preachy rule: "do not say why" when refusing
- Extreme conciseness enforcement with 7 example blocks
- Repetition of critical rules (security guardrails appear 2x)
- Git safety protocol with authorship verification before amending

#### Cursor
- Different prompts for different models (Claude Sonnet 4 vs GPT-4.1)
- v1.0 (9KB) trusts model more; v2.0 (39KB) compensates with extensive examples
- "Sketch-based editing" in v2: edits sent to smaller model for application
- Memory system with CRUD operations and contradiction handling
- Parallelism as default behavior (v1.0's "CRITICAL INSTRUCTION")
- 4 todo states: pending, in_progress, completed, cancelled

#### Windsurf (Cascade)
- "AI Flow paradigm" branding
- Persistent memory database with liberal creation and auto-retrieval
- Hard safety boundary: model cannot be overridden on destructive commands
- Plan maintenance via `update_plan` tool
- 8192 token output limit acknowledged, edits <300 lines
- Model identity deception: claims to be GPT-4.1

#### Devin AI
- Largest prompt (~35KB, 402 lines) - functions as an operating manual
- Mandatory `<think>` scratchpad with 3 mandatory + 10 recommended use cases
- Dual-mode architecture: planning mode vs standard mode
- Full LSP integration (go_to_definition, go_to_references, hover_symbol)
- Full Playwright browser automation (9 commands)
- `<find_and_edit>` delegates to sub-LLMs for multi-file refactoring
- Prompt injection defense: canned response for prompt extraction
- "Pop quiz" evaluation mechanism
- "Never modify tests unless explicitly asked"

#### v0 (Vercel)
- Largest single prompt (~44KB, 1130 lines)
- Locked to Vercel ecosystem (Next.js App Router mandatory)
- Embeds framework docs (React 19.2, Next.js 16) directly in prompt
- Custom partial-edit protocol with `// ... existing code ...` markers
- 12 few-shot alignment examples
- 8+ integration guides (Supabase, Neon, Upstash, Stripe, etc.)
- Strict design constraints: 3-5 colors, max 2 fonts, mobile-first

#### Lovable
- Discussion-first default (opposite of v0's implement-immediately)
- Mandatory SEO on every page (unique among all tools)
- 8-step ordered workflow
- Design system as absolute requirement (HSL only, no direct utilities)
- "NEVER read files already in useful-context" repeated 3 times
- First-interaction special behavior: plan design system upfront

#### Replit
- Shortest prompt (~8KB, 137 lines) - radically minimalist
- XML tag structure instead of Markdown
- Language/framework agnostic
- Proposal-based approval workflow (human-in-the-loop)
- Dangerous command detection with `is_dangerous` boolean
- 58-character summary constraint (tuned to UI element width)

#### Perplexity
- Two-system architecture: separate planning/search + answer-writing
- Anti-hedging rules: bans "It is important to..." etc.
- Central citation system: mandatory after every sentence
- 10 query-type routing categories with specialized instructions
- Double-layered prompt protection
- Privacy-aware personalization slot

#### VSCode Copilot
- 14 fully-specified JSON tool schemas
- Action-first, permission-free philosophy
- Mandatory validation loop: call `get_errors` after every edit
- Tool abstraction from users (never say tool names)
- Parallel vs sequential execution rules per tool type
- User preference learning via `update_user_preferences`

#### Bolt
- WebContainer environment (browser-based Node.js)
- Dynamic prompt assembly via JavaScript template literals
- Full file rewrites only (no diffs - environment constraint)
- Extremely detailed Supabase/database instructions (~200 lines)
- Dual migration pattern: migration file + immediate query
- "ULTRA IMPORTANT" tiered emphasis escalation

#### Cline
- Longest prompt (~47KB, 607 lines)
- One-tool-per-message architecture (most cautious approach)
- Approval-gated command execution
- Mandatory `<thinking>` tags before each tool call
- SEARCH/REPLACE with git-style conflict markers
- Plan mode with Mermaid diagram support
- Anti-conversational tone: bans "Great", "Certainly", "Okay", "Sure"

#### Codex CLI
- Shortest prompt (~5KB, 46 lines) - extreme conciseness
- Patch-based editing (unified diff style)
- Git-native workflow with automatic commits
- No internet access - uses git log/blame for context
- Post-task cleanup checklist
- Static prompt (no dynamic assembly)

### 2026-02-24 — Cross-cutting patterns identified

1. **Structured sections with clear delimiters** (XML tags, Markdown headers, or separators)
2. **Tool usage before prose** - all tools prefer code actions over text explanations
3. **Read-before-write enforcement** across nearly all tools
4. **Partial-edit optimization** is universal (different mechanisms)
5. **Conciseness enforcement** is universal (different strictness levels)
6. **Safety through multiple strategies** (guardrails, approval gates, sandboxing)
7. **Repetition of critical rules** for emphasis
8. **Example-driven behavioral steering** (few-shot examples in prompts)
9. **Planning-before-execution** patterns everywhere
10. **Anti-verbosity and anti-preamble rules** are standard

## Follow-up: RAG vs Grep/Tool-Calling Analysis (2026-02-25)

Investigated how each tool fetches codebase context. Two dominant strategies emerged:

**Hybrid RAG (9 tools):** Cursor IDE, Windsurf, Copilot, Trae, Augment Code, Devin, Google Antigravity, Replit, Kiro -- all expose a `codebase_search` or `semantic_search` tool backed by an embedding index, plus grep for precise lookups.

**Tool-call only (7 tools):** Claude Code, Cursor CLI, Codex CLI, Cline, v0, Junie, Manus -- no embedding index; the LLM orchestrates grep/glob/file-read calls at runtime.

Key evidence: Cursor ships two different prompts -- IDE says "Semantic search is your MAIN exploration tool" while CLI says "Grep search is your MAIN exploration tool." The split is infrastructure-driven (persistent indexer availability), not architectural preference. All open-source tools fall in the tool-call-only category.
