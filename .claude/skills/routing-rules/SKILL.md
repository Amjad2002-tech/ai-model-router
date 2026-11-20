---
name: routing-rules
description: Decision rules for picking the cheapest model and effort that can do a task well. Use when triaging any request or deciding whether to escalate.
---

# Routing rules

Goal: spend tokens only where they change the result. Start cheap, escalate on evidence.

## Classify

| Signal in the request | Class | Goes to |
|---|---|---|
| Question, explanation, typo, rename, 1-line config | trivial | router does it (sonnet/low) |
| "add", "build", "implement", new behaviour | feature | /factory → spec-writer (fable/low) |
| layout, screen, component structure, schema, API shape | design | designer (opus/medium) |
| error, broken, failing test, wrong output | bug | debugger (opus/medium) |
| bug that survived a fix, intermittent, race, memory, data loss | hard-bug | debugger at high → max |
| "review this", PR link | review | 4 reviewers in parallel |
| any agent reports `ESCALATE: hard-part` | hard part | hard-part (opus/xhigh) |

When unsure between two classes, pick the cheaper one. Escalation is cheap; over-spending on every request is not.

## Escalate one step when

- the same test still fails after a fix attempt
- the fix spreads over 5+ files or across modules
- the root cause is still unknown after one investigation pass
- the code is concurrency, memory, data-loss or security-critical

Ladder: `medium → high → max → needs-human`. Never skip straight to max.

## Saving tokens

- Pass agents a one-line brief plus file paths, not pasted file contents.
- Reviewers read the diff only, never the whole repo.
- The spec is written once; builders read it, they don't regenerate it.
- Reviewers cap output at 5 items.
