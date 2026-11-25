# solo-orchestrate

A Claude Code skill that turns a session into an orchestrator of other coding
agents: it plans, briefs implementation workers, sends their PRs to reviewers
from a different model family, verifies every finding itself, and routes all
questions back to you.

It is an independent skill built for [Solo](https://soloterm.com) (not
affiliated with it), which spawns and watches agent
processes and provides todos and idle timers. Without Solo, it falls back to
running the agent CLIs as background processes.

Claude Code orchestrates and Claude Code agents do all the implementation.
Codex and Grok are optional: when installed, they review plans and PRs from a
different model family, and never write code.

## What it does

- **Plans first.** Forms its own view, gets a planning consult, verifies the
  claims, and shows you the plan before any code is written.
- **Picks the worker model per task.** Light tasks go to Sonnet, regular ones to
  Opus, demanding ones to Fable, and it escalates or steps down as the work
  changes.
- **Reviews with different eyes.** The agent that wrote the code never reviews
  it. Reviewers are read-only and report file:line findings with a verdict.
- **Verifies, never votes.** Every finding is checked against the code. Only
  provable blockers get patch rounds; the rest become follow-ups.
- **Asks before anything irreversible.** Push, PR, merge, release and deploy each
  need your words.

## Install

```bash
git clone git@github.com:mathiasgrimm/solo-orchestrate.git ~/.claude/skills/solo-orchestrate
```

Then ask for another agent in your own words: "spin an agent to implement
this", "get codex to review the PR", "check the plan with codex", "you are the
orchestrator". The skill does not delegate on its own initiative.

## Customise

Edit the **Roster** and **Worker tiers** tables in `SKILL.md` to match the
agents and models you have. Everything else is general.

## Files

| File | Purpose |
| --- | --- |
| `SKILL.md` | The workflow: roster, tiers, setup, planning, implementation, review loop, watching agents, permissions |
| `references/worker-brief.md` | Fill-in brief for implementation workers |
| `references/review-prompt.md` | PR review and planning consult templates, with the read-only reviewer rules |
| `references/solo-pitfalls.md` | Measured traps when driving agents through Solo |
