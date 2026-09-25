# Notes for Claude

Read `app/docs/HANDOFF.md` first: it covers the user, the code map, setups, testing and comments. The points below **update or override** it.

## Repo layout
- `app/`: the HTML app and its docs. `workflows/`: setups exported from the app. Keep the two separate.
- The repo file `app/agent-flow-sketchpad.html` is the source of truth. It matches the live artifact as last published from this repo (version 27, auto-size boxes).

## Publishing
- Edit `app/agent-flow-sketchpad.html`, test it, commit it, then publish with `Artifact` using `url: https://claude.ai/artifact/FAht8ymX9NECxeoieiT26w` and `file_path` set to that file. Omit `capabilities` and `icon`.
- Before publishing, `Artifact read` the live page. If it changed since the last commit, merge first.

## Setups (app database)
- **Main setup:** `namedworkersv7`, "user agent + project folder (named workers) v7" (35 boxes · 5 links). v6 copy plus a worker check in the orchestrator's Context full: At a safe point? → Worker active? → (yes) Tell the worker: save and pause → Worker paused? → Commit; after compacting, Tell the worker to resume. The user's request ended at "and once…"; the resume step is Claude's completion, flagged to the user.
- v6: `namedworkersv6`, "user agent + project folder (named workers) v6" (31 boxes · 5 links). v5 copy where Fresh orchestrator and Handover check moved inside the Orchestrator Thread's "Context full: compact or hand over" (exits: `ocont` continue, `otake` O02 takes over). The Orchestrator Thread page is now just orchestrator → context check → Context full.
- v5: `namedworkersv5`, "user agent + project folder (named workers) v5" (31 boxes · 5 links). v4 copy plus a "Handover check" sub-flow on the Orchestrator Thread page (fresh orchestrator → check → takes over): OWNER line in progress.md, handover-only mode for the old orchestrator, 3-point check, max 2 fix rounds, then tell the user.
- v4: `namedworkersv4`, "user agent + project folder (named workers) v4" (23 boxes · 5 links). v3 copy where the Orchestrator box became an "Orchestrator Thread" sub-flow (id still `orch`, so all links keep working) with its own "Context full" sub-flow; the user starts fresh orchestrator chats (00 - Orchestrator O01, O02…).
- v3: `namedworkersv3`, "user agent + project folder (named workers) v3" (13 boxes · 5 links), kept unchanged. Built by Claude from v2 (`dhhsk0icnb92x7`): compaction moved into the handoff sub-flow, now "Context full: compact or hand off", with one shared safe-point check and commit.
- v2 (`dhhsk0icnb92x7`) is the user's own save and is kept unchanged.
- Never overwrite a setup without being asked. When editing one, pin the write with `if_version`.
- When re-exporting, write each setup's `canvas` field to `workflows/` (see `workflows/README.md`).

## App features added in the cloud
- **Match badges:** an optional `badge` colour on a link that touches a sub-flow box. Drawn as a lit strip along the box edge where the link meets the sub-flow box, and at the matching `pin:<link id>` point inside, whose pill takes the colour. The last 60 canvas units of the line and its arrowhead take the colour too. Strips live in `#badges`, a layer above the boxes, sized in canvas units (5×40) so they zoom with the box, like the line. Set in Details for that link or its entry/exit point. Code: `BADGES`, `badgePicker()`, `badgeOf`/`plug` in `renderEdges()`, `sizeStubs()`, `.bsock`/`.bstub`/`.pin.badged` CSS.
- **Auto-size:** `autoSize()` (called in `renderCanvas()` before `renderEdges()`) grows any box, sub-flow box or entry/exit pill until its text fits; it never shrinks below the stored size and skips while resizing. Descriptions are no longer line-clamped, sub-flow titles wrap, and `.nin > *{flex-shrink:0}` stops lines squashing so overflow is measurable. Pin heights are kept as `sub.pins[id].h`.
- Fixed: after double-clicking into a sub-flow, the canvas stayed faded (the hover focus pointed at a box on the level above). `applyFocus()` now ignores targets that aren't on the current level.

## Testing in the cloud
- The handoff's `preview_start` tools are desktop-only. Here, use Playwright with Chromium (preinstalled, `executablePath: '/opt/pw-browsers/chromium'` if needed) and a local `python3 -m http.server`.
- Syntax check: extract the `<script>` block and run `node --check`.
- Claude can't sign in to claude.ai, so comments and database sync can't be tested live. Say so.

## Artifact comments
- Thread `a16d17fa…` ("things get very dense") is still open. It was answered twice and later covered by the spacing pad. Leave it until the user decides.
