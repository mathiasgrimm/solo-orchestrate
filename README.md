# solo-orchestrate

A Claude Code skill that turns a session into an orchestrator of other coding
agents. Give it a task and it plans, gets the plan reviewed, briefs Claude
workers to implement it, gets the PRs reviewed, verifies every finding itself,
and routes every question back to you.

It is an independent skill built for [Solo](https://soloterm.com) (not
affiliated with it), which spawns and watches agent processes and provides
todos and idle timers. Without Solo, it falls back to running the agent CLIs
as background processes.

Claude Code orchestrates and Claude Code agents do all the implementation.
Codex (recommended) and Grok (optional) only review plans and PRs, read-only,
from a different model family.

## What it does

- **Plans first.** Writes the plan itself, gets it reviewed, verifies the
  claims, and shows you the plan with the model picked for each PR before any
  code is written.
- **Picks models per task.** Each PR is rated Light, Standard or Demanding, and
  gets the implementer and reviewers for that tier (see Defaults).
- **Reviews with different eyes.** The agent that wrote the code never reviews
  it. Reviewers are read-only, run at high effort, and report file:line
  findings with a verdict.
- **Verifies, never votes.** Every finding is checked against the code. Only
  provable blockers get patch rounds; the rest become follow-ups.
- **Asks before anything irreversible.** Push, PR, merge, release and deploy each
  need your words. Without them, a run ends with local branches reviewed and
  ready to push.

## Defaults

`/solo-orchestrate <task>` runs the full pipeline. The orchestrator rates each
PR and uses its row:

| Tier | Plan review | Implementer | PR review |
| --- | --- | --- | --- |
| Light (chores, renames, bumps) | none: the plan goes straight to you | Sonnet 5.5, medium | Codex, high, when the diff changes code; none for chores |
| Standard (most features and bugs) | Codex, high | Opus 5.5, high | Codex, high |
| Demanding (schema invariants, money, auth, concurrency, unknown-cause bugs) | Codex, high + Fable 5.2, high | Opus 5.5, high | Codex, high + Fable 5.2, high |
| Escalation (a Demanding PR failed verification twice) | n/a | Fable 5.2, high | Codex, high |

- **Fable reviews instead of implementing.** A review is a handful of turns,
  an implementation is hundreds. Fable is used where a flaw is cheapest to
  catch, the plan and the PR, and implements only on escalation.
- **Every reviewer runs at high effort.** Grok reviews only when you name it.
- **At most one Fable process runs at a time.** The plan says how many Fable
  runs to expect.
- **Run the orchestrator itself on Opus 5.5 at high effort.** Its job is
  judgment (planning and checking every review claim) over a long session.
  Sonnet is too weak for that, Fable costs about twice as much per token on a
  long context, and xhigh slows every turn. The skill can't change the model of
  the session you run it in.
- **No Codex installed:** Fable 5.2 high reviews Opus work, and you're told it's
  a weaker second opinion than another model family.

## Examples

Full pipeline with the defaults:

```text
/solo-orchestrate add CSV export to the orders page
```

Plans, has Codex review the plan, has an Opus worker implement it, has Codex
review the result, and stops with the branch ready to push.

Full pipeline with permission to open PRs:

```text
/solo-orchestrate add two-factor login, split it into small PRs and open them. don't merge
```

Likely rated Demanding (auth), so Codex and Fable review both the plan and
every PR. PRs are opened as each one passes review; merging is left to you.

Changing the defaults in the request:

```text
/solo-orchestrate fix the flaky invoice test, no plan review
/solo-orchestrate migrate sessions to Redis, also get grok to review the plan
/solo-orchestrate rewrite the importer, use fable to implement
```

Only the roles you name (no full pipeline):

```text
spin a sonnet agent to create a private repo for this project
review this PR with codex
review the plan with codex and fable
get a claude agent to implement the plan we just agreed, then review it with codex
```

Handing work off:

```text
hand this to an agent in the billing project, I'll talk to it directly
```

While a run is going, you can ask "how is the worker doing?" or "what did codex
say?". If you have to step away, say what's allowed, for example "keep going,
open PRs but don't merge anything".

## Install

Install a released version, never `main` (it holds unreleased work). The
latest version is listed in [CHANGELOG.md](CHANGELOG.md).

```bash
git clone --branch 0.1.0 git@github.com:mathiasgrimm/solo-orchestrate.git ~/.claude/skills/solo-orchestrate
```

## Update

```bash
cd ~/.claude/skills/solo-orchestrate
git fetch --tags
git checkout 0.2.0   # the version you want, from CHANGELOG.md
```

Start a new Claude Code session afterwards to be sure it loads the new version.

If you customized `SKILL.md`, keep your edits on a local branch
(`git switch -c my-setup`, commit) and rebase it onto the new tag instead of
checking the tag out directly.

## Customize

Edit the tables under **Defaults** in `SKILL.md` to match the agents and models
you have, on your own local branch (see Update). If your own instruction files
forbid overriding configured models, the skill follows them and skips its
model picks.

## Files

| File | Purpose |
| --- | --- |
| `SKILL.md` | The workflow: defaults, tiers, setup, planning, implementation, review loop, watching agents, permissions |
| `references/worker-brief.md` | Fill-in brief for implementation workers |
| `references/review-prompt.md` | PR review and planning consult templates, with the read-only reviewer rules |
| `references/solo-pitfalls.md` | Measured traps when driving agents through Solo |
