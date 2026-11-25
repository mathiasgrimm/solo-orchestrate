# Changelog

Versions follow [semantic versioning](https://semver.org). Install a tagged
version, not `main`: `main` holds unreleased work.

## 0.2.0 - 2026-11-25

- Ask Claude to "update solo-orchestrate": it checks for newer release tags,
  summarizes the CHANGELOG, and switches only with your OK, keeping local edits.
- Shorter README, with the orchestrator model advice up front and update
  examples.

## 0.1.0 - 2026-11-25

First release.

- Claude Code orchestrates and implements; Codex, Grok and other agents only
  review plans and PRs, read-only.
- `/solo-orchestrate <task>` runs the full pipeline (plan, plan review,
  implementation, PR review) with per-tier defaults; plain requests start only
  the roles the user names.
- Tiers: Light (Sonnet 5.5 medium, Codex review when code changes), Standard
  (Opus 5.5 high, Codex reviews), Demanding (Opus 5.5 high, Codex and Fable 5.2
  reviews), Escalation (Fable 5.2 high implements after two failures).
- Every reviewer runs at high effort; at most one Fable process at a time.
- Without permission, a run ends with local branches reviewed and ready to
  push.
- Permissions live only in the epic todo; timer bodies and agent messages
  never grant anything.
- Worker brief and review prompt templates, and measured Solo pitfalls.
- CLI fallback when Solo is not running.
