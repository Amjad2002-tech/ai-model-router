# AI Model Router

Route every Claude Code request to the **cheapest model and effort that can do it well**. Small jobs stop costing Opus-max tokens, and hard problems still get the strongest model.

Goals: **fewer tokens · better answers · faster responses.**

## Pipeline

```
feature
  └─ spec-writer (fable / low, once)
       └─ builder (opus / medium) ◄──────────────┐
            ├─ security  (opus / medium)         │ any CHANGES:
            ├─ ux        (sonnet / low)          │ resume builder
            ├─ ui design (sonnet / low)          │ (max 2 rounds)
            └─ code      (sonnet / low)          │
                 └─ WAIT FOR ALL 4 ──────────────┘
                      └─ all PASS → approver (opus / medium)
                             ├─ APPROVE  → merge to main
                             └─ ESCALATE → needs-human ← /factory approve
```

## Routing table

| Role | Model | Effort |
|---|---|---|
| Router (every request) | Sonnet 5.5 | low |
| Spec-writer / planning | Fable 5.2 | low |
| Builder | Opus 5.5 | medium |
| Design | Opus 5.5 | medium |
| Bugs | Opus 5.5 | medium → high → max |
| Hard part | Opus 5.5 | xhigh |
| Security review | Opus 5.5 | medium |
| UX / UI / Code review | Sonnet 5.5 | low |
| Approver | Opus 5.5 | medium |

All values live in [`routing.yaml`](routing.yaml). The escalation rules are in [`docs/escalation.md`](docs/escalation.md).

## Install

Copy the `.claude/` folder into your project, or into `~/.claude/` to use it everywhere:

```bash
git clone https://github.com/Amjad2002-tech/ai-model-router.git
cp -r ai-model-router/.claude/* your-project/.claude/
```

## Use

```
/factory add a dark-mode toggle to settings     # full pipeline
/factory approve dark-mode-toggle               # clear a needs-human item
"use the router agent: why is login slow?"      # triage a single request
```

## Why the router runs on Sonnet

The router runs on every request, which makes it the biggest single cost. Triage only needs classification, not deep reasoning, so it runs on Sonnet at low effort and hands real work to Opus.

## License

MIT
