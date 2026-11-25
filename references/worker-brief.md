# Worker brief template

Write this to a file and send the worker: "Read <path> and do exactly what it
says." Fill every `<...>`; delete sections that do not apply.

```markdown
You are a worker spawned by Solo process <orchestrator id> (project <project id>),
running as <model> at <effort> effort (<tier> tier: <one-line reason>).
Your Solo todo is <todo id> (parent <epic id>). Keep it current: status, body,
and comments with evidence. Rename your own Solo process at each phase.
Report progress in your own output. Never send messages to process <orchestrator id>.

## Goal
<what to build or find out, and why>

## Branch
Work on `<branch>`, based on `<base branch>` at `<sha>`.

## Settled decisions (do not reopen)
- <decision>

## Read first
- <files, docs, todos, earlier plan>

## Out of scope
- <things to leave alone>

## Before you say READY
- <tests, static analysis, build commands that must pass>

## Hard rules
- Do not ask the user anything. Write questions to <dir>/QUESTIONS.md, print
  `QUESTIONS READY: <path>`, and wait. For a plan, write it and print
  `PLAN READY: <path>`, then wait.
- Do not spawn agents or subagents, and do not run review workflows.
- <Do not push, open PRs or merge. | You may push and open the PR; never merge.>
- Stage explicit paths only. Never `git add -A`, `git reset --hard`,
  `git checkout .`, `git clean` or `git stash`: other agents share this tree.
- No `rm` with globs or variables. Make temp dirs with `mktemp -d` outside the
  repository and delete by explicit absolute path. Chain dependent commands
  with `&&`.
- Read-only on anything production: <databases, buckets, servers>. Never print
  secrets.
- <repository conventions: test framework, style rules>

## Done markers (print exactly one, then stop and wait)
- `READY branch <branch> <sha>` when work is committed locally.
- `READY PR #<n> <sha>` when you pushed and opened the PR (only if allowed above).
- `UPDATED <sha>` after applying review fixes.
- `REPORT READY: <path>` for research tasks.
The orchestrator replies with `PROCEED <next step>` or a fix file.
```
