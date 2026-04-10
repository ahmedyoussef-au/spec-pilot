# SpecPilot

A plugin for **Claude Code** and **GitHub Copilot CLI** that brings spec-driven development to AI coding agents. SpecPilot ships a set of slash commands, agents, and skills that guide you through specification, planning, analysis, implementation, review, and documentation — so AI-assisted work stays consistent, auditable, and aligned with your team's standards.

## Table of Contents

- [What You Get](#what-you-get)
- [Installation](#installation)
- [The Spec-Driven Workflow](#the-spec-driven-workflow)
- [Slash Commands](#slash-commands)
- [Agents](#agents)
- [Skills](#skills)
- [MCP Servers](#mcp-servers)
- [Working Directories](#working-directories)
- [Workflow Examples](#workflow-examples)
- [Best Practices](#best-practices)
- [Uninstall](#uninstall)

---

## What You Get

Installing SpecPilot adds to your AI coding session:

- **8 slash commands** — `/specify`, `/refine`, `/plan`, `/analyse`, `/implement`, `/code-review`, `/troubleshoot`, `/save`
- **4 agents** — `Architect` (user-invokable), `Scout`, `Auditor`, `Writer` (subagents)
- **4 skills** — `docx`, `pdf`, `xlsx`, `webapp-testing` document and testing helpers
- **MCP server config** for context7, sequential-thinking, and package-version (Claude Code)

The whole plugin is defined in [.claude-plugin/plugin.json](.claude-plugin/plugin.json) (Claude Code) and [plugin.json](plugin.json) (GitHub Copilot CLI), and ships agents, commands, and skills as directory trees that both ecosystems auto-discover.

---

## Installation

SpecPilot can be installed into Claude Code or GitHub Copilot, from either the terminal or directly inside VS Code. Pick whichever path matches your setup.

### Claude Code (CLI and VS Code extension)

The Claude Code CLI and the VS Code extension share the same plugin state, so a single install command covers both.

```bash
claude plugin marketplace add ahmedyoussef-au/spec-pilot
claude plugin install spec-pilot@spec-pilot-market
```

Verify:

```bash
claude plugin list
```

You should see `spec-pilot@spec-pilot-market` listed as enabled. Start a fresh `claude` session (or reload the VS Code extension) and type `/help` to confirm the SpecPilot commands appear.

### GitHub Copilot CLI

Requires [GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli) installed and authenticated.

```bash
copilot plugin install ahmedyoussef-au/spec-pilot
```

Or load it from a local checkout for development:

```bash
copilot --plugin-dir /path/to/spec-pilot
```

Verify:

```bash
copilot plugin list
```

### GitHub Copilot Chat in VS Code

VS Code's [Agent Plugins (Preview)](https://code.visualstudio.com/docs/copilot/customization/agent-plugins) feature loads the same `plugin.json` format, so you can install SpecPilot directly into Copilot Chat without leaving the editor.

Choose one of the three install paths:

1. **Command Palette** — press `Cmd+Shift+P` (macOS) or `Ctrl+Shift+P` (Windows/Linux), run **Chat: Install Plugin From Source**, and paste:
   ```
   https://github.com/ahmedyoussef-au/spec-pilot
   ```
2. **Extensions sidebar** — open Extensions (`Cmd+Shift+X` / `Ctrl+Shift+X`), type `@agentPlugins` in the search box, and install from there.
3. **Chat Customizations editor** — open the Plugins page and click the **+** button to add a plugin from a Git URL.

After installation, SpecPilot's slash commands and agents become available in the Copilot Chat panel. Manage installed plugins from the **Agent Plugins — Installed** section of the Extensions view.

---

## The Spec-Driven Workflow

SpecPilot structures AI-assisted work into distinct phases, each triggered by a slash command:

```
1. SPECIFY    → Define what needs to be built (requirements, acceptance criteria)
2. REFINE     → Surface ambiguities and fill gaps before planning
3. PLAN       → Break down how to build it (tasks, dependencies)
4. ANALYSE    → Understand existing code or systems (optional)
5. IMPLEMENT  → Build according to the plan
6. REVIEW     → Audit code changes for quality and correctness
7. TROUBLESHOOT → Diagnose and resolve issues
8. SAVE       → Preserve conversation context for later
```

Each phase produces a reviewable artefact written to a structured location, so your decisions, plans, and analyses become part of the project history rather than ephemeral chat.

---

## Slash Commands

All commands live in [commands/](commands/) and can be customised by editing the corresponding `.md` file.

### `/specify` — Create Specifications

Define requirements, objectives, and acceptance criteria before any code is written. Asks clarifying questions when the brief is ambiguous and writes output to `.tasks/{task_name}/specifications.md`.

```
/specify Add user authentication with OAuth2
/specify Fix memory leak in data processor
```

### `/refine` — Refine a Spec or Plan

Surfaces ambiguities, hidden assumptions, and missing details in an existing `specifications.md` or `plan.md`, then asks targeted clarification questions. Reduces churn before planning or implementation begins.

```
/refine oauth-authentication
```

### `/plan` — Create an Implementation Plan

Reads the specification for a task and produces a step-by-step roadmap at `.tasks/{task_name}/plan.md`. Never writes code — planning only.

```
/plan oauth-authentication
```

### `/analyse` — Analyse Code or Systems

Deep-dives into existing code, data, or architecture and writes a structured report to `.context/{context_name}.md`. Use this before refactors or when onboarding to unfamiliar code.

```
/analyse Payment processing module
```

### `/implement` — Execute the Plan

Reads the plan for a task and executes it step by step, following project coding standards and existing patterns.

```
/implement oauth-authentication
```

### `/code-review` — Review Changes

Audits code changes for correctness, edge cases, security concerns, and adherence to project conventions. Produces a structured review with actionable feedback.

### `/troubleshoot` — Diagnose Issues

Walks through a structured diagnosis of a bug or failure: reproducing the issue, isolating the cause, and proposing fixes.

### `/save` — Save Conversation Context

Summarises the current conversation — key decisions, findings, open questions — and writes it to `.context/{context_name}.md` so it can be picked up later.

```
/save authentication-decisions
```

---

## Agents

SpecPilot ships four agents in [agents/](agents/). One is user-invokable; the rest are subagents that get called indirectly.

### `Architect` (user-invokable)

Orchestrates a full feature lifecycle — research, design, audit, documentation. Delegates to the other three agents.

**Workflow:**
1. Confirms scope and which folders to search
2. Delegates to `Scout` for codebase research
3. Produces a technical design based on findings
4. Delegates to `Auditor` for review
5. Delegates to `Writer` for documentation
6. Presents the complete package for approval

### `Scout` (subagent)

Traces code flows, maps dependencies, and returns evidence-backed findings with code snippets. Respects scoping constraints.

### `Auditor` (subagent)

Reviews designs, plans, or code proposals for correctness, edge cases, security, and best practices. Returns a structured verdict.

### `Writer` (subagent)

Translates technical designs into clear documentation — README updates, API specs, tutorials, inline docs. Follows the project's existing conventions and Australian English spelling.

Edit the files in [agents/](agents/) to change roles, tools, or model preferences.

---

## Skills

SpecPilot bundles four productivity skills in [skills/](skills/) for document handling and web testing:

| Skill | Purpose |
|---|---|
| [docx](skills/docx/) | Create, read, and edit Word documents |
| [pdf](skills/pdf/) | Work with PDFs including form filling |
| [xlsx](skills/xlsx/) | Create, read, and edit Excel spreadsheets |
| [webapp-testing](skills/webapp-testing/) | Browser-based testing for web applications |

Each skill has its own `SKILL.md` describing usage.

---

## MCP Servers

Claude Code receives pre-configured [MCP](https://modelcontextprotocol.io/) server definitions via [.claude/mcp.json](.claude/mcp.json):

| Server | Purpose |
|---|---|
| **context7** | Retrieves up-to-date library documentation and code examples |
| **sequential-thinking** | Iterative problem-solving for complex architectural decisions |
| **package-version** | Checks package versions across npm, PyPI, Docker Hub, GitHub Actions |

MCP servers launch via `npx`, so **Node.js** must be installed on the host.

Some servers may require API keys. Copy [.env.example](.env.example) to `.env` and populate any required values.

---

## Workflow Examples

### Example 1: Adding a New Feature

```
# 1. Create the spec
User: /specify Add user authentication with OAuth2

SpecPilot: Asks clarifying questions (providers, scope, session strategy),
           then writes .tasks/oauth-authentication/specifications.md

# 2. Refine if needed
User: /refine oauth-authentication

SpecPilot: Surfaces hidden assumptions, asks targeted questions, updates the spec

# 3. Plan the work
User: /plan oauth-authentication

SpecPilot: Writes .tasks/oauth-authentication/plan.md

# 4. Execute
User: /implement oauth-authentication

SpecPilot: Works through the plan step by step
```

### Example 2: Understanding Existing Code

```
User: /analyse Payment processing module

SpecPilot: Writes .context/payment-processing.md with:
  - Business purpose (subscription billing, refunds)
  - Technology stack (Stripe API, webhooks, queues)
  - Strengths, weaknesses, recommendations
```

### Example 3: Reviewing Changes

```
User: /code-review

SpecPilot: Audits current changes for correctness, edge cases, security,
           and adherence to project patterns; returns actionable feedback
```

---

## Best Practices

### Specifications
- Be specific about requirements and constraints
- Define testable acceptance criteria
- Focus on **what**, not **how** — no code or implementation details

### Planning
- Break work into small, independently testable tasks
- Document dependencies between tasks
- Don't write code during the planning phase

### Analysis
- Use `/analyse` before refactors and when joining unfamiliar codebases
- Document both patterns to follow and anti-patterns to avoid

### Implementation
- Follow the approved plan; update it before deviating
- Commit frequently with descriptive messages
- Write tests as you go, not "later"

### General
- Start every change with `/specify`, even small ones
- Review plans before implementing — catching issues early saves time
- Commit `.tasks/` and `.context/` contents to preserve decision history

---

## Uninstall

### Claude Code

```bash
claude plugin uninstall spec-pilot@spec-pilot-market
claude plugin marketplace remove spec-pilot-market
```

### GitHub Copilot CLI

```bash
copilot plugin uninstall spec-pilot
```

---

## Repository

Source: [github.com/ahmedyoussef-au/spec-pilot](https://github.com/ahmedyoussef-au/spec-pilot)

License: MIT
