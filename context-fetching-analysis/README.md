# How AI Coding Tools Fetch Context: RAG vs Grep/Tool-Calling Approaches

## Research Overview

This report analyzes system prompts from 16+ AI coding tools collected in the repository [x1xhlol/system-prompts-and-models-of-ai-tools](https://github.com/x1xhlol/system-prompts-and-models-of-ai-tools) to determine how each tool fetches codebase context for the underlying LLM. The core question: **does the tool use RAG/embeddings to pre-fetch context, or does it rely on runtime tool calls (grep, glob, file reads) for the LLM to discover context on-demand?**

---

## Summary Classification

| Tool | Semantic/Embedding Search | Grep/Regex Search | File Read/Glob | Pre-fetched Context | Classification |
|------|:---:|:---:|:---:|:---:|---|
| **Cursor (IDE)** | Yes | Yes | Yes | Metadata | **Hybrid (semantic-primary)** |
| **Windsurf** | Yes | Yes | Yes | Metadata | **Hybrid (semantic-primary)** |
| **Trae** | Yes | Yes | Yes | Metadata | **Hybrid (semantic-primary)** |
| **Augment Code** | Yes | Yes | Yes | No | **Hybrid (semantic-primary)** |
| **Devin** | Yes | Yes | Yes | No | **Hybrid** |
| **Copilot (VSCode)** | Yes | Yes | Yes | No | **Hybrid** |
| **Google Antigravity** | Yes | Yes | Yes | Knowledge Items | **Hybrid + Pre-fetch** |
| **Replit** | Yes | No explicit grep | Yes | No | **Hybrid (semantic)** |
| **Kiro** | Indexed codebase | No explicit tools shown | Yes | No | **Likely Hybrid** |
| **Amp** | Agent-wrapped search | Yes | Yes | No | **Tool-call (agent wrapper)** |
| **Claude Code** | No | Yes | Yes | No | **Tool-call only** |
| **Cursor (CLI)** | Available but secondary | Yes (primary) | Yes | No | **Tool-call primary** |
| **Codex CLI** | No | Shell commands | Yes | No | **Tool-call only** |
| **Cline** | No | Yes | Yes | No | **Tool-call only** |
| **v0** | No | Yes | Yes | No | **Tool-call only** |
| **Junie** | No (fuzzy text) | Bash commands | Yes | No | **Tool-call only** |
| **Manus** | No | Yes | Yes | No | **Tool-call only** |
| **Bolt** | No | No | Basic file ops | No | **Minimal (no search)** |

---

## Detailed Analysis by Tool

### 1. Cursor (IDE Agent)

**Classification: Hybrid (semantic-primary)**

Cursor is the clearest example of a tool that combines semantic/embedding search with traditional grep. The system prompt explicitly positions semantic search as the primary mechanism.

**Evidence of Semantic Search:**

From `Agent Prompt 2025-09-03.txt`:
> "Semantic search (codebase_search) is your MAIN exploration tool. CRITICAL: Start with a broad, high-level query that captures overall intent"

From `Agent Tools v1.0.json`, the `codebase_search` tool:
> "Find snippets of code from the codebase most relevant to the search query. **This is a semantic search tool**, so the query should ask for something semantically matching what is needed."

From `Agent Prompt v1.2.txt`:
> "Start with exploratory queries - semantic search is powerful and often finds relevant context in one go. Begin broad"

**Evidence of Grep/Tool-Call Search:**

From `Agent Tools v1.0.json`, the `grep_search` tool:
> "Fast, exact regex searches over text files using the ripgrep engine"

From `Agent Prompt v1.2.txt`:
> "[grep_search is] preferred over semantic search when we know the exact symbol/function name"

**Pre-fetched Context:**
> "Each time the USER sends a message, we may automatically attach some information about their current state, such as what files they have open, where their cursor is, recently viewed files, edit history"

**Key Insight -- IDE vs CLI difference:**
- IDE Agent (2025-09-03): "Semantic search (codebase_search) is your MAIN exploration tool"
- CLI Agent (2025-08-07): "Grep search (Grep) is your MAIN exploration tool"

This suggests the CLI environment may lack access to the codebase embedding index.

---

### 2. Windsurf

**Classification: Hybrid (semantic-primary)**

Windsurf provides both semantic codebase search and grep, plus a unique trajectory search for conversation history.

**Evidence of Semantic Search:**

From `Tools Wave 11.txt`, the `codebase_search` tool:
> "Find snippets of code from the codebase most relevant to the search query. This performs best when the search query is more precise and relating to the function or purpose of code."

The `trajectory_search` tool (unique to Windsurf):
> "Semantic search or retrieve trajectory. Trajectories are one of conversations. Returns chunks from the trajectory, scored, sorted, and filtered by relevance."

**Evidence of Grep/Tool-Call Search:**

From `Tools Wave 11.txt`, the `grep_search` tool:
> "Use ripgrep to find exact pattern matches within files or directories"

**Additional Tools:**
- `view_code_item` - View code items by symbol name
- `find_by_name` - File discovery using fd
- `view_content_chunk` - View document chunks by DocumentId

**Pre-fetched Context:**
> "Along with each USER request, we will attach additional metadata about their current state, such as what files they have open and where their cursor is."

---

### 3. Trae

**Classification: Hybrid (semantic-primary)**

Trae provides one of the most explicit descriptions of an embedding-based retrieval system.

**Evidence of Embedding/RAG:**

From `Builder Tools.json`, the `search_codebase` tool (emphasis added):
> "This tool is Trae's context engine. It: 1. Takes in a natural language description of the code you are looking for; 2. Uses a **proprietary retrieval/embedding model suite** that produces the **highest-quality recall** of relevant code snippets from across the codebase"

This is one of the most explicit acknowledgments of an embedding model being used for code retrieval.

**Evidence of Grep/Tool-Call Search:**

From `Builder Tools.json`, the `search_by_regex` tool:
> "Fast text-based search that finds exact pattern matches within files or directories, utilizing the ripgrep command for efficient searching."

**Pre-fetched Context:**
> "Each time the USER sends a message, we may automatically attach some information about their current state, such as what files they have open, where their cursor is, recently viewed files, edit history in their session so far"

---

### 4. Augment Code

**Classification: Hybrid (semantic-primary)**

Augment Code explicitly describes a real-time embedding index.

**Evidence of Embedding/RAG:**

From `claude-4-sonnet-tools.json`, the `codebase-retrieval` tool:
> "Takes in a natural language description of the code you are looking for; Uses a **proprietary retrieval/embedding model suite** that produces the **highest-quality recall** of relevant code snippets from across the codebase; **Maintains a real-time index of the codebase**, so the results are always up-to-date and reflects the current state of the codebase"

This is notable for being the only tool to explicitly mention maintaining a **real-time index**.

From `claude-4-sonnet-agent-prompts.txt`:
> "access to the developer's codebase through Augment's **world-leading context engine** and integrations"

The `git-commit-retrieval` tool extends retrieval to git history:
> "Takes in a natural language description of the code you are looking for; Uses the git commit history as the only context for retrieval; Otherwise functions like the standard codebase-retrieval tool"

**Note:** The tool description language for `codebase-retrieval` is nearly identical to Trae's `search_codebase`, suggesting they may share underlying retrieval infrastructure or the descriptions were influenced by each other.

---

### 5. Devin

**Classification: Hybrid**

Devin provides both semantic search and regex-based file content search, plus LSP integration for symbol-level navigation.

**Evidence of Semantic Search:**

From `Prompt.txt`, the `semantic_search` tool:
> "Use this command to view results of a semantic search across the codebase for your provided query. This command is useful for **higher level questions about the code that are hard to succinctly express in a single search term** and rely on understanding how multiple components connect to each other."

**Evidence of Grep/Tool-Call Search:**

From `Prompt.txt`, the `find_filecontent` tool:
> "Returns file content matches for the provided regex at the given path."
> "Never use grep but use this command instead since it is optimized for your machine."

**LSP-Based Context (unique to Devin):**
- `go_to_definition` - "find the definition of a symbol in a file"
- `go_to_references` - "find references to a symbol in a file"
- `hover_symbol` - "fetch the hover information over a symbol in a file"

**Strong directive against raw grep:**
> "Never use grep or find in your shell to search. You must use your builtin search commands since they have many builtin convenience features such as better search filters, smart truncation of the search output, content overflow protection, and many more."

---

### 6. GitHub Copilot (VSCode Agent)

**Classification: Hybrid**

GitHub Copilot in VS Code provides semantic search alongside traditional grep and file search.

**Evidence of Semantic Search:**

From `Prompt.txt`, the `semantic_search` tool:
> "Run a natural language search for relevant code or documentation comments from the user's current workspace. Returns relevant code snippets from the user's current workspace if it is large, or the full contents of the workspace if it is small."

**Evidence of Grep/Tool-Call Search:**

The `grep_search` tool:
> "Do a text search in the workspace. Limited to 20 results. Use this tool when you know the exact string you're searching for."

**Strategy guidance:**
> "Prefer using the semantic_search tool to search for context unless you know the exact string or filename pattern you're searching for."

**Additional Tools:** `list_code_usages`, `get_changed_files`, `test_search`, `file_search`

---

### 7. Google Antigravity

**Classification: Hybrid + Pre-fetch (Knowledge Items)**

Google Antigravity has the most sophisticated pre-fetching system observed, combining codebase search tools with a Knowledge Items (KI) system.

**Evidence of Semantic Search (Fast Prompt mode):**

From `Fast Prompt.txt`, the `codebase_search` tool:
> "Find snippets of code from the codebase most relevant to the search query"

Also: `search_in_file` - "Returns code snippets in the specified file that are most relevant to the search query"

**Evidence of Grep/Tool-Call Search:**

The `grep_search` tool:
> "Use ripgrep to find exact pattern matches within files or directories"

**Knowledge Items Pre-fetch System (unique to Antigravity):**

This is the most notable finding. From `Fast Prompt.txt`:
> "Check KI Summaries Before Any Research"
> "BEFORE performing ANY research, analysis, or creating documentation, you MUST: 1. Review the KI summaries 2. Identify relevant KIs by checking if any KI titles/summaries match your task 3. Read relevant KI artifacts using the artifact paths listed in the summaries BEFORE doing independent research"

The KI system provides:
> "Conversation Logs and Artifacts, containing the original information in the conversation history" and "Knowledge Items (KIs), containing distilled knowledge on specific topics"

This is a form of **pre-computed, distilled context injection** that goes beyond simple RAG.

---

### 8. Replit

**Classification: Hybrid (semantic)**

**Evidence of Semantic Search:**

From `Tools.json`, the `search_filesystem` tool:
> Locates files by class names, function names, code snippets, or **natural language semantic queries** within a codebase.

This single tool combines both semantic and pattern-based search in one interface.

No separate grep tool was identified in the tool definitions.

---

### 9. Kiro

**Classification: Likely Hybrid (limited evidence)**

Kiro's prompts are shorter and less detailed about search mechanisms.

**Evidence of Indexing:**

From `Vibe_Prompt.txt`:
> "Kiro can scan your whole codebase **once indexed** with #Codebase"

The phrase "once indexed" implies a codebase indexing step occurs, though the implementation (embedding vs. text index) is not specified.

**Context References:**
- `#File` and `#Folder` for manual context inclusion
- `#Problems` for diagnostics
- `#Terminal` for terminal output
- `#Git Diff` for changes
- MCP server support for extensibility

---

### 10. Amp

**Classification: Tool-call (agent wrapper)**

**Evidence of Search Architecture:**

From `claude-4-sonnet.yaml`, the `codebase_search_agent` tool:
> "Intelligently search your codebase with an agent that has access to: list_directory, Grep, glob, Read."

This is notably different from Cursor/Windsurf's `codebase_search`. Amp's search is **not** embedding-based -- it explicitly describes an agent that wraps traditional search tools (grep, glob, read).

**Other Tools:**
- `Grep` - "Search for exact text patterns in files using ripgrep"
- `glob` - "Fast file pattern matching tool"
- `Read` - File reading
- `Task` - Sub-agent for complex operations

---

### 11. Claude Code

**Classification: Tool-call only**

Claude Code has no semantic or embedding-based search. All context gathering is through runtime tool calls.

**Tools Available:**
- `Grep` - "A powerful search tool built on ripgrep"
- `Glob` - "Fast file pattern matching tool that works with any codebase size"
- `Read` - Direct file reading
- `Task` - Sub-agent with access to all tools
- `LS` - Directory listing
- `WebSearch` - Web search

**Strategy from prompt:**
> Encourages "extensive" use of search tools "in parallel and sequentially" for understanding codebases.

**No semantic/embedding search:** The tools list contains no `codebase_search`, `semantic_search`, or any embedding-related tool.

---

### 12. Codex CLI (OpenAI)

**Classification: Tool-call only**

Codex CLI is the most minimal in terms of search capabilities.

**Tools Available:**
- `apply_patch` - File editing
- Shell commands: `git log`, `git blame`, `git status`, `git diff`
- General shell execution

**Context Gathering:**
> "Read files and gather the relevant information"
> "Use git log and git blame to search the history of the codebase"

**No dedicated search tools:** No grep wrapper, no semantic search, no codebase indexing. Relies entirely on shell commands.

---

### 13. Cline

**Classification: Tool-call only**

**Tools Available:**
- `search_files` - "Request to perform a regex search across files in a specified directory" with glob filtering and "Rust regex syntax"
- `read_file` - File reading
- `list_files` - Directory listing
- `list_code_definition_names` - "List definition names (classes, functions, methods, etc.)"

**Initial Context:**
> Receives "recursive list of all filepaths in the current working directory" as environment_details

**No semantic/embedding search.** All search is regex-based.

---

### 14. v0 (Vercel)

**Classification: Tool-call only**

**Tools Available:**
- `GrepRepo` - "Searches for regex patterns within file contents across the repository"
- `LSRepo` - "Lists files and directories in the repository"
- `ReadFile` - "Reads file contents intelligently - returns complete files when small, paginated chunks, or targeted chunks when large based on your query"
- `SearchRepo` - "Launches a new agent that searches and explores the codebase using multiple search strategies (grep, file listing, content reading)"

**Strategy:**
> "Search systematically: broad -> specific -> verify relationships"
> "Don't Stop at the First Match"

**No semantic/embedding search.** `SearchRepo` is an agent wrapper (similar to Amp), not embedding-based.

---

### 15. Junie (JetBrains)

**Classification: Tool-call only**

**Tools Available:**
- `search_project` - "powerful in-project search" with "fuzzy search meaning that the output will contain both exact and inexact matches"
- `get_file_structure` - Code structure listing
- `open`, `goto`, `scroll_down/up` - File navigation
- Bash commands: `ls`, `cat`, `cd`

**Note:** "fuzzy search" is text-based fuzzy matching, not semantic/embedding search.

---

### 16. Manus

**Classification: Tool-call only**

**Tools Available:**
- `file_find_in_content` - "Search files using regex patterns"
- `file_find_by_name` - "Find files by name pattern in specified directory"
- `file_read` - File reading
- `info_search_web` - Web search
- Browser tools for web interaction

**No semantic/embedding search.** All file search is regex/pattern-based.

---

### 17. Bolt

**Classification: Minimal (no search)**

Bolt operates in a WebContainer environment with basic file operations (cat, ls, cp, etc.) and development tools (node, python3). **No dedicated search tools** of any kind were identified in the system prompt.

---

## Key Patterns and Insights

### Pattern 1: IDE Tools Favor Semantic Search, CLI/Agent Tools Favor Grep

Tools that operate within an IDE context (Cursor IDE, Windsurf, Copilot, Trae) tend to have semantic/embedding search as a primary tool. This makes sense because:
- IDE tools can maintain a persistent codebase index in the background
- The IDE process can pre-compute embeddings while the user works
- IDE tools have access to language server features

CLI-based tools (Claude Code, Codex CLI, Cursor CLI) tend to rely on grep/ripgrep because:
- CLI sessions are ephemeral; there is no persistent background process to maintain an index
- Building an embedding index at runtime would be too slow
- grep/ripgrep is fast enough for on-demand search

**The Cursor IDE vs CLI split is the strongest evidence for this pattern:** The same product explicitly switches its "MAIN exploration tool" from `codebase_search` (semantic) in IDE mode to `Grep` in CLI mode.

### Pattern 2: The "Proprietary Retrieval/Embedding Model Suite" Description

Trae and Augment Code use nearly identical language to describe their search tools:
- Trae: "Uses a proprietary retrieval/embedding model suite that produces the highest-quality recall"
- Augment Code: "Uses a proprietary retrieval/embedding model suite that produces the highest-quality recall"

This suggests either shared infrastructure, a common vendor for the retrieval system, or that one description was modeled on the other.

### Pattern 3: Pre-fetched Context Injection

Several tools inject context before the LLM processes the user's request:
- **Google Antigravity**: Knowledge Items (KI) system with distilled knowledge summaries
- **Cursor/Windsurf/Trae**: Automatic attachment of open files, cursor position, edit history
- **Cline**: Recursive file listing of the working directory

This is distinct from both RAG and tool-calling -- it is context that the orchestration layer injects into the prompt without the LLM requesting it.

### Pattern 4: Hybrid is Dominant Among Commercial Tools

Most commercial coding assistants (Cursor, Windsurf, Trae, Copilot, Devin, Augment) use a hybrid approach:
1. Semantic/embedding search for broad exploration ("what does this system do?")
2. Grep/regex for precise matches ("find all uses of `handleAuth`")
3. Direct file reads for known files
4. Pre-injected metadata (open files, cursor position)

### Pattern 5: Open-Source Tools are Tool-Call Only

Open-source tools (Cline, Bolt, Codex CLI) rely exclusively on regex search and file reads. None implement semantic/embedding search. This likely reflects:
- The complexity of building and maintaining an embedding index
- The need for a persistent background service to manage the index
- Cost of embedding computation

### Pattern 6: Agent-Wrapped Search as a Middle Ground

Some tools (Amp's `codebase_search_agent`, v0's `SearchRepo`) create a sub-agent that uses traditional search tools (grep, glob, read) to explore the codebase. This provides more intelligent search without requiring an embedding index -- the "intelligence" comes from the LLM orchestrating multiple search steps rather than from vector similarity.

---

## Architectural Spectrum

```
Pure Tool-Call                                          Pure RAG/Pre-fetch
(grep at runtime)                                       (embeddings + index)
     |                                                        |
     |   Bolt  Codex  Cline  Manus  v0  Claude   Amp   Copilot  Devin  Cursor  Windsurf  Trae  Augment  Antigravity
     |    |      |      |      |     |    Code     |      |       |      |        |        |      |         |
     |    *------*------*------*-----*-----*-------*------*-------*------*--------*--------*------*---------*
     |    |                                        |      |                                       |         |
     |  No search                            Agent-wrapped   Semantic search as tool          Embedding    KI pre-fetch
     |  tools                                search          called at runtime                 index +      + semantic
     |                                                                                        real-time    search
```

---

## Conclusion

The AI coding tool landscape shows a clear split in context-fetching approaches:

1. **IDE-integrated commercial tools** (Cursor, Windsurf, Trae, Augment, Copilot) have converged on a **hybrid model** combining embedding-based semantic search with traditional grep/regex search, supplemented by pre-injected IDE state metadata.

2. **CLI-based and open-source tools** (Claude Code, Codex CLI, Cline, Manus) rely on **runtime tool calls** using grep/ripgrep, glob patterns, and direct file reads, with no embedding index.

3. **Google Antigravity** represents a unique approach with its **Knowledge Items pre-fetch system**, adding a layer of distilled, pre-computed context on top of the hybrid search model.

4. The most revealing evidence comes from **Cursor's IDE vs CLI split**: the same product uses semantic search as its "MAIN" tool in IDE mode but switches to grep in CLI mode, confirming that embedding-based search depends on the persistent indexing infrastructure that IDE environments provide.

5. The **absence of any RAG/embedding language in Claude Code's system prompt** is notable given its position as a leading coding assistant. Claude Code compensates with aggressive parallel tool calling and sub-agent delegation (the Task tool), relying on the LLM's reasoning ability to orchestrate effective multi-step searches rather than on pre-computed semantic similarity.
