---
name: deep-reasoning
description: Use this skill on hard problems where reasoning would otherwise drown the context window in raw data — large logs, datasets, traces, sprawling search results, or any investigation that needs to crunch a lot of stuff to reach a small answer. It pairs the sequential-thinking MCP (for structured, revisable steps) with the context-mode MCP (for sandboxed analysis that returns small structured results instead of raw payloads). Reach for it whenever a problem is genuinely complex *and* evidence-heavy: tricky debugging across logs, architectural decisions that need data to back them up, ambiguous requirements hidden inside a big codebase, or any situation where the model would otherwise read a 5000-line file just to answer one question about it.
---

# Deep Reasoning

The unique job of this skill is to keep reasoning *focused* when the evidence is large. Plain `sequential-thinking` helps you think in steps, but on its own it doesn't stop you from filling the context window with raw logs and losing the thread. That's what `context-mode` is for — it lets you ask questions of large data without ingesting it. The two together let you reason about evidence-heavy problems without your working memory turning to mush.

If a problem is hard but the evidence is small (a few short files, a clear question), you don't need this skill — just think and answer. If the evidence is large but the problem is easy (look up one value in a CSV), you don't need this skill either — one `ctx_execute` call is enough on its own. This skill earns its keep when **both** conditions hold.

## Fitting into existing slash commands

Several spec-pilot commands (`/plan`, `/troubleshoot`, `/analyse`, `/code-review`, `/implement`) already invoke `sequential-thinking` themselves. When one of those commands is running, don't impose a parallel structure on top — fit into the command's workflow and add `context-mode` where it would genuinely reduce context bloat. The skill complements those commands; it doesn't override them.

## `context-mode` — think in code, not in tokens

The point of `context-mode` is captured by its own slogan: *think in code*. Instead of reading a 300KB log into context to find a pattern, you write a five-line script that returns the count, the timestamps, or the matching lines. Instead of dumping a 50-row SQL result, you write a query that returns the aggregate. The sandbox runs the script and gives you a small structured result; the raw data never enters your context window.

The tools available are `ctx_execute` (run a script), `ctx_batch_execute` (run several), `ctx_execute_file` (run a saved script), `ctx_index` and `ctx_fetch_and_index` (build a searchable index over files or web pages), and `ctx_search` (query that index).

**When to reach for it:**
- The data you need to look at is more than ~100 lines, *and* you'll need to do more than glance at it
- You're going to ask the same data multiple questions — index it once, query repeatedly
- You'd otherwise be tempted to Read a file and scroll through it looking for something
- Your analysis would produce a small answer (a count, a list, an aggregate, a yes/no) from a large input

**When to skip it and just use Read/Grep:**
- The file is short (under ~100 lines) or you only need to look once
- The question is "what does this code do" — Read is the right tool for code comprehension
- You already know the exact line you want — Grep is faster than writing a script

**Concrete shape — turning a "read the log" instinct into a script:**

Wrong shape: Read a 4000-line server log, scroll through it, try to spot which endpoint is failing.

Right shape: `ctx_execute` with a script like *"group lines matching `ERROR` by the endpoint path in the preceding request line, return the top 10 by count."* Result: a 10-row table. Context impact: negligible. Now you can reason about the table.

If a `ctx_execute` call comes back huge, that's a signal the script is wrong — narrow the query, aggregate harder, return fewer fields. The whole point is structured smallness.

## `sequential-thinking` — for the reasoning steps

Use `sequential-thinking` when externalizing your reasoning helps the user follow it, correct it mid-stream, or see *why* you reached a conclusion. The tool's own schema covers the mechanics (numbered thoughts, revisions, branches) — read its description for the inputs. What matters here is judgment:

- A thought should make progress — surface a constraint, rule out a hypothesis, raise a sharp question. Restating the problem isn't a thought.
- Revise earlier thoughts when an assumption breaks. This isn't optional politeness — the visible revision is how the user learns to trust the conclusion.
- Branch only when there are genuinely independent paths worth exploring (not to look thorough).
- Stop the moment you can act. The tool is a means; the action is the point.

## The loop

On a problem that fits this skill, the shape is:

1. **Frame** with one or two `sequential-thinking` thoughts. What's actually being asked? What are the constraints? What's unknown?
2. **Gather** with `context-mode` for anything large. Index once if you'll query the same data repeatedly. Each call should return a small structured result you can reason about.
3. **Reason** in more `sequential-thinking` thoughts, pulling specific facts from the sandbox as you go. Revise when an assumption breaks.
4. **Decide and act.** State the conclusion, propose the change, ask the clarifying question — whichever the work calls for. Stop thinking once the path is clear.

## Anti-pattern to recognize

The failure mode this skill exists to prevent looks like this:

> User: *"Why is our nightly job failing? Here's the log directory: `/var/log/nightly/`."*
>
> Assistant: *Reads `2026-04-11.log` (3200 lines) into context. Reads `2026-04-10.log` (2900 lines). Reads the runner config. Reads the wrapper script. Now has 8000 lines of mostly-noise in working memory and writes a hedged answer.*

The right shape is: one `ctx_execute` to extract error timestamps and exit codes across the recent logs, one or two `sequential-thinking` thoughts to interpret the pattern, then a targeted Read of the specific file and line the data points to. The reasoning stays sharp because the evidence stays small.
