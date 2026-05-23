# Project Instructions

## General Behaviour

- **ALWAYS** use Australian English spelling and grammar when generating text or comments. Use default US English for code syntax and libraries.
- Be concise, factual, and consistent with the codebase and documentation style.
- Prefer clarity and maintainability over brevity.
- Follow the professional objectivity principle: prioritise technical accuracy and truthfulness over validation. Provide direct, objective technical information without unnecessary superlatives or excessive praise.
- Prioritise solutions that are best and clean, not just "good enough for now". Avoid tactical workarounds or partial fixes unless explicitly requested by the user.

**PUNCTUATION**

- Use plain keyboard characters only. Straight quotes ' and ", not curly quotes ' ' " ". Hyphen-minus - and double-hyphen -- for ranges or breaks, not en dash – or em dash —. Three periods ... for ellipsis, not the single character ….
- This applies everywhere: prose, code comments, commit messages, and quoted strings.

## Using Subagents

Delegate tasks to subagents when they add value:

**When to use:**
- Codebase research: Searching for patterns, usages, or implementations across multiple files
- Multi-file refactoring: When changes span many files
- Complex searches: When keyword/file searches may require multiple attempts
- Independent subtasks: Breaking down large implementations into parallel workstreams

**When creating subagent prompts:**
- Provide detailed, self-contained prompts with all necessary context (subagents are stateless)
- Specify whether the task is research-only or includes code changes
- Define the expected output format clearly
- Summarise the subagent's results to the user (they don't see raw output)

**Avoid subagents for:**
- Simple, single-file edits or searches with known targets
- Tasks requiring iterative user feedback

## Implementation Guidelines

### General Principles

- **Avoid over-engineering**: Only make changes that are directly requested or clearly necessary. Keep solutions simple and focused.
- **No scope creep**: Don't add features, refactor code, or make "improvements" beyond what was asked.
- **Incremental progress**: Make small, safe changes keeping the system working.
- **Follow existing patterns**: Ensure consistency with existing codebase patterns.
- **Validate continuously**: Check for errors after each change.

### Proposing Solutions

Prioritize clean, highly scalable, and robust design decisions over quick, easy, or overly conservative solutions. Do not optimize for immediate development speed if it introduces technical debt or fragile patterns. Propose forward-looking, foundational solutions that establish strong, maintainable patterns for the future, even if they require slightly more initial setup.

### Stay Focused on the Ask

- **Answer the question on the table.** When the user asks a specific question, answer it. Do not pivot to recommending a different path, deferring the work, or framing a "tonight vs next session" plan unless the user asks for one.
- **No tactical workarounds offered as a shortcut.** Do not suggest "ship a known-broken version now and fix it properly later", "accept this as tech debt", "do option A tonight and option B next session", or any framing that splits the work into a partial fix plus a deferred follow-up. If the user wants a workaround, they will ask.

### Documentation

- Use concise, professional, and active language.
- Generate clear explanations, function/class docstrings, and README sections when requested.
- Include examples or usage when helpful, but avoid redundancy.
- Follow Markdown or comment styles already used in the project.
- Follow Australian English spelling and grammar.

## Plugin Structure

This project is distributed as a plugin for both Claude Code and GitHub Copilot CLI. Workflow definitions, agents, and skills live in directories at the repo root:

- **`commands/`** — Slash command definitions (`specify`, `refine`, `plan`, `analyse`, `implement`, `code-review`, `troubleshoot`, `save`)
- **`agents/`** — Agent definitions (`architect`, `scout`, `auditor`, `writer`)
- **`skills/`** — Productivity skills (`docx`, `pdf`, `xlsx`, `webapp-testing`)

When a workflow is triggered, read the corresponding file in `commands/` and follow it exactly.

## How to Trigger Workflows

You can invoke workflows by:

1. **Slash command**: `/specify`, `/plan`, `/implement`, etc. — I'll read and follow the matching file in `commands/`
2. **Direct request**: "Create specifications for [feature]" → I'll read and follow `commands/specify.md`
3. **Agent reference**: "Act as the Architect agent" → I'll read and follow `agents/architect.agent.md`

When you trigger a workflow:
1. Read the corresponding file in `commands/` or `agents/`
2. Follow the instructions exactly as written
3. Apply the workflow's principles and structure
4. Deliver outputs in the specified format

## Packages and Third-party Tools

### Using Context7 for Third-party Documentation

The `context7` MCP server provides access to up-to-date documentation and code examples for third-party libraries and frameworks.

**When to use:**
- Retrieving official documentation for libraries, frameworks, or tools
- Finding code examples and best practices
- Checking for specific API usage patterns or recent changes
- Verifying library-specific conventions or configurations

**How to use:**
1. Use the `resolve-library-id` tool first to identify the correct library ID (format: `/org/project` or `/org/project/version`)
2. Use `query-docs` tool to retrieve documentation with specific queries
3. Include the resolved library ID and a detailed, specific query
4. Prioritise libraries with high reputation scores and comprehensive code snippet coverage

**Note:** Do not call either tool more than 3 times per question. If information cannot be found after 3 attempts, use the best result obtained.
