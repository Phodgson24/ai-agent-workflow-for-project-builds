# Notes for Claude

Read `app/docs/HANDOFF.md` first: it covers the user, the code map, setups, testing and comments. The points below **update or override** it.

## Repo layout
- `app/`: the HTML app and its docs. `workflows/`: setups exported from the app. Keep the two separate.
- The repo file `app/agent-flow-sketchpad.html` is the source of truth. It is identical to the live artifact at version 21.

## Publishing
- Edit `app/agent-flow-sketchpad.html`, test it, commit it, then publish with `Artifact` using `url: https://claude.ai/artifact/FAht8ymX9NECxeoieiT26w` and `file_path` set to that file. Omit `capabilities` and `icon`.
- Before publishing, `Artifact read` the live page. If it changed since the last commit, merge first.

## Setups (app database)
- **Main setup:** `dhhsk0icnb92x7`, "user agent + project folder (named workers)v2" (12 boxes · 5 links). The user saves it from the app. It is newer than `namedworkers01`: same boxes and links, with layout tweaks.
- Never overwrite a setup without being asked. When editing one, pin the write with `if_version`.
- When re-exporting, write each setup's `canvas` field to `workflows/` (see `workflows/README.md`).

## Testing in the cloud
- The handoff's `preview_start` tools are desktop-only. Here, use Playwright with Chromium (preinstalled, `executablePath: '/opt/pw-browsers/chromium'` if needed) and a local `python3 -m http.server`.
- Syntax check: extract the `<script>` block and run `node --check`.
- Claude can't sign in to claude.ai, so comments and database sync can't be tested live. Say so.

## Artifact comments
- Thread `a16d17fa…` ("things get very dense") is still open. It was answered twice and later covered by the spacing pad. Leave it until the user decides.
