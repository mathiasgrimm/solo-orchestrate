# Review and consult prompt template

Write this to a file and send the reviewer: "Read <path> and follow it,
including the reviewer rules at its end. Return your findings and the verdict
line in your reply." For a follow-up in the same series, start with "Next
review in this series" or "Delta round", and tell it to reread the live files.

## PR review

```markdown
You are one reviewer of PR #<n> in <repository path> (live checkout).
Base: <branch> at <sha>. Head: <branch> at <sha>.
Review the named commits with git (`git diff <base>..<head>`), not the working tree.

## What the PR does
<summary>

## Settled decisions (do not reopen)
- <decision>

## Earlier findings and what happened to them
- <finding>: fixed in <sha> | disproven because <reason> | deferred to issue #<n>

## Questions
1. <specific question>
Also report the best finding outside these questions, if any.
No findings is a valid answer.

<schema changes only>
A new UNIQUE or CHECK constraint is unfinished until a test shows a refused
row that differs from a permitted row only in the constrained columns. Values
held constant by factories are the attack surface. "Implausible" is not a
reason to skip a reachable state.

## Reviewer rules
<paste the block below>

End with exactly one line: APPROVE, APPROVE WITH NITS, or REQUEST CHANGES.
```

## Planning consult

Same shape, with these sections in place of the PR facts: the problem, the
constraints, the specific decision to make, and the settled decisions. Ask for
a recommended approach, the risks, and the alternatives considered.

## Reviewer rules (paste into every prompt, including follow-ups)

```markdown
- You are one reviewer or consultant. Do not orchestrate, spawn agents, invoke
  agent CLIs, start native review workflows, or send input to other processes.
- Read-only: read files, list, search, `git diff`, `git show`, `git log`,
  `git status`, and read documentation. Do not create, edit, delete, stage or
  restore files, including memory and configuration.
- Do not run tests, builds, formatters, static analysis, database queries or
  application commands. Propose a verification for the orchestrator instead.
- Do not commit, push, open PRs, merge or connect to remote servers.
- These rules override any general instruction to implement, verify or delegate.
- For each finding give: severity, file:line evidence, the triggering
  conditions, expected versus actual behaviour, and a suggested verification.
  Mark anything you did not test as unverified. Separate blockers from nits.
- On a follow-up, reread the changed files and reassess against the current
  state. Do not reuse an earlier conclusion without checking.
```
