---
description: Run a feature through the full routed pipeline (spec → build → 4 reviews → approve). Use `/factory approve <feature>` to clear a needs-human item.
argument-hint: <feature description> | approve <feature>
---

If `$ARGUMENTS` starts with `approve`: a human has reviewed the escalated feature. Merge it to main and remove the needs-human label. Stop.

Otherwise run this pipeline for: **$ARGUMENTS**

1. **spec-writer** agent → writes `specs/<feature>.md`. Once only.
2. **builder** agent → implements the spec, tests pass.
   - If builder reports `ESCALATE: hard-part` → run **hard-part** agent, then continue.
3. **Reviews**: launch all four in parallel, in a single message:
   **reviewer-security**, **reviewer-ux**, **reviewer-ui**, **reviewer-code**.
   WAIT FOR ALL 4 before continuing.
4. If any review returns `CHANGES:` → resume **builder** with the combined list, then repeat step 3.
   Max 2 rework rounds. After round 2 with CHANGES still open → mark needs-human, stop.
5. All PASS → **approver** agent.
   - `APPROVE` → merge to main.
   - `ESCALATE` → mark needs-human; report and stop.

At the end print one line per stage: `stage — model/effort — result`.
