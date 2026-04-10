# Project Instructions

## General Behaviour

- **ALWAYS** use Australian English spelling and grammar when generating text or comments. Use default US English for code syntax and libraries.
- Be concise, factual, and consistent with the codebase and documentation style.
- Prefer clarity and maintainability over brevity.
- Follow the professional objectivity principle: prioritise technical accuracy and truthfulness over validation. Provide direct, objective technical information without unnecessary superlatives or excessive praise.

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

### Documentation

- Use concise, professional, and active language.
- Generate clear explanations, function/class docstrings, and README sections when requested.
- Include examples or usage when helpful, but avoid redundancy.
- Follow Markdown or comment styles already used in the project.
- Follow Australian English spelling and grammar.

## Instructions References

The project contains detailed workflow definitions in the `.github/` folder that provide enhanced instructions:

## Workflow Prompts

The `.github/prompts/` folder contains workflow definitions (specify, plan, implement, analyse, refine, save). When a workflow is triggered, read the corresponding file and follow it exactly.

### Instruction Files (`.github/instructions/`)

Language-specific guidelines applied based on file patterns:

- **`csharp.instructions.md`**: C# coding conventions, .NET guidelines
- **`javascript.instructions.md`**: JavaScript/TypeScript conventions
- **`python.instructions.md`**: Python conventions, tooling, and testing

## How to Trigger Workflows

You can invoke these workflows by:

1. **Direct request**: "Create specifications for [feature]" → I'll read and follow `specify.prompt.md`
2. **Workflow name**: "Enter planning mode for [task]" → I'll read and follow `plan.prompt.md`
3. **Agent reference**: "Act as the Architect agent" → I'll read and follow `architect.agent.md`
4. **Context reference**: "Following the implementation workflow..." → I'll read and follow `implement.prompt.md`

When you trigger a workflow:
1. Read the corresponding `.github/prompts/` file
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