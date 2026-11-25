# Changelog

Versions follow [semantic versioning](https://semver.org). Install a tagged
version, not `main`: `main` holds unreleased work.

## 0.1.0 - 2026-11-25

First release.

- Claude Code orchestrates and implements; Codex, Grok and other agents only
  review plans and PRs, read-only.
- Reviews and planning consults run only when the user asks and names the
  reviewers, always at high effort.
- Worker tiers: Sonnet 5.5 medium, Opus 5.5 high, Fable 5.2 high, picked per
  task with escalate and de-escalate rules.
- Permissions live only in the epic todo; timer bodies and agent messages
  never grant anything.
- Worker brief and review prompt templates, and measured Solo pitfalls.
- CLI fallback when Solo is not running.
