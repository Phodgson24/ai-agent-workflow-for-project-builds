# AGENTS.md: worker naming section

Paste the section below into the project's `AGENTS.md` (Codex reads it once per chat).

---

## Worker naming and handoffs

Read this before starting, naming or replacing any worker.

### Names
- Orchestrator chat: `00 - Orchestrator`. It is pinned and never renamed.
- Worker chats: `M##_H## - milestone name`, for example `M01_H00 - ui improvement`.
  - `M##` = milestone number, two digits, taken from `milestones.md`.
  - `H##` = handover number within that milestone. The first worker on a milestone is `H00`.
  - The milestone name is copied exactly from `milestones.md`. Never invent or reword one.
  - Plain characters only: an underscore between `M##` and `H##`, then space, hyphen, space.

### When the orchestrator starts a worker
- Milestone accepted after review: the next worker is `M(+1)_H00 - <next milestone name>`.
- Worker replaced before its milestone is finished (context nearly full, safe checkpoint reached,
  handoff doc written): the next worker is `M(same)_H(+1) - <same milestone name>`.
- Fix instructions after a failed review go to the SAME worker. Its name does not change.
- After starting a worker, tell the user its exact name on one line, so they can rename the chat
  if the automatic rename does not take.
- Add one line to `progress.md`, e.g. `M01_H00 handed off at checkpoint -> M01_H01 started`.

### Everything a worker touches carries its ID
- Chat title: `M01_H01 - ui improvement`
- Commits: `[M01_H01] short description`
- Handoff doc: `handoffs/M01_H01-ui-improvement.md`, named after the worker that writes it
- Progress log (`progress.md`, kept by the orchestrator): one line per worker started, handed off or accepted

### Handoff doc
A worker writes it before it is replaced, after committing its work. Sections:
**Done** · **Next** · **Open issues** · **Files touched** · **How to verify**.
A fresh worker reads the most recent handoff doc for its milestone before doing anything else.

### Context: compact once, then retire after the task
- After each step, check two things: is the task done, and is there still room in the context?
  You judge "done" (definition of done, tests). Codex measures context: it compacts automatically
  near its limit, and a summary starting `COMPACTED: n` means that has happened n times.
- Room left: keep working.
- Getting full, and you have not compacted during this task: compact once, at a safe point.
  1. Finish the current step. Never compact in the middle of an edit.
  2. Add a checkpoint line to `progress.md`: done so far, what's next, files touched, key decisions.
  3. Run `/compact`, then carry on with the same task.
  If Codex compacts on its own, that counts as your one compaction.
- Getting full again after compacting, or you notice you're repeating work or losing earlier
  decisions: stop at the next safe checkpoint and hand off (commit, write the handoff doc).
- When a task you compacted during is finished, say so in your result: `compacted: replace me`.
  The orchestrator then starts a fresh worker for the next task (same M, H+1; or M+1, H00 if the
  milestone is done). A worker that never compacted may continue with the next task.

### Housekeeping
- Archive a worker's chat once its work is accepted or handed off.
  The sidebar should only show `00 - Orchestrator` and the one active worker.

---

# Optional `.codex/config.toml`

```toml
compact_prompt = """
Start the summary with one line: COMPACTED: n  (n = 1, or 1 more than any
COMPACTED line already in this chat). This tells the worker it has been compacted.
Summarize this chat so the same worker can continue the SAME task. Keep:
the worker ID (M##_H##) and milestone name; the task goal and definition of done;
what is finished and what remains; files changed; decisions made and why;
open issues and failing tests. Drop full file contents, long logs and tool output.
"""
```

# `milestones.md` template

```markdown
# Milestones - Data workflow

M01 - ui improvement
M02 - data import
M03 - (next milestone)
```
