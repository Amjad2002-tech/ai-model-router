---
name: ai-model-router
description: Route each coding or AI task to the cheapest model and effort level that can do it well, and run features through a spec, build, four-review, approve pipeline. Use when triaging a request, planning a feature, fixing bugs, reviewing changes, or deciding whether to escalate effort.
---

# AI Model Router

Goal: fewer tokens, better answers, faster responses. Start cheap and escalate only on evidence.

## 1. Triage every request

Classify the request first, then route it.

| Signal | Class | Model / effort |
|---|---|---|
| Question, explanation, typo, rename, one-line config | trivial | Sonnet / low: just do it |
| Add, build, implement, new behaviour | feature | run the pipeline (section 2) |
| Layout, screen, component structure, schema, API shape | design | Opus / medium |
| Error, broken, failing test, wrong output | bug | Opus / medium |
| Bug that survived a fix, intermittent, race, memory, data loss | hard bug | Opus / high, then max |
| "Review this", a diff or PR | review | the four reviewers (section 3) |
| Any stage is stuck at a genuine dead end | hard part | Opus / xhigh |

When two classes fit, pick the cheaper one. Escalating later costs less than overspending on every request.

## 2. Feature pipeline

1. **Spec** (Fable / low, once): write `specs/<feature>.md` with the goal, a testable acceptance checklist, the files likely touched, risks, and what is out of scope. One page, no code.
2. **Build** (Opus / medium): implement exactly what the spec says, with a test for every acceptance criterion. If the same failure happens twice, stop and escalate to the hard part instead of guessing.
3. **Review**: run all four reviews in parallel and wait for all of them (section 3).
4. **Rework**: if any review returns CHANGES, the builder fixes only the listed items and the reviews run again. Allow at most **2 rounds**; if changes are still open after that, mark the feature needs-human.
5. **Approve** (Opus / medium): check that the acceptance criteria are met, the tests pass and all four reviews say PASS.
   - APPROVE: merge to main.
   - ESCALATE: mark needs-human with the reason. A person decides whether to merge.

At the end, report one line per stage: `stage — model/effort — result`.

## 3. Reviewers

Each reviewer reads the **diff only** and outputs `PASS` or `CHANGES:` followed by a numbered list in the form `file:line — issue — fix`. Each list holds at most 5 items, most important first.

| Reviewer | Model / effort | Looks at |
|---|---|---|
| Security | Opus / medium | auth, input handling, secrets, injection, data exposure (real issues only) |
| UX | Sonnet / low | flows, empty and error states, copy, basic accessibility |
| UI design | Sonnet / low | consistency, spacing, design tokens, responsive behaviour |
| Code | Sonnet / low | correctness, tests (run them), readability, duplication |

## 4. Escalation

Step up **one** level at a time, never straight to max: `medium → high → max → needs-human`.

Step up when:
- the same test still fails after a fix attempt
- the fix spreads over 5 or more files or crosses module boundaries
- the root cause is still unknown after one investigation pass
- the code is concurrency, memory, data-loss or security-critical

Before a stronger model starts, give it the earlier attempts' notes so it does not repeat them. See `references/escalation.md` for the full ladder.

## 5. Token-saving habits

- Brief each stage with one line plus file paths, not pasted file contents.
- The spec is written once; later stages read it rather than regenerating it.
- Reviewers read the diff, never the whole repo.
- Keep outputs short: verdicts, lists and one-line reasons.
