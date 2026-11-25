# solo-orchestrate

A Claude Code skill that orchestrates coding agents through
[Solo](https://soloterm.com) (an independent skill, not affiliated with Solo).
It plans, gets the plan reviewed, has Claude workers implement it, gets the PRs
reviewed, and verifies every finding itself. Codex and Grok only review, never
write code. Push, PR and merge need your explicit permission.

## Defaults

`/solo-orchestrate <task>` runs the full pipeline. Each PR is rated by tier:

| Tier | Plan review | Implementer | PR review |
| --- | --- | --- | --- |
| Light | none | Sonnet 5.5, medium | Codex, high (code changes only) |
| Standard | Codex, high | Opus 5.5, high | Codex, high |
| Demanding | Codex + Fable 5.2, high | Opus 5.5, high | Codex + Fable 5.2, high |
| Escalation | n/a | Fable 5.2, high | Codex, high |

- Reviewers always run at high effort. Grok reviews only when you name it.
- Fable implements only after Opus fails a demanding PR twice.
- Run the orchestrator session itself on Opus 5.5 high.

## Examples

```text
/solo-orchestrate add CSV export to the orders page
/solo-orchestrate add two-factor login in small PRs, open them, don't merge
/solo-orchestrate fix the flaky invoice test, no plan review
/solo-orchestrate rewrite the importer, use fable to implement
review this PR with codex
review the plan with codex and fable
spin a sonnet agent to create a private repo for this project
```

Without `/solo-orchestrate`, only the agents you name are started.

## Install

Use a release tag, not `main`. Versions are in [CHANGELOG.md](CHANGELOG.md).

```bash
git clone --branch 0.1.0 git@github.com:mathiasgrimm/solo-orchestrate.git ~/.claude/skills/solo-orchestrate
```

To update, ask Claude "update solo-orchestrate". It shows what changed in newer
versions, switches only when you agree, and keeps your own edits.

## Customize

Edit the tables under **Defaults** in `SKILL.md`. If your own instructions
forbid overriding configured models, the skill follows them.
