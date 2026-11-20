# Escalation

Subagent effort is set in frontmatter, so "escalating" means re-running the same job with a stronger agent or at a higher effort.

## Bug ladder

| Step | Agent | Model / effort | Move up when |
|---|---|---|---|
| 1 | debugger | opus / medium | fix failed once |
| 2 | debugger (high) | opus / high | fix failed again |
| 3 | debugger (max) | opus / max | still failing → needs-human |

To run step 2 or 3, ask Claude Code: *"Use the debugger agent at high effort"*. The Agent tool accepts an `effort` override. You can also copy `debugger.md` to `debugger-high.md` / `debugger-max.md` and change the `effort:` line.

## Hard part

Any agent can report `ESCALATE: hard-part` → the **hard-part** agent (opus / xhigh) gets the earlier attempts' notes and takes over.

## Review rework

The builder gets at most **2** rework rounds from the reviewers. If CHANGES are still open after that, the feature goes to needs-human, and a person clears it with `/factory approve <feature>`.
