# Notes for Claude

Read `app/docs/HANDOFF.md` first: it covers the user, the code map, setups, testing and comments. The points below **update or override** it.

## Repo layout
- `app/`: the HTML app and its docs. `workflows/`: setups exported from the app. Keep the two separate.
- The repo file `app/agent-flow-sketchpad.html` is the source of truth. It matches the live artifact as last published from this repo (version 24, port-plug badges).

## Publishing
- Edit `app/agent-flow-sketchpad.html`, test it, commit it, then publish with `Artifact` using `url: https://claude.ai/artifact/FAht8ymX9NECxeoieiT26w` and `file_path` set to that file. Omit `capabilities` and `icon`.
- Before publishing, `Artifact read` the live page. If it changed since the last commit, merge first.

## Setups (app database)
- **Main setup:** `namedworkersv3`, "user agent + project folder (named workers) v3" (13 boxes · 5 links). Built by Claude from v2 (`dhhsk0icnb92x7`): compaction moved into the handoff sub-flow, now "Context full: compact or hand off", with one shared safe-point check and commit.
- v2 (`dhhsk0icnb92x7`) is the user's own save and is kept unchanged.
- Never overwrite a setup without being asked. When editing one, pin the write with `if_version`.
- When re-exporting, write each setup's `canvas` field to `workflows/` (see `workflows/README.md`).

## App features added in the cloud
- **Match badges:** an optional `badge` colour on a link that touches a sub-flow box. Drawn as a "port plug" (coloured tab with a direction arrow, plus a lit strip along the box edge) where the link meets the sub-flow box, and at the matching `pin:<link id>` point inside, whose pill takes the colour. The last ~70px of the line and its arrowhead take the colour too. Plugs live in `#badges`, a layer above the boxes, and counter-scale with `--z` so they stay 22×16px at any zoom. Set in Details for that link or its entry/exit point. Code: `BADGES`, `badgePicker()`, `badgeOf`/`plug` in `renderEdges()`, `sizeStubs()`, `.bsock`/`.bstub`/`.pin.badged` CSS.
- Fixed: after double-clicking into a sub-flow, the canvas stayed faded (the hover focus pointed at a box on the level above). `applyFocus()` now ignores targets that aren't on the current level.

## Testing in the cloud
- The handoff's `preview_start` tools are desktop-only. Here, use Playwright with Chromium (preinstalled, `executablePath: '/opt/pw-browsers/chromium'` if needed) and a local `python3 -m http.server`.
- Syntax check: extract the `<script>` block and run `node --check`.
- Claude can't sign in to claude.ai, so comments and database sync can't be tested live. Say so.

## Artifact comments
- Thread `a16d17fa…` ("things get very dense") is still open. It was answered twice and later covered by the spacing pad. Leave it until the user decides.
