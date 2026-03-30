# Research Notes: AI Coding Tool Context-Fetching Approaches

## Date: 2026-02-25

## Source Repository
https://github.com/x1xhlol/system-prompts-and-models-of-ai-tools

## Methodology
- Explored the GitHub repository directory structure to identify folders for each AI coding tool
- Fetched raw system prompt and tool definition files for each tool
- Searched for specific keywords: RAG, embeddings, vector, semantic search, indexing, codebase search, grep, ripgrep, glob, file search, tool calls, context fetching, retrieval
- Quoted verbatim passages as evidence

## Key Files Examined

### Claude Code
- `Anthropic/Claude Code/Prompt.txt` - System prompt
- `Anthropic/Claude Code/Tools.json` - Tool definitions

### Cursor
- `Cursor Prompts/Agent Prompt 2.0.txt` - Latest agent prompt
- `Cursor Prompts/Agent Prompt 2025-09-03.txt` - September 2025 agent prompt
- `Cursor Prompts/Agent Prompt v1.2.txt` - Earlier agent prompt
- `Cursor Prompts/Agent CLI Prompt 2025-08-07.txt` - CLI agent prompt
- `Cursor Prompts/Agent Tools v1.0.json` - Tool definitions

### Windsurf
- `Windsurf/Prompt Wave 11.txt` - System prompt
- `Windsurf/Tools Wave 11.txt` - Tool definitions

### Devin AI
- `Devin AI/Prompt.txt` - System prompt

### Copilot (VSCode Agent)
- `VSCode Agent/Prompt.txt` - System prompt (identified as GitHub Copilot)

### Cline
- `Open Source prompts/Cline/Prompt.txt` - System prompt

### Codex CLI
- `Open Source prompts/Codex CLI/Prompt.txt` - System prompt

### Kiro
- `Kiro/Vibe_Prompt.txt` and `Kiro/Spec_Prompt.txt` - System prompts

### Trae
- `Trae/Builder Prompt.txt` - System prompt
- `Trae/Builder Tools.json` - Tool definitions

### Bolt
- `Open Source prompts/Bolt/Prompt.txt` - System prompt

### v0
- `v0 Prompts and Tools/Prompt.txt` - System prompt
- `v0 Prompts and Tools/Tools.json` - Tool definitions

### Replit
- `Replit/Prompt.txt` - System prompt
- `Replit/Tools.json` - Tool definitions

### Manus
- `Manus Agent Tools & Prompt/Prompt.txt` - System prompt
- `Manus Agent Tools & Prompt/tools.json` - Tool definitions

### Google Antigravity
- `Google/Antigravity/Fast Prompt.txt` - Fast mode prompt
- `Google/Antigravity/planning-mode.txt` - Planning mode prompt

### Amp (bonus)
- `Amp/claude-4-sonnet.yaml` - System prompt

### Augment Code (bonus)
- `Augment Code/claude-4-sonnet-agent-prompts.txt` - System prompt
- `Augment Code/claude-4-sonnet-tools.json` - Tool definitions

### Junie (bonus)
- `Junie/Prompt.txt` - System prompt

## Key Findings

### Tools with explicit semantic/embedding search
1. **Cursor** - `codebase_search` described as "semantic search tool" that "finds code by meaning, not exact text"
2. **Windsurf** - `codebase_search` finds "snippets of code from the codebase most relevant to the search query" + `trajectory_search` for "semantic search or retrieve trajectory"
3. **Trae** - `search_codebase` described as using "proprietary retrieval/embedding model suite that produces the highest-quality recall"
4. **Augment Code** - `codebase-retrieval` uses "proprietary retrieval/embedding model suite" with "real-time index of the codebase"
5. **Devin** - `semantic_search` for "higher level questions about the code"
6. **Copilot/VSCode** - `semantic_search` for "natural language search for relevant code"
7. **Replit** - `search_filesystem` supports "natural language semantic queries"
8. **Google Antigravity** - `codebase_search` tool + Knowledge Items (KI) pre-fetched context system
9. **Kiro** - `#Codebase` reference that works "once indexed"

### Tools with purely grep/tool-call approach
1. **Claude Code** - Grep (ripgrep), Glob, Read, Task tools only. No semantic search.
2. **Codex CLI** - Shell commands, git log/blame only. No semantic search.
3. **Cline** - search_files (regex), read_file, list_files only. No semantic search.
4. **Bolt** - Basic file operations only. No search tools at all.
5. **Manus** - file_find_in_content (regex), file_find_by_name. No semantic search.
6. **v0** - GrepRepo, LSRepo, ReadFile, SearchRepo (agent wrapper). No semantic search mentioned.
7. **Junie** - search_project (fuzzy text search), bash commands. No semantic/embedding search.

### Hybrid approaches
1. **Cursor** - Has BOTH codebase_search (semantic) AND grep_search (ripgrep). Prompt says "Semantic search is your MAIN exploration tool" but also has grep for exact matches. CLI version favors grep as "MAIN exploration tool."
2. **Windsurf** - Has BOTH codebase_search AND grep_search (ripgrep)
3. **Devin** - Has BOTH semantic_search AND find_filecontent (regex)
4. **Copilot** - Has BOTH semantic_search AND grep_search
5. **Google Antigravity** - Has codebase_search, grep_search, AND Knowledge Items pre-fetching
6. **Trae** - Has BOTH search_codebase (embedding) AND search_by_regex (ripgrep)
7. **Augment Code** - Has codebase-retrieval (embedding) AND regex search via view tool
8. **Amp** - codebase_search_agent wraps grep/glob/read tools. Not true semantic search.

### Interesting Pattern: Cursor CLI vs IDE
- Cursor IDE Agent: "Semantic search (codebase_search) is your MAIN exploration tool"
- Cursor CLI Agent: "Grep search (Grep) is your MAIN exploration tool"
This suggests CLI mode may not have access to the embedding index.
