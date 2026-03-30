# Research projects carried out by AI tools

Each directory in this repo is a separate research project carried out by an LLM
tool - usually [Claude Code](https://www.claude.com/product/claude-code). Every
single line of text and code was written by an LLM.

See [Code research projects with async coding agents like Claude Code and
Codex](https://simonwillison.net/2025/Nov/6/async-code-research/) for more
details on how this works.

I try to include prompts and links to transcripts in [the
PRs](https://github.com/ecarrara/research/pulls?q=is%3Apr+is%3Aclosed)
that added each report, or in [the
commits](https://github.com/ecarrara/research/commits/main/).

<!--[[[cog
import os
import re
import subprocess
import pathlib
from datetime import datetime, timezone

# Model to use for generating summaries
MODEL = "github/gpt-4.1"

# Get all subdirectories with their first commit dates
research_dir = pathlib.Path.cwd()
subdirs_with_dates = []

for d in research_dir.iterdir():
    if d.is_dir() and not d.name.startswith('.'):
        # Get the date of the first commit that touched this directory
        try:
            result = subprocess.run(
                ['git', 'log', '--diff-filter=A', '--follow', '--format=%aI', '--reverse', '--', d.name],
                capture_output=True,
                text=True,
                timeout=5
            )
            if result.returncode == 0 and result.stdout.strip():
                # Parse first line (oldest commit)
                date_str = result.stdout.strip().split('\n')[0]
                commit_date = datetime.fromisoformat(date_str.replace('Z', '+00:00'))
                subdirs_with_dates.append((d.name, commit_date))
            else:
                # No git history, use directory modification time
                subdirs_with_dates.append((d.name, datetime.fromtimestamp(d.stat().st_mtime, tz=timezone.utc)))
        except Exception:
            # Fallback to directory modification time
            subdirs_with_dates.append((d.name, datetime.fromtimestamp(d.stat().st_mtime, tz=timezone.utc)))

# Print the heading with count
print(f"## {len(subdirs_with_dates)} research projects\n")

# Sort by date, most recent first
subdirs_with_dates.sort(key=lambda x: x[1], reverse=True)

for dirname, commit_date in subdirs_with_dates:
    folder_path = research_dir / dirname
    readme_path = folder_path / "README.md"
    summary_path = folder_path / "_summary.md"

    date_formatted = commit_date.strftime('%Y-%m-%d')

    # Get GitHub repo URL
    github_url = None
    try:
        result = subprocess.run(
            ['git', 'remote', 'get-url', 'origin'],
            capture_output=True,
            text=True,
            timeout=2
        )
        if result.returncode == 0 and result.stdout.strip():
            origin = result.stdout.strip()
            # Convert SSH URL to HTTPS URL for GitHub
            if origin.startswith('git@github.com:'):
                origin = origin.replace('git@github.com:', 'https://github.com/')
            if origin.endswith('.git'):
                origin = origin[:-4]
            github_url = f"{origin}/tree/main/{dirname}"
    except Exception:
        pass

    if github_url:
        print(f"### [{dirname}]({github_url}) ({date_formatted})\n")
    else:
        print(f"### {dirname} ({date_formatted})\n")

    # Check if summary already exists
    if summary_path.exists():
        # Use cached summary
        with open(summary_path, 'r') as f:
            description = f.read().strip()
            if description:
                print(description)
            else:
                print("*No description available.*")
    elif readme_path.exists():
        # Generate new summary using llm command
        prompt = """Summarize this research project concisely. Write just 1 paragraph (3-5 sentences) followed by an optional short bullet list if there are key findings. Vary your opening - don't start with "This report" or "This research". Include 1-2 links to key tools/projects. Be specific but brief. No emoji."""
        result = subprocess.run(
            ['llm', '-m', MODEL, '-s', prompt],
            stdin=open(readme_path),
            capture_output=True,
            text=True,
            timeout=60
        )
        if result.returncode != 0:
            error_msg = f"LLM command failed for {dirname} with return code {result.returncode}"
            if result.stderr:
                error_msg += f"\nStderr: {result.stderr}"
            raise RuntimeError(error_msg)
        if result.stdout.strip():
            description = result.stdout.strip()
            print(description)
            # Save to cache file
            with open(summary_path, 'w') as f:
                f.write(description + '\n')
        else:
            raise RuntimeError(f"LLM command returned no output for {dirname}")
    else:
        print("*No description available.*")

    print()  # Add blank line between entries

# Add AI-generated note to all project README.md files
# Note: we construct these marker strings via concatenation to avoid the HTML comment close sequence
AI_NOTE_START = "<!-- AI-GENERATED-NOTE --" + ">"
AI_NOTE_END = "<!-- /AI-GENERATED-NOTE --" + ">"
AI_NOTE_CONTENT = """> [!NOTE]
> This is an AI-generated research report. All text and code in
this report was created by an LLM (Large Language Model). For more
information on how these reports are created, see the [main research
repository](https://github.com/ecarrara/research)."""

for dirname, _ in subdirs_with_dates:
    folder_path = research_dir / dirname
    readme_path = folder_path / "README.md"

    if not readme_path.exists():
        continue

    content = readme_path.read_text()

    # Check if note already exists
    if AI_NOTE_START in content:
        # Replace existing note
        pattern = re.escape(AI_NOTE_START) + r'.*?' + re.escape(AI_NOTE_END)
        new_note = f"{AI_NOTE_START}\n{AI_NOTE_CONTENT}\n{AI_NOTE_END}"
        new_content = re.sub(pattern, new_note, content, flags=re.DOTALL)
        if new_content != content:
            readme_path.write_text(new_content)
    else:
        # Add note after first heading (# ...)
        lines = content.split('\n')
        new_lines = []
        note_added = False
        for i, line in enumerate(lines):
            new_lines.append(line)
            if not note_added and line.startswith('# '):
                # Add blank line, then note, then blank line
                new_lines.append('')
                new_lines.append(AI_NOTE_START)
                new_lines.append(AI_NOTE_CONTENT)
                new_lines.append(AI_NOTE_END)
                note_added = True

        if note_added:
            readme_path.write_text('\n'.join(new_lines))

]]]-->
## 3 research projects

### [context-fetching-analysis](https://github.com/ecarrara/research/tree/main/context-fetching-analysis) (2026-02-25)

Analyzing system prompts from over 16 AI coding tools (see [x1xhlol/system-prompts-and-models-of-ai-tools](https://github.com/x1xhlol/system-prompts-and-models-of-ai-tools)), the research reveals two main approaches for codebase context retrieval: tools either pre-fetch context using Retrieval-Augmented Generation (RAG) and embeddings, or rely on runtime tool calls (like grep, glob, and file reads) for discovery. IDE-based commercial tools (such as Cursor, Windsurf, Trae, Augment, Copilot) predominantly use a hybrid method, integrating semantic search via codebase embedding indexes alongside traditional grep, with supplementary context injection (e.g., open files, cursor position). In contrast, CLI and open-source tools (like Claude Code, Codex CLI, Cline) rely on runtime tool calls, lacking semantic search or codebase indexing. Google Antigravity uniquely blends hybrid retrieval with pre-fetched, distilled Knowledge Items to inject context. Notably, the distinction is clearest in Cursor’s split: IDE mode uses semantic search as primary, while CLI mode defaults to grep, underscoring infrastructural limits in CLI environments.

**Key Findings:**
- IDE tools favor embedding-based semantic search due to persistent indexing infrastructure; CLI tools default to grep for efficiency.
- Commercial tools converge on hybrid retrieval, combining semantic, regex, and metadata injection.
- Open-source tools (e.g. [Cline](https://github.com/x1xhlol/Cline)) implement only runtime tool calls and regex/text search.
- Context injection (e.g., open files, Knowledge Items) is a distinct architectural feature beyond RAG and tool-calling.
- Agent-wrapped search (Amp, v0) offers intelligent orchestration without embeddings, relying on runtime tool combination.

### [ai-system-prompts-best-practices](https://github.com/ecarrara/research/tree/main/ai-system-prompts-best-practices) (2026-02-24)

A cross-tool review of production AI coding assistants, leveraging the [system-prompts-and-models-of-ai-tools](https://github.com/x1xhlol/system-prompts-and-models-of-ai-tools) repository, identifies 15 best practices in prompt design by comparing over a dozen live system prompts ranging from IDE agents to autonomous OS-level tools. The study finds that robust structure (with clear section delimiters), example-driven guidance, and multi-layered safety rules are universal, while prompt length depends more on platform specificity than quality. Dynamic prompt assembly, planning before execution, precise context management, and explicit anti-verbosity techniques are standard. Notably, context retrieval divides into RAG-driven semantic search for IDE tools and runtime tool-calling for CLI agents. Findings are reinforced by observed prompt variants for specific models, highlighting the necessity of adapting prompt engineering to model idiosyncrasies and deployment environment.

**Key Findings:**
- Prompt length tracks platform specificity, not output quality.
- Safety strategies are multi-layered: prompt guardrails, approval gates, and infrastructure.
- Most critical challenge: minimizing redundant context fetching.
- "Think-before-act" planning protocols are becoming standard.
- Runtime prompt templating is now common practice.
- Tools combat verbosity with examples, anti-pattern bans, and explicit constraints.
- Context retrieval split: IDE agents use RAG-augmented search; CLI agents rely on iterative tool-calling.

### [agent-experience-research](https://github.com/ecarrara/research/tree/main/agent-experience-research) (2026-02-02)

Agent Experience (AX) redefines how software is designed by focusing on the needs of AI agents—rather than just users or developers—within collaborative repositories. By implementing standards like [AGENTS.md](https://github.com/search?q=filename%3AAGENTS.md&type=code), teams improve agent comprehension, reduce cognitive load, and enable reliable automation. Research underscores that concise, code-first documentation, thoughtful task decomposition, and clear repository structures are vital for maximizing agent productivity, all while keeping a human-in-the-loop for critical oversight. As more open-source projects and tools (e.g., [Devin](https://cognition-labs.com/devin)) adopt AX best practices, teams see immediate gains in agent-driven workflows with clear, modular, and actionable information.

**Key Findings:**  
- Context size must be managed carefully; overfilling degrades agent performance.  
- The AGENTS.md file has become the norm for agent-facing documentation.  
- Tasks should be granular and verifiable to ensure reliable outcomes.  
- Progressive, incremental agent operations foster safer, higher-quality automation.  
- Human oversight remains a crucial and enduring component.

<!--[[[end]]]-->

---

## Updating this README

This README uses [cogapp](https://nedbatchelder.com/code/cog/) to automatically generate project descriptions.

### Automatic updates

A GitHub Action automatically runs `cog -r -P README.md` on every push to main and commits any changes to the README or new `_summary.md` files.

### Manual updates

To update locally:

```bash
# Run cogapp to regenerate the project list
cog -r -P README.md
```

The script automatically:
- Discovers all subdirectories in this folder
- Gets the first commit date for each folder and sorts by most recent first
- For each folder, checks if a `_summary.md` file exists
- If the summary exists, it uses the cached version
- If not, it generates a new summary using `llm -m <!--[[[cog
print(MODEL, end='')
]]]-->
github/gpt-4.1
<!--[[[end]]]-->` with a prompt that creates engaging descriptions with bullets and links
- Creates markdown links to each project folder on GitHub
- New summaries are saved to `_summary.md` to avoid regenerating them on every run

To regenerate a specific project's description, delete its `_summary.md` file and run `cog -r -P README.md` again.
