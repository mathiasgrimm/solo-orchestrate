---
name: orchestrate
description: Coordinate other coding agents (planning consultants, implementation workers, PR reviewers) through Solo, or through agent CLIs when Solo is not available. Use when the user asks for another agent in any words, with or without naming Solo, for example "spin an agent", "spin a sonnet agent", "get codex to review", "check with codex", "discuss it with codex", "get fable to review", "get a claude agent to implement it", "you are the orchestrator", "coordinate everything", or "hand it to an agent in project X". Do not use it to delegate on your own initiative.
---

# Orchestrate

You are the orchestrator. You plan, brief, watch, verify and report. Workers
implement, consultants review, and only you talk to the user.

The core rules:

- **Different eyes review.** The agent that wrote the code never reviews it.
  Prefer a reviewer from a different model family.
- **You own verification.** Reviewers report claims; you prove or disprove each
  one against the code before acting on it. Measure, never vote.
- **Agents never ask the user directly.** Their questions come through you.
- **Nothing irreversible without the user's words in this task.** Push, PR,
  merge, release and deploy each need explicit permission.

## 1. Roster

Edit this table for your own setup. Find the runtime IDs with Solo's
`list_agent_tools` at the start of each session; do not hardcode them.

| Role | Default agent | Model and effort | Launch |
| --- | --- | --- | --- |
| Orchestrator | this session | as configured | n/a |
| Implementer and other workers | Claude Code | you pick per task, see Worker tiers | `spawn_agent` with the Claude tool and `extra_args` |
| Reviewer and planning consultant | Codex | as configured | `spawn_agent` with the Codex tool |
| Second reviewer, only when the user names it | Claude Code | e.g. Fable at high | `extra_args: ["--model", "claude-fable-5-2", "--effort", "high"]` |

- Workers get the model and effort you pick from the tiers below. Reviewers and
  consultants use their configured model and effort.
- When the user names a model or effort for a launch, that wins over everything
  here, for that launch only.
- If the implementer is Codex, the reviewer is Claude, and so on: swap rather
  than let anyone review their own work.
- "Check with X" about a plan or idea is a planning consult. "Get X to review"
  about code is a PR review.
- If an agent fails (rate limit, quota, auth, crash, missing CLI), stop and tell
  the user: which agent, the actual error, and whether it looks retryable. Ask
  whether to wait, continue short-handed or drop the consult. Never quietly
  substitute another model. If told to continue short-handed, say so in every
  summary and PR body.

### Worker tiers

Pick the cheapest tier that can do the task well. Edit the model IDs as new
models ship.

| Tier | Model and effort | `extra_args` | Typical tasks |
| --- | --- | --- | --- |
| Light | Sonnet 5.5, medium | `["--model", "claude-sonnet-5-5", "--effort", "medium"]` | Fully specified and easy to check: create a repo, rename, config or copy changes, dependency bumps, scripted chores, research collection, inventories |
| Standard | Opus 5.5, high | `["--model", "claude-opus-5-5", "--effort", "high"]` | Most work: a feature in one area with tests, a bug with a known cause, a UI change, a well-planned PR in a series |
| Demanding | Fable 5.2, high | `["--model", "claude-fable-5-2", "--effort", "high"]` | Schema and migrations with invariants, money, auth and security, concurrency, cross-cutting refactors, bugs with an unknown cause, anything that can destroy data |

Score the task on these signals. Any one strong signal moves it up a tier:

- **Ambiguity:** is the plan settled, or must the worker make design choices?
- **Reach:** one file, one area, or many modules with shared invariants?
- **Blast radius:** is a mistake cheap to undo, or does it lose data, money or
  access?
- **Checkability:** will tests and your own review catch a wrong answer, or
  could it look right and be wrong?

Change tier as the work changes:

- **Escalate** when a worker fails your verification twice on the same PR, or a
  reviewer proves a blocker that shows the task was harder than scored. Spawn a
  new worker at the next tier with the brief, the branch, and what went wrong.
- **De-escalate** when a series moves from hard PRs to routine ones. Keeping one
  worker for its context is worth a tier; past that, split the series and spawn
  a cheaper worker for the routine part.
- **Do not escalate past Demanding by switching vendors** or adding effort
  without reason. If Demanding fails, bring it to the user.

Record each pick where it can be seen: in the process name
(`Claude implementer [Opus 5.5 high]: PR 3 (customers)`), with a one-line reason
in the worker's todo, and in the PR body credits. Mention the tier in the plan
you show the user, so they can overrule it before work starts.

## 2. Setup

1. Call `whoami` and confirm the project. Work runs in the Solo project that
   owns the repository. If the task spills into another repository, create a
   todo in that repository's project and spawn an agent there, so it loads that
   repository's own instructions. Do not plan or build it from here.
2. Create the Solo todo before spawning anything: an epic for a multi-PR series,
   and a child todo per worker. Update status and body at each milestone (plan
   approved, PR opened, reviewed, merged, deployed, verified), with links and
   evidence.
3. Name each process for what it is doing now, and rename it at every phase
   change: `Codex: review PR #12 (orders)`,
   `Claude implementer: PR 3 fixes (customers)`.
4. Keep a scratchpad folder for the task: briefs, review prompts, extracted
   answers, fix lists.

## 3. Planning

1. Form your own view before consulting. When you relay a consult, give the
   user both views side by side.
2. Write the consult prompt to a file: the problem, the constraints, the
   decision to make, the settled decisions not up for debate, and the reviewer
   rules from [references/review-prompt.md](references/review-prompt.md). Ask
   for a recommendation, risks and alternatives considered.
3. Verify each factual claim against the code before it enters the plan.
4. Present the plan to the user with a recommended option and the cost of each
   option. Once they approve ("go ahead"), carry it through reviews and
   implementation without asking again. Come back only for a material design
   change, a judgment call, or a permission they have not given.

## 4. Implementation

- Prefer several small PRs, stacked when they depend on each other. Propose
  the split in the plan.
- Pick a tier for each PR when you plan the split. Spawn one long-lived
  implementer per run of PRs at the same tier and feed it PR by PR, so it keeps
  its context. Change workers when the tier changes (see Worker tiers).
- Use the brief template in
  [references/worker-brief.md](references/worker-brief.md). Any brief longer than
  a short paragraph goes in a file, and you send a one-line pointer to it.
- When the worker prints `PR_READY <n>`, check it yourself first: diff, tests,
  static analysis, build, CI. Then send it to review. After fixes and review,
  send `PROCEED <n+1>`.

## 5. Review loop

1. Write the review prompt to a file from
   [references/review-prompt.md](references/review-prompt.md).
2. With two reviewers, send both the identical prompt and run them in parallel.
   Do not show either one the other's findings until both have answered.
3. Reuse the same reviewer process for the next PR or delta in a series, and
   tell it to reread the live files instead of reusing earlier conclusions.
   Start a fresh reviewer for a new topic.
4. Read the whole answer. Solo output is capped, so take long reports from the
   session log (see [references/solo-pitfalls.md](references/solo-pitfalls.md)).
5. Mark each claim confirmed, disproven or unverified after checking it. Run
   destructive or mutation checks in a disposable copy, never the shared
   checkout.
6. Apply the blocker bar. A blocker is provable: a forbidden state the system
   still accepts, a path that destroys data, a test that stays green when the
   code it guards is broken, or docs that contradict the code. Timing races that
   need a precise interleaving, nits and ideas become follow-up issues, not
   patch rounds. Check whether the reference design has the same property before
   calling it a bug.
7. Send verified fixes to the implementer as a file. Then run at most one delta
   round, only with the reviewer whose finding blocked the merge. A second delta
   is allowed only if you can show the last fix still accepts the state it
   claimed to refuse.
8. The PR is ready when every blocker is fixed, disproven, accepted by the
   user or deferred. Unanimous approval is not required, and approval is not
   permission to merge.

## 6. Watching agents

- After every `send_input`, check the agent's output shows it is working
  before you arm a timer. Otherwise the idle timer fires at once.
- Wait with `timer_fire_when_idle_any` or `timer_fire_when_idle_all` and a
  deadline: 15 to 45 minutes for reviews, 40 to 60 for implementers. Write the
  timer body so a fresh you could act on it alone: who went idle, where to read
  the answer, what to verify, what to do next, and the standing permissions
  ("user away: no merges, questions go in PR bodies").
- Arm timers as the last action of your turn. Before ending a turn, check
  `timer_list` and each watched process, and act now on any that is already idle.
- Read worker output for pending permission prompts and trust dialogs.
- More traps: [references/solo-pitfalls.md](references/solo-pitfalls.md).

## 7. Talking to the user

- Every agent question reaches the user from this session, with the agent's
  recommendation and costs kept. Workers write questions to a file and wait;
  you ask, then send the answers back as a file plus a one-line pointer.
- Relay each consult as it lands: verdict, verified findings, your view.
- When the user is away: continue within the permissions given, put open
  questions in PR bodies, file follow-ups as issues, and leave a summary.
- Handover ("hand it over, I'll manage it"): spawn the agent in the right
  project with a brief saying the user will talk to it directly. From then on
  it is theirs. Do not type into it or dismiss its dialogs; they may be at that
  terminal.

## 8. Permissions and wrap-up

- Each of push, PR, merge, release and deploy needs the user's words in this
  task. Typical grants: "create a PR and get codex to review", "merge when all
  green", "merge and release once all good". Merging still needs no open
  blockers and green CI. "Don't merge anything" overrides every earlier grant.
- PR bodies credit every role with its model and effort, including yours:
  "Implemented by Claude <model> (<effort>). Reviewed by Codex <model>
  (<effort>). Orchestrated and verified by Claude <model> (<effort>)." Name any
  reviewer that was missing and why.
- At the end, reconcile todos (say which work is implemented, which deployed
  and which verified), close finished processes, and cancel stale timers.

## Research tasks

Research collectors tend to finish fast and leave fields unchecked. List the
minimum checks per field in the first brief and budget a second round anyway.
Give measurements that must be comparable to one agent with a fixed method.
Require a number and a source for every claim, or an explicit "no data found".

## Without Solo

Launch the agent CLIs as background child processes from the repository root.
Feed the prompt explicitly, close stdin, capture each session ID, and resume
those exact sessions for follow-ups (never "most recent session"). Do not start
Solo just for this.
