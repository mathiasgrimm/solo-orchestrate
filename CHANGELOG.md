# Changelog

Versions follow [semantic versioning](https://semver.org). Install a tagged
version, not `main`: `main` holds unreleased work.

## 0.3.2 - 2026-11-25

- Install by asking Claude Code with the repo URL; the hand command installs
  the newest release tag, so the README no longer names a version.
- Updating no longer asks for a new session: the running one picks up the new
  version.

## 0.3.1 - 2026-11-25

- Workflow diagrams show the Codex model like the Claude ones: Sol 6.2 as a
  plain ring, Astra 6.2 as a ring with a dot, labelled at each review gate.

## 0.3.0 - 2026-11-25

- Codex reviewer model by tier: Sol 6.2 high for Light and Standard, Astra 6.2
  high for Demanding and Escalation. A reviewer process is reused only on the
  same model.

## 0.2.3 - 2026-11-25

- README workflow diagrams: the pipeline for each tier under Defaults, and a
  "How a run works" section with a full example run and who does what.

## 0.2.2 - 2026-11-25

- README hero banner (rounded, with a stepped grid) and logo.
- README shows tier signals and rules as tables.
- Grok removed: Codex is the reviewer from another model family, and Fable
  the second reviewer.

## 0.2.1 - 2026-11-25

- README explains how the orchestrator picks a tier, with a link to the full
  rules in SKILL.md.

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
