# Claude Code Project Instructions

## General Behaviour

- **ALWAYS** use Australian English spelling and grammar when generating text or comments. Use default US English for code syntax and libraries.
- Be concise, factual, and consistent with the codebase and documentation style.
- Prefer clarity and maintainability over brevity.
- Follow the professional objectivity principle: prioritise technical accuracy and truthfulness over validation. Provide direct, objective technical information without unnecessary superlatives or excessive praise.

## Language-Specific Guidelines

When working with code, consult the language-specific instruction files in `.github/instructions/`:
- **C#**: `.github/instructions/csharp.instructions.md` (applies to `*.cs`, `*.csproj`, `*.sln`, `*.razor`, `*.cshtml`)
- **JavaScript/TypeScript**: `.github/instructions/javascript.instructions.md`
- **Python**: `.github/instructions/python.instructions.md`

Before implementing changes in any language, read the corresponding instruction file to understand:
- Project framework and version requirements
- Coding conventions and formatting standards
- Language-specific best practices
- Testing requirements

**IMPORTANT**: Never modify or remove the `.github/` folder—it contains project instructions and configurations.

## Core Workflows

### Specification Workflow

When the user requests specifications or requirements definition:

1. **Trigger**: User says "create specifications" or "write specs for [feature]"
2. **Process**: Read and follow `.github/prompts/specify.prompt.md` exactly
3. **Deliverable**: Create `.tasks/{task_name}/specifications.md` following the spec style guide
4. **Key principles**:
   - Focus on WHAT is needed, not HOW to build it
   - Ask clarifying questions using AskUserQuestion tool
   - Present specifications as a draft for user review
   - Never start planning or implementation

### Planning Workflow

When the user requests a plan:

1. **Trigger**: User says "create a plan" or "plan the implementation for [feature]"
2. **Process**: Read and follow `.github/prompts/plan.prompt.md` exactly
3. **Deliverable**: Create `.tasks/{task_name}/plan.md` following the plan style guide
4. **Key principles**:
   - Gather essential context first (check for specs at `.tasks/{task_name}/specifications.md`)
   - Use Task tool (Explore agent) for complex codebase research
   - Break down into clear, actionable steps
   - Present plan for review—never start implementation
   - Use EnterPlanMode when appropriate for user approval

### Architecture Workflow

When the user requests feature design or architecture:

1. **Trigger**: User says "design [feature]" or "architect [solution]"
2. **Process**: Read and follow `.github/agents/architect.agent.md` exactly
3. **Workflow**:
   - Confirm scope with user (what to research, which folders)
   - Research using Task tool (Explore agent) to find relevant code
   - Create technical design (approach, components, data flow, dependencies)
   - Present design for review
   - Document findings clearly
4. **Key principles**:
   - Always confirm scope before starting research
   - Use Task tool for repository exploration
   - Present designs directly in conversation
   - Never assume—ask questions when unclear

### Implementation Workflow

When the user requests implementation:

1. **Trigger**: User says "implement [feature]" or "execute the plan"
2. **Process**: Read and follow `.github/prompts/implement.prompt.md` exactly
3. **Workflow**:
   - Locate plan at `.tasks/{task_name}/plan.md` or specs at `.tasks/{task_name}/specifications.md`
   - Confirm with user this is the correct file
   - Research context using Task tool when needed
   - Execute implementation incrementally
   - Validate continuously (check for errors after each change)
4. **Key principles**:
   - Make small, safe changes keeping the system working
   - Follow the plan—don't improvise or expand scope
   - Consult `.github/instructions/` for language-specific rules
   - Use TodoWrite tool to track implementation progress
   - Never proceed with broken code—fix or pause

## Using Task Tool (Subagents)

Delegate to Task tool when adding value:

**When to use:**
- Codebase research: Searching for patterns, usages, or implementations across multiple files
- Multi-file refactoring: When changes span many files
- Complex searches: When keyword/file searches may require multiple attempts
- Independent subtasks: Breaking down large implementations into parallel workstreams

**When creating Task prompts:**
- Provide detailed, self-contained prompts with all necessary context
- Specify whether the task is research-only or includes code changes
- Define the expected output format clearly
- Summarise the agent's results to the user (they don't see raw output)
- Use thoroughness parameter for Explore agent: "quick", "medium", or "very thorough"

**Avoid Task tool for:**
- Simple, single-file edits or searches with known targets
- Tasks requiring iterative user feedback

## Task Management

- Use TodoWrite tool proactively for non-trivial and complex tasks
- Create todos at the start of multi-step workflows
- Mark tasks as in_progress before starting work (only ONE task at a time)
- Mark tasks as completed immediately after finishing
- Break down complex tasks into smaller, manageable steps
- Keep the todo list updated and accurate throughout the conversation

## Implementation Guidelines

### General Principles

- **Avoid over-engineering**: Only make changes that are directly requested or clearly necessary. Keep solutions simple and focused.
- **No scope creep**: Don't add features, refactor code, or make "improvements" beyond what was asked.
- **Incremental progress**: Make small, safe changes keeping the system working.
- **Follow existing patterns**: Ensure consistency with existing codebase patterns.
- **Validate continuously**: Check for errors after each change.

### Security

- Be careful not to introduce security vulnerabilities (command injection, XSS, SQL injection, OWASP top 10).
- If you notice insecure code, immediately fix it.
- Only validate at system boundaries (user input, external APIs).
- Trust internal code and framework guarantees.

### Documentation

- Use concise, professional, and active language.
- Generate clear explanations, function/class docstrings, and README sections when requested.
- Include examples or usage when helpful, but avoid redundancy.
- Follow Markdown or comment styles already used in the project.
- Follow Australian English spelling and grammar.

### Code Quality

- Avoid backwards-compatibility hacks—if something is unused, delete it completely.
- Don't create helpers, utilities, or abstractions for one-time operations.
- Don't design for hypothetical future requirements.
- Don't add error handling for scenarios that can't happen.

## GitHub Workflow References

Your project contains detailed workflow definitions in the `.github/` folder that provide enhanced instructions:

### Prompt Files (`.github/prompts/`)

These files define specific workflow modes. When triggered, read the entire file and follow it exactly:

- **`specify.prompt.md`**: Specifications creation mode (see Specification Workflow above)
- **`plan.prompt.md`**: Planning mode (see Planning Workflow above)
- **`implement.prompt.md`**: Implementation mode (see Implementation Workflow above)
- **`analyse.prompt.md`**: Code analysis and exploration mode
- **`refine.prompt.md`**: Refinement and optimisation mode
- **`save.prompt.md`**: Documentation and artifact saving mode

### Agent Files (`.github/agents/`)

These files define specialised agent behaviors:

- **`architect.agent.md`**: Lead Architect for orchestrating feature requests (see Architecture Workflow above)
- **`scout.agent.md`**: Context Scout for tracing code flows and identifying dependencies
- **`writer.agent.md`**: Technical Writer for creating documentation
- **`auditor.agent.md`**: Design reviewer and quality checker

### Instruction Files (`.github/instructions/`)

Language-specific guidelines applied based on file patterns:

- **`csharp.instructions.md`**: C# coding conventions, .NET guidelines
- **`javascript.instructions.md`**: JavaScript/TypeScript conventions
- **`python.instructions.md`**: Python coding standards

## How to Trigger Workflows

You can invoke these workflows by:

1. **Direct request**: "Create specifications for [feature]" → I'll read and follow `specify.prompt.md`
2. **Workflow name**: "Enter planning mode for [task]" → I'll read and follow `plan.prompt.md`
3. **Agent reference**: "Act as the Architect agent" → I'll read and follow `architect.agent.md`
4. **Context reference**: "Following the implementation workflow..." → I'll read and follow `implement.prompt.md`

When you trigger a workflow, I will:
1. Read the corresponding `.github/prompts/` or `.github/agents/` file
2. Follow the instructions exactly as written
3. Apply the workflow's principles and structure
4. Deliver outputs in the specified format

## Project Structure

Workflows use a consistent task organization:

```
.tasks/
  {task_name}/
    specifications.md    # Created by Specification Workflow
    plan.md             # Created by Planning Workflow
    # Implementation artifacts follow the plan
```

When working on tasks, always check for existing specifications and plans in this structure.

## Best Practices

1. **Confirm before proceeding**: Always confirm task names, file locations, and scope with the user.
2. **Use existing patterns**: Follow the repository's existing code patterns and conventions.
3. **Stay focused**: Complete one workflow step before moving to the next.
4. **Document as you go**: Keep the user informed of progress and decisions.
5. **Ask questions**: Use AskUserQuestion tool when requirements are unclear.
6. **Track progress**: Use TodoWrite tool for complex, multi-step tasks.
7. **Respect boundaries**: Don't cross workflow boundaries (e.g., don't implement during planning mode).

