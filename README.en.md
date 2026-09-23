# Planning Suite — P1~P6 Planning Chain + Orchestrator

[한국어 README](README.md)

A set of 7 Claude Skills that break the **entire strategic planning
process** — mid/long-term development plans, management plans,
business plans — into 6 sequential phases, each validated against
the ones before it.

Especially useful for **public-sector and government agency work** —
mid/long-term development plans, annual management plans, and
department-level business plans. The chain runs from environmental
scan through goal-setting, strategy options, feasibility review,
roadmap, and feedback loops, producing output in a consistent
government report format at every stage while automatically
validating consistency between phases.

## What's included

| Phase | Skill | Output |
|:---:|---|---|
| P1 Diagnose | `env-scanning-loop-p1` | Environmental scan (PEST · 3C · VRIO · SWOT) |
| P2 Goals | `goal-setting-loop-p2` | Mission/vision/OKR/KPI framework |
| P3 Strategy | `strategy-option-loop-p3` | Strategic initiative pool · ERRC · prioritization |
| P4 Review | `feasibility-review-loop-p4` | Feasibility screening (economics, legal, resources) |
| P5 Execute | `roadmap-design-loop-p5` | Roadmap · RACI · KPI rollout |
| P6 Feedback | `feedback-loop-p6` | Monitoring · contingency planning · cycle handoff |
| — | `planning-orchestrator` | **Control layer** — links, validates, and routes rollback across the 6 phases above |

`planning-orchestrator` is the only skill in the set that doesn't
produce content directly. It takes each phase's output through a
**Q-Gate** (4-part check: schema, carry-forward, cross-reference,
regression signal) and, if something's off, determines exactly which
earlier phase needs to be revisited (with the rollback scoped to
what's actually affected).

## Why this set

The two most common failure modes in planning work: **output from an
earlier phase gets lost or contradicted by the time you reach a
later one**, and **a single planning request actually spans multiple
phases with no clear entry point**. This set is built to address
both directly.

- **Each phase stands alone**: P1–P6 can each run independently —
  pick just the one phase you need.
- **`[DATA-P○-OUTPUT]` interface**: P2–P6 each emit a structured
  block the next phase can pick up directly (P1 hands off the
  environmental-scan report itself instead of a block). If a session breaks,
  paste that block into a new one and continue.
- **Q-Gate + rollback control**: with the orchestrator running, if a
  problem discovered during P5 (execution) turns out to trace back to
  a P2 (goal-setting) error, only the affected parts get reworked —
  not the whole plan from scratch.
- **Assumption-labeling protocol**: when information is missing
  mid-run, the skill doesn't stop — it labels the gap
  (`[Assumption A1...]`) with a confidence level and rationale, and
  keeps going, so you can correct it after reviewing the finished
  draft.

## Install

### Claude Code — one-line install (recommended)

```
/plugin marketplace add rjs-zzz/planning-suite
/plugin install goal-setting-loop-p2@planning-suite
```

Install only the phases you need. Installing all seven is recommended only when
drafting a full plan document — more skills means more trigger overlap.
`planning-orchestrator` is needed only when two or more phases are involved.

Available plugins: `env-scanning-loop-p1` `goal-setting-loop-p2`
`strategy-option-loop-p3` `feasibility-review-loop-p4` `roadmap-design-loop-p5`
`feedback-loop-p6` `planning-orchestrator`

### Claude.ai (web/desktop/mobile)

Zip the folder `plugins/<skill-name>/skills/<skill-name>/` and upload it on the
Skills screen (the menu path varies by plan and date — see the
[official guide](https://support.claude.com/en/articles/12512180)).

### See the output before installing

Real output samples live in [`examples/`](examples/).

- [P2 goal system output with its DATA block](examples/01_p2-goal-system.md)
- [Q-Gate rollback ruling with scoped re-propagation](examples/02_qgate-rollback.md)

## Usage

**When you need the full plan**: call `planning-orchestrator`. It
checks what you already have (e.g., an existing environmental scan)
to suggest a starting point, then calls the phase skills in sequence.

**When you only need one phase**: call that phase skill directly.
E.g., "run a SWOT analysis for our organization" triggers
`env-scanning-loop-p1` on its own.

**When you just need a single decision** (choose A or B, go/no-go):
this isn't the right set — use the separately published
`problem-solver` skill instead. That one's for a standalone
judgment call, not a component of a larger planning document.
During planning work, a Phase skill delegates a single judgment to
problem-solver (only on user request or for a decision-critical
one-way issue) and inherits the returned `[DATA-PS-OUTPUT]` block.

## Structure

```
planning-suite/
├── .claude-plugin/
│   └── marketplace.json          # Marketplace catalog (7 plugins)
├── plugins/
│   ├── env-scanning-loop-p1/
│   │   ├── .claude-plugin/plugin.json
│   │   └── skills/env-scanning-loop-p1/
│   │       ├── SKILL.md
│   │       ├── agents/           # PEST / 3C (market) / VRIO sub-agents, orchestrator logic
│   │       └── templates/
│   ├── goal-setting-loop-p2/…    # references/ (BSC, OKR, Golden Circle) + templates/
│   ├── strategy-option-loop-p3/… # references/ (portfolio matrices, ERRC, scoring)
│   ├── feasibility-review-loop-p4/…
│   ├── roadmap-design-loop-p5/…  # references/ (milestone, RACI, KPI design)
│   ├── feedback-loop-p6/…        # references/ (PDCA, double-loop learning, contingency)
│   └── planning-orchestrator/
│       └── skills/planning-orchestrator/
│           ├── references/       # Q-Gate checklists (per-phase schema criteria)
│           └── templates/        # Control dashboard template
├── examples/                     # Real output samples
├── COMMON_CONTROLS.md            # Single source for the shared control block
└── tools/sync_common.py          # Syncs that block into every skill
```

## Notes on use

- **Customizing for your organization**: the "Standing Context"
  section in each skill's SKILL.md is written generically on
  purpose. Filling it in with your organization's applicable laws,
  stakeholder map, and evaluation framework (public agency,
  corporate, etc.) meaningfully improves accuracy — each section has
  a note marking where to customize.
- **BSC perspective**: P2 defaults to the public-sector BSC variant
  (public value at the top of the causal chain). For a for-profit /
  revenue-driven context, SKILL.md includes guidance for switching to
  the standard BSC (financial perspective at the top).
- **Language**: all 7 skills are Korean-only. Every instruction,
  framework rule, and output template is written in Korean, and the
  output format follows Korean government report conventions
  (lead-with-conclusion, bulleted structure, 【 】 heading system).
  To use these in English, you'd need to translate each SKILL.md's
  "출력 표준" (Output Standard) section plus the references and
  templates yourself.
- **No sub-agent spawning**: "agents" in each skill refer to role
  switching (internal reasoning), not actual sub-agents spawned in
  Claude.ai chat. Even in multi-agent-capable environments like
  Claude Code, delegation for verification/re-confirmation purposes
  is explicitly disallowed.

## License

MIT License — free to use, modify, distribute, and use commercially.
Attribution isn't required, but it's appreciated.
