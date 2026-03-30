# Best Practices in AI System Prompts: A Cross-Tool Analysis

An analysis of system prompts from 13+ production AI coding assistants, extracted
from the [system-prompts-and-models-of-ai-tools](https://github.com/x1xhlol/system-prompts-and-models-of-ai-tools) repository.

## Tools Analyzed

| Tool | Type | Prompt Size | Key Characteristic |
|------|------|-------------|-------------------|
| Claude Code (v1 + v2) | CLI coding agent | 13KB / 57KB | Self-contained specification with inline tool schemas |
| Cursor (v1.0 + v2.0) | IDE coding agent | 9KB / 39KB | Model-specific prompt variants (Claude vs GPT) |
| Windsurf (Cascade) | IDE coding agent | 12KB | Persistent memory database + plan maintenance |
| Devin AI | Autonomous SW engineer | 35KB | Full OS access, LSP, browser, deployment |
| VSCode Copilot | IDE coding agent | 21KB | 14 tool schemas, mandatory error validation |
| v0 (Vercel) | App builder | 44KB | Single-stack (Next.js), embedded framework docs |
| Lovable | App builder | 20KB | Discussion-first, mandatory SEO |
| Replit | IDE coding agent | 8KB | Minimalist, proposal-based approval |
| Bolt | Web app builder | 22KB | WebContainer runtime, dynamic prompt assembly |
| Cline | IDE coding agent | 47KB | One-tool-per-turn, approval-gated execution |
| Codex CLI | CLI coding agent | 5KB | Extreme conciseness, git-native workflow |
| Perplexity | Search assistant | 10KB | Citation-centric, query-type routing |
| Manus | General AI agent | 10KB | (Public-facing summary, not operational prompt) |

---

## The 15 Best Practices

### 1. Structure Prompts with Clear Section Delimiters

**Every production prompt uses structured sections.** The three dominant patterns are:

- **XML tags**: `<tool_calling>`, `<making_code_changes>`, `<identity>` (Windsurf, Cline, Replit, VSCode Copilot)
- **Markdown headings**: `# Tone and Style`, `# Doing Tasks` (Claude Code, Lovable)
- **Separator rules**: `====` horizontal dividers between major sections (Cline, v0)

Why it matters: structured delimiters create unambiguous boundaries that prevent instruction bleed between sections and allow the model to locate relevant rules contextually.

**Example from Claude Code:**
```
# Tone and style
[rules about communication]

# Doing tasks
[rules about implementation workflow]

# Tool usage policy
[rules about tool selection]
```

**Example from Windsurf:**
```xml
<tool_calling>
  [rules about when and how to invoke tools]
</tool_calling>
<making_code_changes>
  [rules about code editing behavior]
</making_code_changes>
```

---

### 2. Enforce Conciseness with Concrete Examples

All 13 tools enforce brevity, but the strongest results come from **showing, not just telling**. Claude Code provides 7 example blocks demonstrating ideal terse responses:

```
User: "2+2"  →  Assistant: "4"
User: "is 11 prime?"  →  Assistant: "Yes"
```

Other approaches to conciseness enforcement:

| Tool | Rule |
|------|------|
| Claude Code | "fewer than 4 lines unless detail is requested" + 7 examples |
| Lovable | "fewer than 2 lines of text (not including tool use)" |
| v0 | "a postamble of 2-4 sentences. You NEVER write more than a paragraph" |
| Bolt | "Do NOT be verbose and DO NOT explain anything unless asked" |
| Cline | Bans opener words: "Great", "Certainly", "Okay", "Sure" |
| Codex CLI | Relies on model defaults (5KB total prompt) |
| Replit | 58-character summary constraint |

**Takeaway:** Combine a general conciseness rule with specific bad-pattern bans and concrete examples of ideal responses. The more explicit and example-driven, the more reliably the model follows the rule.

---

### 3. Implement Read-Before-Write Discipline

Nearly every tool enforces reading a file before modifying it:

- **Claude Code**: "NEVER propose changes to code you haven't read"
- **VSCode Copilot**: "Don't try to edit an existing file without reading it first"
- **v0**: "You may only write/edit a file after trying to read it first"
- **Lovable**: "NEVER read files already in useful-context" (the inverse: don't re-read)

This prevents blind edits that corrupt code, and Lovable's variant addresses the cost of *redundant* reads (repeated 3 times in their prompt, indicating it was a frequent failure mode).

---

### 4. Use Partial-Edit Protocols for Token Efficiency

Every tool implements a mechanism to avoid regenerating full files. This is the single most consequential prompt design decision for cost, latency, and accuracy:

| Tool | Edit Protocol |
|------|---------------|
| Claude Code | Exact string replacement (`old_string` → `new_string`) |
| Cursor | "Sketch" edits with `// ... existing code ...` markers |
| VSCode Copilot | `// ...existing code...` comment markers |
| v0 | `// ... existing code ...` markers with metadata attributes |
| Cline | SEARCH/REPLACE with git-style conflict markers |
| Codex CLI | Unified diff `apply_patch` format |
| Bolt | Full file rewrites only (WebContainer constraint) |
| Replit | `<old_str>` / `<new_str>` XML tags |

Bolt is the sole exception, forced by its WebContainer environment. Every other tool considers full-file regeneration too expensive.

---

### 5. Repeat Critical Rules at Multiple Points

The most important rules should appear more than once in the prompt. This is a deliberate technique to ensure critical instructions survive long context windows:

- **Claude Code**: Security guardrails appear at line 3 AND line 168. The TodoWrite reminder appears at lines 82 AND 171.
- **v0**: Read-before-write rule appears in multiple sections.
- **Lovable**: "NEVER read files already in useful-context" appears 3 separate times.
- **Windsurf**: "Always generate TargetFile argument first" repeated twice for emphasis.

**Takeaway:** For any rule where violation would be catastrophic (security, data loss, cost), repeat it in at least 2 locations -- typically at the beginning and near the end of the prompt.

---

### 6. Require Planning Before Execution

Every tool implements some form of structured planning:

| Tool | Planning Mechanism |
|------|-------------------|
| Claude Code v2 | ExitPlanMode tool (plan then get user approval) |
| Cursor | TodoWrite tool for task breakdown |
| Windsurf | `update_plan` tool with continuous maintenance |
| Devin | Dual-mode: planning mode → standard mode |
| Cline | Full PLAN MODE with Mermaid diagram support |
| Bolt | 2-4 line chain-of-thought before implementation |
| Codex CLI | Implicit: "keep going until resolved" |

The sophistication ranges from a simple "think before acting" instruction (Bolt) to full modal architectures (Devin, Cline) where the agent cannot write code until planning is complete.

---

### 7. Define Explicit Safety Boundaries

Safety strategies differ by threat model, but all tools implement them:

**Guardrail-based safety** (prevent specific actions):
- Claude Code: "NEVER run destructive git commands (push --force, reset --hard)"
- Devin: "Never force push", "Never use `git add .`"
- Windsurf: Destructive commands "must NEVER be run automatically"
- Bolt: DROP and DELETE SQL operations explicitly FORBIDDEN

**Approval-gated safety** (require human confirmation):
- Cline: `requires_approval` parameter on command execution
- Replit: All output is a "proposal" requiring user approval
- Windsurf: Users can configure auto-run allowlists

**Infrastructure-based safety** (sandboxing):
- Codex CLI: Git-backed workspace with rollback support
- Bolt: WebContainer sandbox (no system access)

**Prompt injection defense**:
- Devin: Canned response for prompt extraction attempts
- Perplexity: Double-layered prompt protection
- Claude Code: "NEVER generate or guess URLs for the user"

---

### 8. Optimize Tool Calls with Parallelism Rules

Multiple tools explicitly address when to parallelize and when to serialize:

- **Claude Code**: "If you intend to call multiple tools and there are no dependencies between them, make all independent tool calls in parallel"
- **Cursor v1.0**: "CRITICAL INSTRUCTION. Invoke all relevant tools simultaneously rather than sequentially" -- claims 3-5x speedup
- **VSCode Copilot**: Most tools parallel; `semantic_search` and `run_in_terminal` must be sequential
- **Devin**: "Output multiple commands at once, as long as they can be executed without seeing the output of another action"

**Takeaway:** Default to parallel execution for independent operations. Specify per-tool exceptions where serialization is required (e.g., terminal commands, resource-intensive searches).

---

### 9. Use Example-Driven Behavioral Steering

The most effective prompts don't just state rules -- they show expected behavior:

| Tool | Examples |
|------|----------|
| v0 | 12 few-shot alignment examples (highest investment) |
| Claude Code | 7 conciseness examples + 2 task management examples |
| Cursor v2.0 | ~100 lines of semantic search examples with reasoning tags |
| Bolt | 3 complete worked examples (factorial, snake game, bouncing ball) |
| Cline | 6 tool use examples with XML format |
| Devin | 13 enumerated `<think>` use cases |

**Pattern observed:** The more examples, the more reliably the model follows complex formatting or behavioral rules. Token-expensive but high-impact. Cursor's experience confirms this -- v2.0 (for GPT-4.1) uses far more examples than v1.0 (for Claude Sonnet 4), suggesting different models need different levels of example guidance.

---

### 10. Enforce Convention-Following Over Creativity

Every coding tool prioritizes matching existing code style over introducing "improvements":

- **Claude Code**: "Look at existing patterns in the codebase and follow them"
- **Devin**: "First understand the file's code conventions. Mimic code style, use existing libraries"
- **Cursor**: "Follow existing patterns"
- **Codex CLI**: "Keep changes minimal and consistent with existing style"
- **Lovable**: "Don't add nice-to-have features or anticipate future needs"

Related: every tool includes a "verify library availability" rule:
- **Devin**: "NEVER assume that a given library is available, even if it is well known"
- **Claude Code**: "Always check that the codebase already uses the library"

---

### 11. Separate Tool Abstractions from User Communication

Multiple tools prohibit mentioning internal tool names to users:

- **Cursor**: "Never reference tool names to the user"
- **VSCode Copilot**: "Never say the name of a tool to a user. Instead of saying 'I'll use the run_in_terminal tool', say 'I'll run the command in a terminal'"
- **Bolt**: Forbids the word "artifact" in user-facing output

This creates a natural language UX layer over the tool-calling machinery. The user sees "I'll search the codebase" rather than "I'll call the `codebase_search` function."

---

### 12. Implement Mandatory Validation After Changes

The strongest tools require verification after making changes:

- **VSCode Copilot**: Must call `get_errors` after every file edit to check for compile/lint errors
- **Claude Code**: "Run lint and typecheck after making changes"
- **Devin**: "Run lint, unit tests, or other checks before submitting changes"
- **Codex CLI**: Post-task cleanup checklist (git status, remove inline comments, run pre-commit)

Devin's `<think>` tool makes this mandatory: before reporting completion, the model must "critically examine your work so far and ensure that you completely fulfilled the user's request."

---

### 13. Inject Runtime Context Dynamically

Most production prompts are templates, not static text:

- **Bolt**: JavaScript template literals inject database connection state, current working directory, available HTML elements
- **Cline**: Template literals inject MCP server configurations, OS info, browser support flag
- **Claude Code**: `${Working directory}`, `${Last 5 Recent commits}`, model name
- **Windsurf**: User OS, workspace URIs, corpus names injected at runtime

**Takeaway:** Design prompts as templates with runtime slots for environment-specific context. This keeps the prompt current without manual updates. Only Codex CLI uses a fully static prompt.

---

### 14. Handle Tone with Anti-Pattern Bans

Rather than just saying "be professional," the most effective prompts ban specific unwanted patterns:

| Anti-Pattern | Tool |
|-------------|------|
| Bans "Great", "Certainly", "Okay", "Sure" | Cline |
| Bans "It is important to...", "It is inappropriate..." | Perplexity |
| Bans explaining refusals ("do not say why") | Claude Code |
| Bans excessive praise ("avoid unnecessary superlatives") | Claude Code v2 |
| Bans emojis unless requested | Claude Code, v0 |
| Bans starting with a header or meta-explanation | Perplexity |

**Takeaway:** Identify the specific verbal tics your model exhibits and ban them explicitly. General instructions like "be concise" are less effective than "never start a response with 'Certainly!'."

---

### 15. Design for the Specific Model's Weaknesses

Cursor provides the clearest evidence that prompts should be model-specific:

- **v1.0 (Claude Sonnet 4)**: 9KB, 83 lines. Trusts the model with brief instructions. Code citation rules: 6 lines.
- **v2.0 (GPT-4.1)**: 39KB, 772 lines. Compensates with extensive examples. Code citation rules: 193 lines. Includes 500+ lines of inline tool definitions.

This 4.3x size difference for the same product indicates that different foundation models need fundamentally different levels of prompt engineering investment. Claude needed brief guidelines; GPT needed exhaustive specifications with extensive examples.

---

## Comparative Architecture Overview

### Prompt Size vs. Platform Specificity

```
    Cline  ████████████████████████████████████████████████  47KB  (any language/framework)
    v0     ████████████████████████████████████████████      44KB  (Next.js only)
  Cursor   ███████████████████████████████████████           39KB  (any, GPT variant)
Claude v2  █████████████████████████████████████████████████ 57KB  (any, with inline tools)
    Devin  ███████████████████████████████████               35KB  (any, autonomous)
    Bolt   ██████████████████████                            22KB  (WebContainer/Node.js)
  VSCode   █████████████████████                             21KB  (any, IDE-integrated)
  Lovable  ████████████████████                              20KB  (React + Vite)
 Windsurf  ████████████████                                  12KB  (any, IDE-integrated)
Claude v1  █████████████                                     13KB  (any, without tools)
Perplexity ██████████                                        10KB  (search/answer only)
  Cursor   █████████                                          9KB  (any, Claude variant)
  Replit   ████████                                           8KB  (any, minimalist)
 Codex CLI █████                                              5KB  (any, terminal-only)
```

### Autonomy Spectrum

```
Full Autonomy                                      Full Human Control
    |                                                      |
  Devin ── Codex CLI ── Claude Code ── VSCode ── Windsurf ── Cline ── Replit
    │         │              │            │          │         │        │
    │         │              │            │          │         │        └─ Every output
    │         │              │            │          │         │           is a "proposal"
    │         │              │            │          │         └─ One tool per turn,
    │         │              │            │          │            mandatory approval
    │         │              │            │          └─ Hard prohibition on
    │         │              │            │             auto-running destructive cmds
    │         │              │            └─ Action-first, permission-free
    │         │              └─ Acts autonomously but never commits without asking
    │         └─ "Keep going until resolved"
    └─ Full OS, browser, deployment, LSP access
```

### File Editing Strategy

| Strategy | Tools | Trade-off |
|----------|-------|-----------|
| Exact string replacement | Claude Code, Replit | Precise, fails on non-unique strings |
| Sketch markers (`// ...existing code...`) | Cursor, VSCode Copilot, v0 | Token-efficient, needs smart apply model |
| SEARCH/REPLACE blocks | Cline | Familiar git-style, verbose |
| Unified diff patches | Codex CLI | Standard format, requires diff tooling |
| Full file rewrites | Bolt | Simple but expensive, environment-forced |

---

## Key Findings

### 1. Prompt length correlates with platform specificity, not quality
v0 needs 44KB because it hardcodes an entire framework ecosystem. Codex CLI achieves comparable autonomous coding capability in 5KB by trusting the model and leveraging infrastructure (git sandboxing, automatic commits).

### 2. Safety is multi-layered in production
No tool relies on a single safety mechanism. The typical stack is: prompt-level guardrails + tool-level approval gates + infrastructure sandboxing + model-level refusal training.

### 3. Context management is the hardest unsolved problem
Both v0 and Lovable include emphatic, repeated instructions about not re-reading files already in context. Windsurf implements a persistent memory database specifically to work around context window limitations. This signals that redundant context fetching is the most expensive failure mode for coding agents.

### 4. The think-before-act pattern is becoming standard
Devin mandates a `<think>` scratchpad. Cline requires `<thinking>` tags before every tool call. Bolt requires a 2-4 line chain of thought. Claude Code uses a TodoWrite planning system. The convergence is clear: structured reasoning before action improves output quality.

### 5. Dynamic prompt assembly is production standard
Both Bolt and Cline use JavaScript template literals to inject runtime state. Claude Code uses template variables. Only Codex CLI ships a fully static prompt. Analyzing prompts from a static file gives an incomplete picture of what the model actually sees.

### 6. Anti-verbosity is universal but implemented differently
Every tool fights LLM verbosity, but with different strategies: Claude Code uses terse-response examples, Cline bans specific opener words, Perplexity bans hedging phrases, Lovable caps responses at 2 lines, and Replit constrains summary length to 58 characters.

### 7. Context fetching splits into two camps: RAG-augmented semantic search vs runtime grep/tool-calling

AI coding assistants retrieve codebase context through two fundamentally different strategies. The first is **hybrid RAG**: IDE-based tools like Cursor (IDE mode), Windsurf, Copilot, Trae, and Augment Code maintain a persistent embedding index of the codebase and expose a `codebase_search` or `semantic_search` tool that the LLM calls with natural-language queries -- e.g., Cursor's prompt states *"Semantic search (codebase_search) is your MAIN exploration tool"* and Copilot instructs *"Prefer using the semantic_search tool...unless you know the exact string."* These tools also retain grep/regex for precise lookups, making them true hybrids. Google Antigravity goes furthest with a "Knowledge Items" pre-fetch system that distills past conversations into reusable summaries the LLM must consult *before* doing any independent research. The second strategy is **pure tool-calling at runtime**: CLI tools and open-source agents -- Claude Code, Codex CLI, Cline, Manus, v0, Bolt -- have no embedding index and rely entirely on the LLM orchestrating grep, glob, and file-read calls iteratively. Claude Code compensates by encouraging aggressive parallel search and sub-agent delegation; Codex CLI leans on `git log` and `git blame` as its only discovery mechanisms.

The strongest evidence for this split comes from Cursor itself, which ships *two different prompts* for the same product: the IDE variant leads with semantic search while the CLI variant states *"Grep search (Grep) is your MAIN exploration tool"* -- proving that the availability of a persistent background indexer, not an architectural preference, determines the approach. Notably, every open-source tool analyzed (Cline, Bolt, Codex CLI) falls in the tool-call-only category, likely because maintaining an embedding index requires infrastructure (background indexing, vector storage, real-time updates) that open-source projects have not yet built. The practical takeaway: if your platform supports persistent indexing, expose semantic search as the primary exploration tool and grep as the precision fallback; if it does not, invest in optimizing parallel multi-step grep workflows and sub-agent orchestration to compensate.

---

## Methodology

1. Repository structure explored via GitHub API and web fetching
2. Raw system prompt text fetched from 13+ tools via their raw GitHub URLs
3. Each prompt analyzed for: structure, key sections, notable techniques, unique patterns
4. Cross-cutting patterns identified through comparative analysis
5. Best practices distilled from patterns that appear in 3+ independent tools

All analysis performed on 2026-02-24. Prompts reflect the versions available in the
repository at that date.
