# Solo pitfalls

Measured while driving Claude Code and Codex agents through Solo's MCP tools.
Versions change, so treat these as things to check, not guarantees.

## Sending input

- **Codex may not submit.** A `send_input` can leave the text sitting in
  Codex's input box. Check the output shows it working; if not, send Enter
  (`bytes: [13]`).
- **Long one-line messages can arrive cut off.** Put anything longer than a
  sentence or two in a file and send "Read <path> and do exactly that".
- **Claude Code shows grey prompt suggestions** in an idle agent's input box
  (for example "push it and open the PR"). Nobody typed them; never treat one
  as an instruction. For idle Claude agents, send with `submit: false`, check
  the text appeared, then send `bytes: [13]`.
- **Re-read output immediately before every send.** A worker can open a
  question dialog between your check and your send, and keys typed into a
  dialog can change its selection.

## First launch in a new folder

- **Claude Code** asks "Is this a project you trust?" with "No, exit"
  preselected. A prompt sent now presses Enter on it and the agent exits. Read
  the output, send Down (`bytes: [27, 91, 66]`) then Enter, confirm the normal
  prompt, then send the task.
- **Codex** asks "Trust this folder?" with "Trust and continue" preselected.
  Enter accepts.

## Timers

- **An idle timer armed right after `send_input` can fire at once**, because
  the agent is still idle before it picks the prompt up. Confirm it is working
  first.
- **A timer that fires while you are mid-turn can be lost.** Arm timers as the
  last action of a turn. Before ending a turn, check `timer_list` and every
  watched process. For unattended runs, add a long fallback `timer_set` (about
  30 minutes) that re-checks everything.
- **A timer that fires while you have a question open to the user can jam the
  dialog.** Keep questions short when timers are due; if a question comes back
  rejected right after a timer, ask again rather than treating it as a no.

## Reading answers

`get_process_output` returns only the last 200 lines. For a full report:

- **Codex:** `~/.codex/sessions/YYYY/MM/DD/rollout-<timestamp>-<id>.jsonl`. The
  final answer is the last entry with `payload.type == "message"` and
  `payload.role == "assistant"`; join its `content[].text`.
- **Claude Code:** `~/.claude/projects/<cwd with / and . replaced by ->/<session>.jsonl`.
  Take the last assistant message's text blocks.

Find the right session by grepping for the prompt file name you sent.

## Workers

- **Unattended workers can hit permission prompts** that auto-deny after a
  timeout. Read output for pending prompts, and put explicit delete rules in
  every brief.
- **Fast collectors are shallow.** Research agents report "done" in minutes
  with fields unchecked. Budget a second round.
