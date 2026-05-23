# SpecPilot

A plugin for **Claude Code** and **GitHub Copilot CLI** that brings spec-driven development to AI coding agents. SpecPilot ships a set of slash commands, agents, and skills that guide you through specification, planning, analysis, implementation, review, and documentation — so AI-assisted work stays consistent, auditable, and aligned with your team's standards.

## What is spec-driven development?

Spec-driven development flips the usual AI coding workflow on its head. Instead of prompting an agent to "just build it" and hoping the output matches what you had in mind, you first write a specification — what the feature does, who it serves, how success is measured — then a plan that breaks it into concrete steps, and only then let the agent implement. Each artefact is reviewable, version-controlled, and feeds the next stage, so the agent works from shared intent rather than guesswork. The payoff is fewer wrong turns, code that reflects actual requirements, and a paper trail you can audit, share, or hand to the next contributor (human or AI).

## Quick start

After [installing](#installation), a four-command loop covers most work:

```
/specify   Add user authentication with OAuth2
           -> writes .tasks/oauth-authentication/specifications.md

/plan      oauth-authentication
           -> writes .tasks/oauth-authentication/plan.md

/implement oauth-authentication
           -> executes the plan, file by file

/code-review
          -> audits the diff for bugs, edge cases, and security
```

Everything SpecPilot writes lands in two folders. Commit them to keep the paper trail:

- `.tasks/<feature>/` for specs, plans, and implementation notes
- `.context/<topic>/` for analysis reports and saved conversation summaries

## Installation

### Claude Code (CLI and VS Code extension)

The CLI and the VS Code extension share state, so one install covers both.

```bash
claude plugin marketplace add ahmedyoussef-au/spec-pilot
claude plugin install spec-pilot@spec-pilot-market
claude plugin list   # verify
```

Start a fresh `claude` session (or reload the VS Code extension) and run `/help` to see the SpecPilot commands.

<details>
<summary><strong>GitHub Copilot CLI</strong></summary>
<br>
Requires [GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli) installed and authenticated.

```bash
copilot plugin install ahmedyoussef-au/spec-pilot
copilot plugin list   # verify
```

To load from a local checkout for development:

```bash
copilot --plugin-dir /path/to/spec-pilot
```
<br>
</details>
<br>
<details>
<summary><strong>GitHub Copilot Chat in VS Code</strong></summary>
<br>
VS Code's [Agent Plugins (Preview)](https://code.visualstudio.com/docs/copilot/customization/agent-plugins) loads the same `plugin.json`. Three install paths:

1. **Command Palette** (`Cmd/Ctrl+Shift+P`) -> run **Chat: Install Plugin From Source** -> paste `https://github.com/ahmedyoussef-au/spec-pilot`
2. **Extensions sidebar** (`Cmd/Ctrl+Shift+X`) -> search `@agentPlugins` and install
3. **Chat Customizations editor** -> open Plugins page, click **+** to add from Git URL

Manage installed plugins from the **Agent Plugins - Installed** section of the Extensions view.
<br>
</details>

## Commands

| Command | What it does | Output |
|---|---|---|
| [`/specify`](commands/specify.md) | Defines requirements and acceptance criteria. Asks clarifying questions when the brief is vague. | `.tasks/<task>/specifications.md` |
| [`/refine`](commands/refine.md) | Reviews an existing spec or plan, surfaces gaps and ambiguities, asks targeted questions. | Updates the existing file |
| [`/plan`](commands/plan.md) | Turns a spec into a step by step implementation roadmap. Does not write code. | `.tasks/<task>/plan.md` |
| [`/analyse`](commands/analyse.md) | Deep dives into existing code or systems. Use before refactors or when onboarding. | `.context/<topic>.md` |
| [`/implement`](commands/implement.md) | Executes the plan, following project conventions. | Code changes |
| [`/code-review`](commands/code-review.md) | Audits the current diff for correctness, edge cases, and security. | Structured review in chat |
| [`/troubleshoot`](commands/troubleshoot.md) | Walks a structured diagnosis: reproduce, isolate, propose fix. | Diagnosis in chat |
| [`/save`](commands/save.md) | Summarises the current conversation so it can be resumed later. | `.context/<topic>.md` |

All commands are plain Markdown files in [commands/](commands/). Edit them to change prompts or behaviour.

## Agents

Four agents in [agents/](agents/). Only the first is invoked directly; the rest are subagents.

- **[Architect](agents/architect.agent.md)** (user invokable) orchestrates a full feature: confirms scope, delegates research to Scout, drafts a design, sends it to Auditor for review, then Writer for documentation, and presents the package for approval.
- **[Scout](agents/scout.agent.md)** traces code flows, maps dependencies, returns evidence backed findings with snippets.
- **[Auditor](agents/auditor.agent.md)** reviews designs, plans, or code proposals for correctness, edge cases, security, and best practices.
- **[Writer](agents/writer.agent.md)** translates designs into clear documentation. Follows project conventions and Australian English spelling.

## Skills

| Skill | Purpose |
|---|---|
| [deep-reasoning](skills/deep-reasoning/) | For hard problems with large evidence (logs, traces, sprawling codebases). Pairs `sequential-thinking` (structured steps) with `context-mode` (sandboxed analysis returning small results), so reasoning stays focused without the context window drowning in raw data. |

## MCP servers (Claude Code)

Pre configured in [.mcp.json](.mcp.json):

| Server | Purpose |
|---|---|
| **context7** | Up to date library documentation and code examples |
| **sequential-thinking** | Iterative reasoning for complex decisions |
| **context-mode** | Sandboxed analysis of large data without ingesting it |
| **package-version** | Checks package versions across npm, PyPI, Docker Hub, GitHub Actions |
| **browsermcp** | Drives a browser for end to end testing and UI checks |

MCP servers launch via `npx`, so **Node.js** must be installed.

Some servers need API keys. Copy [.env.example](.env.example) to `.env` and populate the values (currently: `CONTEXT7_API_KEY`).

## More examples

### Understanding existing code

```
/analyse Payment processing module
   -> writes .context/payment-processing.md with business purpose,
      tech stack, strengths, weaknesses, and recommendations
```

### Refining a vague spec

```
/specify Add notifications

   (writes a draft spec but the brief was thin)

/refine notifications

   -> surfaces hidden assumptions (channels? batching? delivery
      guarantees?), asks targeted questions, updates the spec
```

## Uninstall

```bash
# Claude Code
claude plugin uninstall spec-pilot@spec-pilot-market
claude plugin marketplace remove spec-pilot-market

# GitHub Copilot CLI
copilot plugin uninstall spec-pilot
```

## Repository

Source: [github.com/ahmedyoussef-au/spec-pilot](https://github.com/ahmedyoussef-au/spec-pilot)

License: MIT
