# Escalation

Subagent effort is set in frontmatter, so "escalating" means re-running the same job with a stronger agent or at a higher effort.

## Bug ladder

| Step | Agent | Model / effort | Move up when |
|---|---|---|---|
| 1 | debugger | opus / medium | fix failed once |
| 2 | debugger (high) | opus / high | fix failed again |
| 3 | debugger (max) | opus / max | still failing → needs-human |

To move up a step, re-run the same job at the next effort level (in Claude Code, ask for the debugger agent at high or max effort).

## Hard part

Any stage that hits a genuine dead end hands over to the hard-part stage (opus / xhigh). It gets the earlier attempts' notes and takes over.

## Review rework

The builder gets at most **2** rework rounds from the reviewers. If CHANGES are still open after that, the feature goes to needs-human, and a person decides whether to merge.
