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
                             └─ ESCALATE → needs-human (a person decides)
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

All values live in [`routing.yaml`](routing.yaml). The escalation rules are in [`skills/ai-model-router/references/escalation.md`](skills/ai-model-router/references/escalation.md).

## Install

### Claude app (claude.ai / desktop)

1. Download [`ai-model-router.zip`](ai-model-router.zip) from this repo.
2. In Claude, open **Settings → Capabilities → Skills** and upload the zip.
3. Ask Claude to build a feature, fix a bug or review a change, and the skill routes the work.

### Claude Code

```bash
git clone https://github.com/Amjad2002-tech/ai-model-router.git
cp -r ai-model-router/skills/ai-model-router ~/.claude/skills/
cp -r ai-model-router/.claude/agents/* ~/.claude/agents/     # optional: subagents with model + effort pinned
```

## Use

Just describe the work, for example *"add a dark-mode toggle to settings"*. The skill triages it and runs the pipeline. No slash commands are needed.

## Format

A skill is a **Markdown** file (`SKILL.md`) with a short YAML header for its name and description. `routing.yaml` is only a settings table for reference; Claude loads the `.md`.

## Why the router runs on Sonnet

The router runs on every request, which makes it the biggest single cost. Triage only needs classification, not deep reasoning, so it runs on Sonnet at low effort and hands real work to Opus.

## License

MIT
