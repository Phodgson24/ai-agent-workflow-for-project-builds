# Handoff: Agent Flow Sketchpad

*Written 24 Sep 2026 at the end of the first build session. Give this whole file to the new session.*

---

## 1. What this is (30-second version)

**Agent Flow Sketchpad** is a single-page interactive canvas, published as a claude.ai Artifact. It's for sketching AI-agent / orchestration workflows: drag boxes onto a canvas, resize them, link them, add names, rules (IF / AND / THEN), comments and tags, nest groups, and drill into sub-flows.

- **Live page:** https://claude.ai/artifact/FAht8ymX9NECxeoieiT26w
  (the same artifact also appears as `https://claude.ai/code/artifact/72bad453-aa76-4095-86cf-baa9ca4c107c` in comment notifications)
- **Current version:** 21
- **Runtime capabilities declared:** `db` (saved setups) and `comments` with `customAnchors: true` (comment pins follow boxes, Ctrl-click multi-select).
- **Runtime contract:** 0.2.56 (0.2.57 is available; don't upgrade unless the user asks).
- **Icon:** `workflow`

The user mainly uses it to design an **orchestrator → worker agent** system for coding with **Codex** (in the ChatGPT desktop app).

---

## 2. About the user and how to work with them

- **They have ADHD.** Keep answers clear, structured and scannable: headings, short bullets, small tables, and ASCII sketches of flows. Say plainly what changed and how to see it.
- **Minimal visuals.** They dislike busy diagrams. Don't add boxes (memory, tools, human, output…) unless asked. Detail belongs inside sub-flows.
- **When they say "review first" or "give me feedback before…", don't build.** Explain your understanding and give options, marking one as recommended. Build only after they confirm. When they say "add / edit / do it", build it.
- **"Research from official sources" means official only.** Use OpenAI docs (developers.openai.com, learn.chatgpt.com) and cite them. Don't present blog numbers as fact; earlier I had to walk back zone percentages I'd taken from a blog.
- **Never overwrite their original setups.** Create new setups unless they ask you to edit a specific one. When editing a setup, re-read it first and write with `if_version` (see section 5).
- **Test before claiming, and report honestly.** You can test locally (section 8). You **cannot** sign in to claude.ai in the built-in browser, so say that live-page behaviour, especially comment mode, is untested.
- **They often interrupt and re-send a corrected message.** If a message looks cut off, ask before acting.
- **They send artifact comments to Claude.** Handle them as described in section 9.

---

## 3. Getting the source (important)

The page source was built in this session's temporary scratch folder, which is **deleted when that session is deleted**. Don't rely on it. Get the latest source from the artifact itself:

1. `Artifact` → `action: "read"`, `url: https://claude.ai/artifact/FAht8ymX9NECxeoieiT26w`. The result says you're a *writer* and names the saved file holding the full page.
2. Edit that file, then republish with `Artifact` → `url` set to the same URL, so it updates **in place**, and a short `label`. Omit `capabilities` so the stored declaration (db + comments/customAnchors) carries forward. Omit `icon`.
3. The page is one self-contained HTML file with no external libraries except Google Fonts (IBM Plex Sans and Plex Mono).

The `AGENTS-worker-naming.md` file I gave the user lived in the same scratch folder. Its full content is in **Appendix A** of this document.

---

## 4. How the page is built (map of the code)

One `<style>`, the markup, then one `<script>` (an IIFE). Main areas, roughly in file order:

| Area | What it does |
|---|---|
| **Theme tokens** | Light palette on `:root`, dark palette via `prefers-color-scheme` and `[data-theme=dark]`. Type colours are `--t-agent`, `--t-tool`, `--t-decision`, `--t-memory`, `--t-folder`, `--t-human`, `--t-input`, `--t-output`, `--t-note`, `--t-group`. |
| **Layout** | CSS grid `.app` with header, left palette (`#palette`), canvas (`main#vp`) and right details panel (`#rightPanel` › `#insp`). Panel widths come from `--lw` / `--rw`, and each area is pinned to its own column. |
| **TYPES / PROVIDERS** | Box types: input, agent, tool, decision, memory, folder, human, output, note, group. **Agents** carry provider (Claude/Codex), model and effort. Model lists live in `PROVIDERS`. |
| **State and normalize** | `state = {name, notes, setupId, setupSavedAt, spread, resetPoint?, nodes, edges, rules}`. `normalize()` / `normNodes` / `normEdges` / `normSub` validate everything loaded (JSON import, setups, localStorage). |
| **Levels / sub-flows** | `path` (the ids of the sub-flow groups you're inside) and `C` (the current level's canvas, i.e. `state` or `group.sub`), set up by `resolvePath()`. A group with `.sub = {nodes, edges, pins, spread, resetPoint?}` is a **sub-flow**; a group without `.sub` is a see-through **frame**. |
| **Entry/exit points ("pins")** | Inside a sub-flow, each overview link touching the sub-flow box appears as a pill with id `pin:<edgeId>`. Positions are stored in `sub.pins[edgeId]`; `pinNodes()` builds them. Inner edges can start or end at `pin:*`. |
| **Frame nesting** | Worked out from geometry, never stored. A box belongs to the smallest frame, larger than itself, that contains its centre (`parentOf`, `children`, `descendants`). |
| **Canvas rules engine** | `state.rules`: IF field/op/value, joined by AND or OR, THEN a flag text in a colour. `computeFlags()` walks every level. Sub-flow boxes show "N flagged inside". |
| **Rendering** | `renderCanvas()` draws frames into `#groups`, boxes and pins into `#nodes`, links as SVG in `#eg`, labels into `#elabels`, and selected-link end handles into `#hg`. `applyView()` handles pan and zoom (`view = {x, y, z}`). |
| **Link geometry** | `portPt` / `sideFor` / `layoutEnds` (spreads several ends along one side) / `geom(p, q, bend)` (cubic curves; handles link spread, fan, arcs and bend). |
| **Link routing** | `computeRoutes()` tries sides × bends per link to minimise crossings (×100), then box hits (×150), then length. It uses greedy passes from two starts, is cached per layout key, and is frozen while a box is dragged. Crossings get a `.halo` gap. Hidden comparison switch: `localStorage['agent-flow-sketchpad-v1-routing'] = 'off'`. |
| **Labels** | `declutterLabels()` slides each label along its own curve to a free spot, falling back to stepping off the line with a dotted leader. |
| **Focus** | Selecting or hovering (after a ~350 ms delay) fades everything that isn't connected (`applyFocus`). |
| **Inspector** | `nodePanel` / `pinPanel` / `edgePanel` / `canvasPanel`, driven by `data-bind` / `data-act` attributes. |
| **Sub-flow actions** | `toSubflow(g)` (frame → sub-flow; rewires crossing links through pins), `toFrame(g)` (the reverse), `enterSub`, `goToLevel`, `goUp`. |
| **Sub-flow opening modes** | Menu `#subMode`: "click to open" (double-click, or the button) and "zoom to open" (a miniature fades in; zooming far enough goes inside). Zooming out never leaves a sub-flow; use ↑ Up, the breadcrumb, or Backspace. |
| **Detail modes** | Menu `#detail`: full / compact. Compact hides descriptions and tags and folds link conditions into "IF ×n". |
| **Spacing pad** | `#spacing` d-pad: taller / shorter / wider / narrower work on **row or column gaps** (`planSpacing` bands, `STEP = 24`, `MIN_GAP = 32`). On a one-row or one-column level it adjusts **link spread** instead (`C.spread`: connection points, fan, arcs; range −3 to 6). ↺ resets; the bookmark saves `C.resetPoint`; the chevron collapses the pad into the bottom bar. Alt+arrows do the same. |
| **Side panels** | Drag dividers `#splitL` / `#splitR` to resize (arrow keys work too; double-click for the default size). « / » hide a panel and edge tabs bring it back. Stored in localStorage `…-panels`. |
| **Setups (db)** | Collection `setups`. `writeSetup`, `quickSave` (Ctrl+S) and the Setups window. Falls back to browser storage when db isn't available. **Newer-version bar** (`#newer`): if the open setup was saved elsewhere, it offers "Load latest" or "Keep mine", and Save is blocked until the user chooses. |
| **Comments** | `claude.use('comments').customAnchors(...)`. Comment mode: click = comment on one item; Ctrl/⌘/Shift-click = multi-select, then "Comment on these" (or Enter). Anchor names look like `Name + Name · in Level [levelTag|id,~edgeId]`, max 128 bytes. `placePins()` reports pin positions after every render and view change. |
| **Other** | Undo/redo (JSON snapshots, 150 deep), a rescue copy if the saved canvas fails to load (`…-rescue`), Examples menu (minimal, triage), JSON import/export, tips button. |

**Browser-storage keys** (per viewer): `agent-flow-sketchpad-v1` (working canvas), `-rescue`, `-submode`, `-detail`, `-spacing-collapsed`, `-panels`, `-routing` (debug only), and `agent-flow-setups-v1` (local fallback).

---

## 5. Saved setups (artifact database, collection `setups`)

| Doc id | Name | Notes |
|---|---|---|
| `rz9azhghfmjxsh` | user agent + project folder | **The user's original.** Don't touch. |
| `improvedrelay01` | … (improved) | First cleanup, flat Worker Thread frame. |
| `subflowrelay01` | … (sub-flow) | First sub-flow version. |
| `namedworkers01` | … (named workers) | **The main, active one, version 8.** Both the user and Claude edit it. |

**To edit a setup safely:**
1. `ArtifactData get` it, using `out_dir` inside the working folder (not the scratchpad; that path is refused). Note the version.
2. Change the JSON with a small node script.
3. `ArtifactData set` with `file_path` and **`if_version`**.
4. Tell the user to reload and click **Load latest** on the bar. Their open canvas is a browser copy and won't update by itself.

### Current design in `namedworkers01` (what the user is building)

**Overview:** three boxes plus links.
- **`00 - Orchestrator`** (Codex · Astra · high, pinned). Plans from `milestones.md`, names and starts every worker, reviews results, keeps `progress.md`. It has 6 rules: accept, fix to the same worker, fresh worker after a handoff, fresh worker after a compacted worker finishes, new milestone M+1 H00, tell the user the name and archive the old chat.
- **Worker Thread** (sub-flow).
- **my-app** (folder: AGENTS.md, milestones.md, progress.md, handoffs/, .codex/config.toml).
- **Links:** task / fix instructions; result + handoff doc; start fresh worker (IF handover OR compacted worker finished OR milestone accepted); reads + edits; reads to review.

**Worker Thread** (inside):
- Current worker → **Context check (after each step)**, which is decided by the worker agent, with context measured by Codex:
  - room left → keep working
  - getting full (not yet compacted this task) → **Compact the chat (at a safe point)** → continue the same task, marked compacted
  - full again after compacting, or quality slipping → **Handoff procedure** (sub-flow)
  - task finished → send result, adding "compacted: replace me" if it compacted
- The result / handoff-doc exit point is linked, via the orchestrator, to the **start fresh worker** entry point, then to Fresh worker (reads the handoff doc first) → takes over as current worker.

**Handoff procedure** (inside): At a safe checkpoint? → yes: Commit work in progress `[M01_H00]` → Write handoff doc `handoffs/M01_H00-ui-improvement.md` → send. No: a note "keep working to a safe point, then check again".

**Naming scheme** (agreed): `M01_H00 - ui improvement`. M = milestone, H = handover. New milestone → M+1, H00. Replacement mid-milestone, or after a compacted task → same M, H+1. The same ID appears in the chat title, commits `[M01_H01]`, handoff doc name and progress log. The orchestrator chat is `00 - Orchestrator`, pinned.

**Compaction design** (from official OpenAI sources):
- Compaction is normal; OpenAI calls it "a default long-run primitive, not an emergency fallback".
- One deliberate compaction per task, at a safe point, after writing a checkpoint line in progress.md.
- A second one means hand off.
- A worker that compacted retires when its task is done.
- Codex measures the context (automatic compaction; `/status` shows what's left).
- `compact_prompt` makes each summary start with `COMPACTED: n`, so the worker knows it has been compacted.

---

## 6. Official sources used (for future research)

- Codex docs index: https://learn.chatgpt.com/llms.txt (append `.md` to any page for plain text)
- Long-running work / Goal mode: https://learn.chatgpt.com/docs/long-running-work
- Slash commands (`/compact`, `/status`, `/new`, `/fork`): https://learn.chatgpt.com/docs/developer-commands
- Config reference (`model_auto_compact_token_limit`, `compact_prompt`, `tool_output_token_limit`, `project_doc_max_bytes`): https://learn.chatgpt.com/docs/config-file/config-reference
- AGENTS.md (loaded once per chat, 32 KiB default cap): https://learn.chatgpt.com/docs/agent-configuration/agents-md
- Subagents (built-in `worker` and `explorer` roles, custom agents): https://learn.chatgpt.com/docs/agent-configuration/subagents
- Codex models (Astra, GPT-6 Sol, GPT-6 Luna…): https://developers.openai.com/codex/models
- Compaction (API): https://developers.openai.com/api/docs/guides/compaction
- Prompt caching (up to 90% off repeated prefixes): https://developers.openai.com/api/docs/guides/prompt-caching
- OpenAI long-running agents tips: https://developers.openai.com/blog/skills-shell-tips

---

## 7. Known limits and ideas not yet built

- **Link routing** prefers avoiding boxes over avoiding crossings (box ×150 vs crossing ×100). On very dense graphs some crossings remain; they get a halo gap. If the user wants crossings minimised first, swap the weights. The router uses side midpoints while the final drawing spreads ends, so a crossing near a shared side can occasionally slip through.
- **Sub-flow miniatures** (zoom-to-open) draw inner links as straight lines, without routing.
- **Alt+← / Alt+→** are also the browser's Back/Forward. The page blocks them on the canvas; if that ever misfires, change the spacing shortcut.
- **Comment mode (custom anchors) has never been exercised on claude.ai.** The user's comment arrived correctly anchored (`[slot|pin:result,…]`), so the multi-select works, but pin placement and the move/reveal behaviours are unverified.
- **Offered but not done:** giving the my-app files (AGENTS.md, milestones.md…) their own boxes inside a my-app sub-flow; "Taller on a single row" beyond link spread; making zoom-to-open the default for everyone.

---

## 8. Testing locally (what worked)

- `.claude/launch.json` in the working folder runs `python -m http.server 8765 --bind 127.0.0.1` (name `sketchpad`). Start it with `preview_start name:"sketchpad"`, open `http://localhost:8765/agent-flow-sketchpad.html`, and use `resize_window` 1400×860. Reset it to desktop afterwards and `preview_stop`.
- The published page gets a wrapper that adds `<meta charset>` and a `[hidden]{display:none!important}` reset. The file includes both itself, so local rendering matches.
- **To load a setup locally:** fetch the JSON from the served folder, then `#bJson` click → set `#mText` → `#mLoad` click.
- **Synthetic-event gotchas:**
  - Dispatch `keydown` on `#vp`, not `window`: the handler calls `e.target.matches`.
  - Re-query elements between two synthetic `pointerdown`s: the render replaces the DOM.
  - `setPointerCapture` throws for synthetic pointers; wrap it in try/catch.
- **Editing gotcha:** the Edit tool turned `\uXXXX` escapes into real invisible characters and broke a regex. Write lines containing escapes with a node script using `String.raw`, then scan the file for invisible characters.
- Syntax check: extract the `<script>` block with sed and run `node --check`.
- The in-app browser can't sign in to claude.ai, so live-page checks aren't possible. Say so.

---

## 9. Artifact comments

The user leaves comments on the page (often multi-selected items) and sends them to Claude. For each one:

1. Read the thread (`ArtifactComments read`). The anchor text names the items and level, e.g. `[slot|pin:result,~hodoc]`, where `~` marks a link id.
2. Re-read the setup from the db before changing anything.
3. Make the change, then **reply in the thread** with what you found, what changed and how to see it. Then **resolve** it.
4. In the chat itself, write just one short line.

---

## Appendix A: `AGENTS-worker-naming.md` (as last sent to the user)

```markdown
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
```

Optional `.codex/config.toml`:

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

`milestones.md` template:

```markdown
# Milestones - Data workflow

M01 - ui improvement
M02 - data import
M03 - (next milestone)
```

---

## Suggested first message for the new session

> Read HANDOFF-agent-flow-sketchpad.md. Then read the artifact at https://claude.ai/artifact/FAht8ymX9NECxeoieiT26w to get the current source, and the `namedworkers01` setup from its database. Confirm you're ready and wait for my next request.
