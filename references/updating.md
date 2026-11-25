# Updating this skill

Use this when the user asks to update solo-orchestrate, or asks whether a new
version exists. It updates the user's installed copy to a newer release tag.
It never edits the skill's files by hand and never pushes anything.

## 1. Find the installed copy

- The install folder is the one holding this skill's `SKILL.md`, usually
  `~/.claude/skills/solo-orchestrate`. Resolve links with `realpath`.
- Check it is a git checkout of this skill: `git -C <dir> remote get-url origin`.
  If it is not a git checkout (copied files), stop and give the user the
  install command from the README instead.

## 2. Compare versions

```bash
cd <dir>
git fetch --tags origin
git describe --tags --exact-match 2>/dev/null || git describe --tags   # current
git tag --sort=-v:refname | head -5                                    # newest first
```

- Release tags are plain semantic versions (`0.1.0`, `0.2.0`). Ignore anything
  else, and never move to `main`: it holds unreleased work.
- If the current version is the newest, say so and stop.

## 3. Show what changes

- Read the newer CHANGELOG without switching: `git show <newest>:CHANGELOG.md`.
- Summarize every entry between the current version and the newest.
- Flag breaking releases: a new major version, or a new minor version while
  still on `0.x`.
- Ask the user whether to update, and to which version. Changing versions
  changes how the skill behaves, so do not switch without their answer.

## 4. Switch without losing their edits

Check for local changes first: `git status --porcelain` and `git branch
--show-current`.

- **Clean, on a tag (detached HEAD):** `git checkout <version>`.
- **On their own branch with committed edits:** `git rebase <version>`. If it
  conflicts, run `git rebase --abort`, show the conflicting files, and ask the
  user how to resolve them.
- **Uncommitted edits:** stop. Ask the user to let you commit them on a local
  branch (`git switch -c my-setup`, then commit) before updating. Never discard
  their edits: no `git reset --hard`, `git checkout .`, `git clean` or
  `git stash`.

## 5. Confirm

- `git describe --tags` shows the new version.
- Tell the user the running session already uses the new version (Claude
  Code reads the skill again when it is invoked), and repeat the one-line
  summary of what changed.
