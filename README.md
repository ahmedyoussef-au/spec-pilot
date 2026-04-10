# SpecPilot

This guide explains how to use GitHub Copilot with custom instructions to implement **spec-driven development** for AI coding agents.

## Table of Contents
- [What is Spec-Driven Development?](#what-is-spec-driven-development)
- [Why Use Spec-Driven Development with AI?](#why-use-spec-driven-development-with-ai)
- [Configuration Structure](#configuration-structure)
- [Setting Up Your Project](#setting-up-your-project)
- [The Workflow](#the-workflow)
- [Slash Commands (Prompts)](#slash-commands-prompts)
- [Agents](#agents)
- [Language-Specific Instructions](#language-specific-instructions)
- [MCP Servers](#mcp-servers)
- [Multi-Agent Platform Support](#multi-agent-platform-support)
- [Workflow Examples](#workflow-examples)
- [Best Practices](#best-practices)

---

## What is Spec-Driven Development?

**Spec-driven development** is a methodology where you define clear specifications and plans before writing code. Instead of jumping straight into implementation, you follow a structured process:

1. **Specify** - Define requirements, objectives, and acceptance criteria
2. **Plan** - Break down the work into actionable steps
3. **Analyze** - Understand existing code and context (when needed)
4. **Implement** - Execute the plan systematically

This approach is particularly powerful when working with AI coding agents because it:
- Provides clear context and boundaries for the AI
- Ensures alignment with project goals before coding begins
- Creates documentation as a natural byproduct
- Enables better review and iteration cycles

## Why Use Spec-Driven Development with AI?

AI coding agents like GitHub Copilot work best when given:
- **Clear objectives** - What needs to be accomplished
- **Structured context** - Understanding of the existing codebase
- **Explicit constraints** - Technical limitations and requirements
- **Step-by-step guidance** - Breaking complex tasks into manageable pieces

Traditional "just code it" approaches with AI often lead to:
- ❌ Code that doesn't match project patterns
- ❌ Missing edge cases and error handling
- ❌ Inconsistent style across the codebase
- ❌ Solutions that don't align with business requirements

Spec-driven development addresses these issues by:
- ✅ Separating "what" from "how"
- ✅ Creating reviewable artefacts at each stage
- ✅ Maintaining consistency through defined guidelines
- ✅ Enabling iterative refinement without code waste

---

## Configuration Structure

The configuration uses a centralised structure in the `.github/` directory:

```
.github/
├── copilot-instructions.md       # Main Copilot behaviour configuration
├── agents/                        # Agent definitions (Copilot Chat agents)
│   ├── architect.agent.md        # Lead Architect (user-invokable)
│   ├── auditor.agent.md          # Logic Auditor (subagent)
│   ├── scout.agent.md            # Context Scout (subagent)
│   └── writer.agent.md           # Technical Writer (subagent)
├── instructions/                  # Language and project guidelines
│   ├── csharp.instructions.md    # C# coding standards
│   ├── javascript.instructions.md # JavaScript/TypeScript standards
│   └── ...                        # Add your own custom instructions
└── prompts/                       # Slash command definitions
    ├── specify.prompt.md          # /specify - Create specifications
    ├── refine.prompt.md           # /refine - Refine specs or plans via targeted questions
    ├── plan.prompt.md             # /plan - Create implementation plans
    ├── analyse.prompt.md          # /analyse - Analyse code/systems
    ├── implement.prompt.md        # /implement - Execute plans
    └── save.prompt.md             # /save - Save conversation context
```

### Configuration Files

#### `copilot-instructions.md`
The main configuration file that defines:
- General Copilot behaviour (concise, factual, consistent)
- Documentation standards (professional, clear, with examples)
- How slash commands work
- References to prompt and instruction files

#### `.github/instructions/` folder

Contains language-specific and project-specific coding guidelines:
- Language coding conventions (C#, JavaScript/TypeScript, etc.)
- Framework and version recommendations
- Testing best practices
- Common patterns and anti-patterns
- Team-specific coding standards
- Project-specific guidelines

All custom instructions for your project should be added as new files in this folder, using YAML frontmatter with `applyTo` glob patterns to specify when they should be applied.

#### `.github/agents/` folder

Contains agent definitions for Copilot Chat's agent mode:
- Each agent has a dedicated role, tools, and behavioural instructions
- Agents can delegate to other agents (subagents)
- User-invokable agents can be triggered directly via `@AgentName`
- See [Agents](#agents) for details

#### `.github/prompts/` folder

Contains slash command definitions that enable different modes:
- Each prompt defines tools, behaviour, and output structure
- Prompts can specify AI model preferences
- Workflows are clearly defined with steps

### Working Directories

The workflow creates structured directories for organising work:

```
.tasks/
└── {task_name}/                   # Individual task workspace
    ├── specifications.md          # Requirements and acceptance criteria
    ├── plan.md                    # Implementation plan with steps
    └── documentation.md           # Task-related documentation (optional)

.context/
└── {context_name}.md              # Analysis reports and context documents
```

**`.tasks/`** - Contains active and completed tasks
- Each task gets its own folder
- Keeps specifications and plans together
- Easy to reference and update

**`.context/`** - Stores analysis and reference documentation
- Code analysis reports
- Architecture overviews
- System understanding documents

---

## Setting Up Your Project

To implement this spec-driven workflow in your project:

### 1. Add Project-Specific Custom Instructions

Create your own instruction files in `.github/instructions/`:

**Example: Team Conventions**

`.github/instructions/team-conventions.instructions.md`:
```markdown
---
description: Team-wide coding standards
applyTo: '**/*'
---

## Code Review Requirements
- All functions must have unit tests
- No TODO comments in production code
- Security review required for authentication changes

## Commit Message Format
- feat: New feature
- fix: Bug fix
- docs: Documentation
- refactor: Code restructuring
```

**Example: API Guidelines**

`.github/instructions/api-guidelines.instructions.md`:
```markdown
---
description: REST API design standards
applyTo: '**/api/**/*.*, **/controllers/**/*.*'
---

## API Standards
- Use RESTful conventions
- Version all endpoints (e.g., /api/v1/)
- Return consistent error format
- Use HTTP status codes correctly

## Response Format
- Success: { data: {...}, meta: {...} }
- Error: { error: { code, message, details } }
```

**Example: Database Patterns**

`.github/instructions/database-patterns.instructions.md`:
```markdown
---
description: Database access patterns
applyTo: '**/repositories/**/*.*, **/data/**/*.*'
---

## Database Guidelines
- Use repository pattern for data access
- Always use parameterised queries
- Handle connection disposal properly
- Implement retry logic for transient failures

## Transaction Guidelines
- Keep transactions short
- Handle deadlocks gracefully
- Use appropriate isolation levels
```

### 2. Create Working Directories

```bash
mkdir -p .tasks .context
echo "# Tasks\nActive development tasks" > .tasks/README.md
echo "# Context\nAnalysis and reference docs" > .context/README.md
```

---

## The Workflow

The spec-driven development workflow follows four distinct phases:

```
1. SPECIFY    → Define what needs to be built (requirements, objectives, acceptance criteria)
2. PLAN       → Break down how to build it (tasks, steps, dependencies)
3. ANALYSE    → Understand existing codebase (optional, for context gathering)
4. IMPLEMENT  → Build according to the plan (following all guidelines)
```

Each phase is triggered by a slash command and produces structured output.

### Phase 1: Specification (`/specify`)
**Purpose**: Create clear, actionable requirements

**Process**:
1. Copilot asks clarifying questions (3-5 multiple choice if needed)
2. Gathers project context from existing documentation
3. Creates structured specifications
4. Saves to `.tasks/{task_name}/specifications.md`

**Output Structure**:
- **Objectives**: What needs to be achieved
- **Requirements**: Functional and non-functional requirements
- **Constraints**: Technical limitations or boundaries
- **Acceptance Criteria**: How to verify completion

**Key Rules**:
- Short and concise
- No implementation details (unless requested)
- No code snippets in specs
- Focus on "what", not "how"

---

### Phase 2: Planning (`/plan`)
**Purpose**: Create a step-by-step implementation roadmap

**Process**:
1. Reads specifications from `.tasks/{task_name}/specifications.md`
2. Confirms correct spec file with user
3. Breaks down objectives into manageable tasks
4. Creates timeline and identifies resources
5. Saves to `.tasks/{task_name}/plan.md`

**Output Structure**:
- **Objectives**: Summary from specs
- **Tasks**: Broken down milestones
- **Timelines**: Estimated completion times
- **Resources**: Tools, files, and dependencies needed

**Key Rules**:
- Never implements code during planning
- Tasks should be small and manageable
- Clear dependencies between tasks
- Actionable steps only

---

### Phase 3: Analysis (`/analyse`) [Optional]
**Purpose**: Deep dive into existing code, data, or systems

**Process**:
1. Gathers context about code/system to analyse
2. Reads existing documentation if available
3. Performs structured analysis
4. Creates detailed report
5. Saves to `.context/{context_name}.md`

**Output Structure**:
- **Business Understanding**: Purpose and goals
- **Technology**: Languages, frameworks, libraries used
- **Technical Analysis**: Strengths, weaknesses, patterns

**Use Cases**:
- Understanding legacy code before refactoring
- Evaluating architectural decisions
- Identifying improvement opportunities
- Creating context for new team members

---

### Phase 4: Implementation (`/implement`)
**Purpose**: Execute the plan and build the solution

**Process**:
1. Reads plan from `.tasks/{task_name}/plan.md` or specs
2. Confirms correct file with user
3. Follows tasks and milestones sequentially
4. Adheres to project coding standards
5. Uses appropriate tools and methods

**Key Rules**:
- Follow the plan strictly
- Maintain consistency with existing codebase
- Use project-specific coding patterns
- Apply language-specific instructions automatically

## Slash Commands (Prompts)

Each slash command corresponds to a prompt file in `.github/prompts/`. These commands trigger specific AI modes optimised for different tasks. The `/refine` command helps improve ambiguous specifications or plans before planning or implementation proceeds.

### `/specify` – Create Specifications

**When to use**: Starting any new feature, bug fix, enhancement, or refactoring

**Example usage**:
```
/specify Add authentication to the API
/specify Fix memory leak in data processor
/specify Refactor database connection pooling
```

**What happens**:
1. Copilot asks clarifying questions (when requirements are unclear)
2. Gathers context from your project
3. Creates `.tasks/{task_name}/specifications.md`

**Customisation**: Edit `.github/prompts/specify.prompt.md` to:
- Change output format
- Add/remove specification sections
- Modify clarification questions
- Adjust tools available

---

### `/refine` – Refine Specifications or Plans

**When to use**: After a draft specification or plan exists and there are uncertainties, gaps, or implicit assumptions that need surfacing before moving forward.

**Example usage**:
```
/refine oauth-authentication
/refine fix-memory-leak
```

**What happens**:
1. Confirms the task name
2. Loads the relevant `specifications.md` or `plan.md`
3. Identifies ambiguities, missing details, and potential challenges
4. Asks 3–5 targeted multiple‑choice clarification questions (only if needed)
5. Updates or recommends updates to the specification or plan (without implementing code)

**Customisation**: Edit `.github/prompts/refine.prompt.md` to adjust refinement strategy, number/style of questions, or tool access.

**Why it helps**: Reduces churn by ensuring planning and implementation are based on well‑understood, explicit requirements; mitigates hidden assumptions early.

### `/plan` – Create Implementation Plan

**When to use**: After specifications are defined and approved

**Example usage**:
```
/plan api-authentication
/plan fix-memory-leak
```

**What happens**:
1. Confirms the task name
2. Loads specifications from `.tasks/{task_name}/specifications.md`
3. Breaks work into actionable steps
4. Creates `.tasks/{task_name}/plan.md`

**Customisation**: Edit `.github/prompts/plan.prompt.md` to:
- Modify plan structure
- Add estimation requirements
- Include risk assessment
- Change timeline format

---

### `/analyse` – Analyse Code or Systems

**When to use**: Understanding existing code, evaluating architecture, planning refactors

**Example usage**:
```
/analyse Authentication middleware implementation
/analyse Database query performance patterns
```

**What happens**:
1. Defines context name (e.g., "authentication-analysis")
2. Gathers information from your workspace
3. Reads existing documentation
4. Creates `.context/{context_name}.md`

**Customisation**: Edit `.github/prompts/analyse.prompt.md` to:
- Change report sections
- Add custom analysis criteria
- Modify output format
- Include specific metrics

---

### `/implement` – Execute the Plan

**When to use**: After plan is reviewed and approved, ready to code

**Example usage**:
```
/implement api-authentication
```

**What happens**:
1. Loads plan from `.tasks/{task_name}/plan.md`
2. Confirms it's the correct plan
3. Executes tasks sequentially
4. Applies all coding standards automatically
5. Follows project patterns

**Customisation**: Edit `.github/prompts/implement.prompt.md` to:
- Add pre-implementation checks
- Include testing requirements
- Modify completion criteria
- Add documentation steps

---

### `/save` – Save Conversation Context

**When to use**: After a productive conversation where you want to preserve key findings, decisions, or insights for future reference

**Example usage**:
```
/save authentication-decisions
/save debugging-session-findings
```

**What happens**:
1. Reviews the conversation history
2. Confirms context name with the user
3. Summarises key information, insights, and findings
4. Saves a structured Markdown summary to `.context/{context_name}.md`

**Customisation**: Edit `.github/prompts/save.prompt.md` to:
- Change summary format
- Add custom sections
- Modify extraction criteria

---

## Agents

The framework includes a multi-agent system in `.github/agents/` that enables specialised AI roles to collaborate on complex tasks. Agents differ from slash commands: while slash commands trigger specific workflows, agents are autonomous roles that can delegate to one another.

### How Agents Work

- **User-invokable agents** can be triggered directly in Copilot Chat (e.g. `@Architect`)
- **Subagents** are called by other agents and cannot be invoked directly
- Each agent definition specifies its role, tools, model, and behavioural instructions

### Available Agents

#### `@Architect` – Lead Architect (user-invokable)

Orchestrates the full lifecycle of a feature request — from research through to documented design.

**Workflow**:
1. **Confirm scope** — asks whether to scout the repo and which folders to search
2. **Research** — delegates to `Scout` to find relevant code and dependencies
3. **Design** — creates a technical solution based on findings
4. **Audit** — delegates to `Auditor` to review the design
5. **Document** — delegates to `Writer` to produce documentation
6. **Finalise** — presents the complete package for approval

#### `Scout` – Context Scout (subagent)

Traces code flows, identifies dependencies, and maps relevant files for a given feature or change. Respects scoping constraints (`includePaths` / `excludeGlobs`) and provides evidence-backed findings with code snippets.

#### `Auditor` – Logic Auditor (subagent)

Reviews designs, plans, and code proposals for:
- Correctness and assumption verification
- Edge cases (empty inputs, large datasets, concurrency)
- Error handling and security concerns
- Adherence to best practices and existing patterns

Provides a structured verdict with actionable feedback.

#### `Writer` – Technical Writer (subagent)

Translates technical designs into clear documentation: README updates, API specs, tutorials, and inline documentation. Follows the project's existing documentation conventions and Australian English spelling.

### Customising Agents

Edit the agent files in `.github/agents/` to:
- Change agent roles or instructions
- Add new specialist agents
- Modify delegation chains
- Adjust model preferences

> **Note**: Agents require VS Code settings `chat.customAgentInSubagent.enabled` and `chat.useAgentSkills` to be enabled. These are pre-configured in `.vscode/settings.json`.

---

## Language-Specific Instructions

The framework includes language-specific and project-specific guidelines in `.github/instructions/`.

### How It Works

Instructions in `.github/instructions/`:
- Cover language-specific coding standards and best practices
- Include project-specific customisations and team conventions
- Applied automatically based on file patterns defined in `applyTo`
- Can be extended by adding new instruction files

All instruction files use YAML frontmatter with `applyTo` glob patterns:

```yaml
---
description: Instruction description
applyTo: '**/*.ext, **/*.other'
---
```

When you edit a file, Copilot applies all matching instructions based on the file patterns defined in each instruction file.

### Available Language Instructions

The framework includes instructions for common languages:

**`.github/instructions/csharp.instructions.md`** - C# standards (applies to `**/*.cs, **/*.csproj, **/*.sln, **/*.razor, **/*.cshtml`)
**`.github/instructions/javascript.instructions.md`** - JavaScript/TypeScript standards (applies to `**/*.ts, **/*.js, **/*.tsx, **/*.jsx, **/*.cshtml`)
**`.github/instructions/python.instructions.md`** - Python standards (applies to `**/*.py, **/*.pyi, **/pyproject.toml`)

---

## MCP Servers

The framework includes pre-configured [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) servers that extend AI capabilities. These are defined in `.vscode/mcp.json` (for VS Code / Copilot) and `.claude/mcp.json` (for Claude Code).

### Included Servers

| Server | Purpose |
|---|---|
| **sequential-thinking** | Provides dynamic, iterative problem-solving for complex architectural and design decisions |
| **context7** | Retrieves up-to-date library documentation and code examples |
| **package-version** | Checks package versions across npm, PyPI, Docker Hub, and GitHub Actions |
| **browsermcp** | Browser automation for testing and interacting with web applications (VS Code only) |

### Setup

Some MCP servers require API keys. Copy `.env.example` to `.env` and fill in the required values:

```bash
cp .env.example .env
```

MCP servers require **Node.js** and `npx` to be installed, as they are launched via `npx` commands.

### Customisation

Add or remove MCP servers by editing:
- `.vscode/mcp.json` — for VS Code / GitHub Copilot
- `.claude/mcp.json` — for Claude Code

---

## Multi-Agent Platform Support

While the core workflow is designed for **GitHub Copilot** in VS Code, the framework includes compatibility files for other AI coding platforms:

| File | Platform | Purpose |
|---|---|---|
| `.github/copilot-instructions.md` | GitHub Copilot | Main behaviour configuration |
| `AGENTS.md` | Claude Code / OpenAI Codex | Project instructions and guidelines |
| `CLAUDE.md` | Claude Code | References `AGENTS.md` for shared configuration |
| `.claude/mcp.json` | Claude Code | MCP server configuration |

These files share the same core guidelines (Australian English, spec-driven workflow, implementation principles) to ensure consistent AI behaviour regardless of platform. If you only use Copilot, the root-level `AGENTS.md` and `CLAUDE.md` files can be safely removed.

---

## Workflow Examples

### Example 1: Adding a New Feature

```
# Step 1: Create specifications
User: /specify Add user authentication with OAuth2

Copilot: I'll help you create specifications. Let me ask a few questions:
1. Which OAuth providers? [Google / GitHub / Microsoft / Custom]
2. What needs protection? [Entire app / Specific routes / API only]
3. Session management? [JWT / Server-side / Hybrid]

User: [Answers: Google and GitHub, Specific routes, JWT]

Copilot: Creates .tasks/oauth-authentication/specifications.md

# Step 2: Create implementation plan
User: /plan oauth-authentication

Copilot: Confirms task and creates .tasks/oauth-authentication/plan.md with:
- OAuth library selection
- Route protection strategy
- Token management approach
- Testing plan

# Step 3: Review and approve plan
User: [Reviews plan, suggests adding rate limiting]

Copilot: Updates plan with rate limiting requirements

# Step 4: Implement the feature
User: /implement oauth-authentication

Copilot: Executes plan step by step:
- Installs OAuth libraries
- Creates authentication middleware
- Implements token validation
- Adds protected routes
- Writes tests
- Adds rate limiting
```

### Example 2: Understanding Existing Code

```
# Analyse an unfamiliar module
User: /analyse Payment processing module

Copilot: Creates .context/payment-processing.md with:
- Business understanding (subscription billing, refunds)
- Technology stack (Stripe API, webhooks, queues)
- Technical analysis (strengths: good error handling; 
  weaknesses: no retry logic)
- Recommendations (add idempotency, improve logging)
```

### Example 3: Bug Fix with Specs

```
# Step 1: Specify the fix
User: /specify Fix race condition in concurrent user updates

Copilot: Creates .tasks/fix-race-condition/specifications.md
- Issue: Multiple simultaneous updates cause data loss
- Expected: Last write wins or optimistic locking
- Constraints: No database migration, backward compatible

# Step 2: Plan the fix
User: /plan fix-race-condition

Copilot: Creates implementation plan:
- Add version field to user model
- Implement optimistic locking
- Handle version conflicts
- Add tests for concurrent updates

# Step 3: Implement
User: /implement fix-race-condition

Copilot: Applies the fix following all guidelines
```

---

## Best Practices

### For Specifications
- ✅ Be specific about requirements and constraints
- ✅ Define clear, testable acceptance criteria
- ✅ Include business context and user value
- ✅ Document technical limitations upfront
- ❌ Don't include implementation details (focus on "what", not "how")
- ❌ Don't write code or architectural designs in specs

### For Planning
- ✅ Break down into small, independently testable tasks
- ✅ Identify and document dependencies between tasks
- ✅ Estimate complexity and potential risks realistically
- ✅ Include rollback strategies for risky changes
- ❌ Don't implement code during the planning phase
- ❌ Don't skip specification review before planning

### For Analysis
- ✅ Use when approaching unfamiliar codebases
- ✅ Document findings for team knowledge sharing
- ✅ Identify both patterns to follow and anti-patterns to avoid
- ✅ Create context documents before major refactoring
- ✅ Include concrete examples in your analysis

### For Implementation
- ✅ Follow the approved plan strictly
- ✅ Test each step before proceeding to the next
- ✅ Maintain consistency with existing coding standards
- ✅ Document changes and rationale as you go
- ✅ Commit frequently with descriptive messages
- ❌ Don't deviate from the approved plan without updating it first
- ❌ Don't skip tests "for now" - write them during implementation

### General Workflow Tips

1. **Always start with `/specify`** for any new work, even small changes
2. **Review and iterate on plans** before coding - catching issues early saves time
3. **Use `/analyse` proactively** when joining projects or before refactoring
4. **Keep tasks small and focused** - easier to review, test, and merge
5. **Commit after completing each task** in the plan - easier to rollback
6. **Update documentation** as you work - specifications and plans are living documents
7. **Version your task files** - commit `.tasks/` contents to track decision history
8. **Reuse analysis** - reference `.context/` files in new specifications
9. **Customise prompts** for your team's workflow and terminology
10. **Iterate on instructions** - improve language-specific guidelines based on experience

---

## Advanced Customisation

### Creating Custom Slash Commands

You can add your own slash commands for project-specific workflows by creating new prompt files:

1. **Create a new prompt file**: `.github/prompts/review.prompt.md`

```markdown
---
description: Review code for security and performance issues
tools: ['edit', 'search', 'problems', 'vscodeAPI']
---

# Code Review Mode

Perform thorough code review focusing on:
- Security vulnerabilities
- Performance bottlenecks
- Code quality and maintainability
- Test coverage

## Process
1. Analyze the specified files or changes
2. Identify issues by category
3. Suggest specific improvements
4. Create report in `.context/review-{timestamp}.md`
```

2. **Use it**: `/review src/authentication/`

### Chaining Commands

You can create workflows that chain multiple commands:

```
1. /analyse existing-auth → understand current implementation
2. /specify add-oauth → define OAuth requirements
3. /plan add-oauth → create implementation plan
4. /implement add-oauth → build the feature
```

### Customising Instruction Files

All instruction files in `.github/instructions/` can be customised:

**Language-specific** (`.github/instructions/csharp.instructions.md`):
```markdown
---
description: C# coding standards
applyTo: '**/*.cs, **/*.csproj, **/*.sln, **/*.razor, **/*.cshtml'
---
## Guidelines
- Target Framework: .NET 9
- Follow Microsoft C# conventions
- Use async/await for asynchronous programming
```

**Project-specific** (`.github/instructions/our-patterns.instructions.md`):
```markdown
---
description: Our project patterns
applyTo: '**/*.cs'
---
## Our Specific Patterns
- Use our ILogger<T> wrapper
- All services inherit from BaseService
- Repository pattern for data access

## Our Exceptions
- We use PascalCase for JSON properties (differs from .NET defaults)
- Reason: Frontend team consistency requirement
```

When both match a file, all matching instructions are applied together.


### Getting Started

1. **Clone or copy** the SpecPilot framework files into your repository:
   - `.github/` — instructions, prompts, and agents
   - `.vscode/` — MCP server configuration and VS Code settings
   - Optionally: `AGENTS.md`, `CLAUDE.md`, `.claude/` for multi-platform support
2. **Install prerequisites**: Ensure [Node.js](https://nodejs.org/) (with `npx`) is installed — required for MCP servers
3. **Configure environment**: Copy `.env.example` to `.env` and fill in any required API keys
4. **Review and customise** the existing instruction files in `.github/instructions/`
5. **Add new instruction files** in `.github/instructions/` for your project-specific guidelines, using YAML frontmatter with `applyTo` glob patterns
6. **Verify VS Code settings**: Ensure `.vscode/settings.json` is present with agent-related settings enabled
7. **Start with a small feature** using `/specify` to test the workflow
8. **Iterate and improve** your instructions, prompts, and agents based on experience

