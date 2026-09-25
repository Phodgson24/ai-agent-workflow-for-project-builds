# Workflows

Setups saved in the Agent Flow Sketchpad, exported as canvas JSON. The live copies are in the app's database (collection `setups`). These files are snapshots.

| File | Name in the app | Boxes · links | Saved (UTC) | App db id |
|---|---|---|---|---|
| ★ `named-workers-v8.json` | user agent + project folder (named workers) v8 | 35 · 5 | 2026-09-25 | `namedworkersv8` |
| `named-workers-v7.json` | … (named workers) v7 | 35 · 5 | 2026-09-25 | `namedworkersv7` |
| `named-workers-v6.json` | … (named workers) v6 | 31 · 5 | 2026-09-25 | `namedworkersv6` |
| `named-workers-v5.json` | … (named workers) v5 | 31 · 5 | 2026-09-25 | `namedworkersv5` |
| `named-workers-v4.json` | … (named workers) v4 | 23 · 5 | 2026-09-24 | `namedworkersv4` |
| `named-workers-v3.json` | … (named workers) v3 | 13 · 5 | 2026-09-24 | `namedworkersv3` |
| `archive/named-workers-v2.json` | … (named workers)v2 | 12 · 5 | 2026-09-24 20:00 | `dhhsk0icnb92x7` |
| `archive/named-workers.json` | … (named workers) | 11 · 6 | 2026-09-24 19:14 | `namedworkers01` |
| `archive/sub-flow.json` | … (sub-flow) | 11 · 5 | 2026-09-24 15:00 | `subflowrelay01` |
| `archive/improved.json` | … (improved) | 4 · 5 | 2026-09-24 14:31 | `improvedrelay01` |
| `archive/original.json` | user agent + project folder | 4 · 6 | 2026-09-24 14:25 | `rz9azhghfmjxsh` |

★ = main, the one to reference and build on. v8 = v7 reorganised: on the Orchestrator Thread page, the safe-point, worker-pause and commit steps are one sub-flow, "Reach a safe point (pause any active worker)", followed by a second, "Compact or hand over"; the old "Context full" box is gone. v7 = v6 plus a worker check in the orchestrator's Context full: at the safe point, if a worker is mid-task the orchestrator tells it to save and pause, waits for its reply, and tells it to resume after compacting (after a handover, the new orchestrator does). v6 = v5 with the orchestrator handover folded into one place: Fresh orchestrator and Handover check now live inside the Orchestrator Thread's "Context full: compact or hand over", which has two exits back (continue compacted, O02 takes over). v5 = v4 plus a "Handover check" sub-flow: old and new orchestrator overlap briefly, run a 3-point check, then ownership (OWNER line in progress.md) moves to the new one. v4 = v3 with the Orchestrator turned into an "Orchestrator Thread" sub-flow that follows the same compact-once-then-hand-off rule as workers. v3 = v2 with compaction merged into the handoff sub-flow (now "Context full: compact or hand off"). "Boxes" counts boxes on every level; "links" counts only the top level.

## Reading the JSON (for people and AI agents)

- A **sub-flow** is a box with a `sub` object: its own `nodes`, `edges` and `pins`.
- An **entry/exit point** inside a sub-flow is written `pin:<link id>`. `<link id>` is the link on the level above that touches the sub-flow box. So `"to": "pin:hodoc"` inside means "leaves through the link `hodoc` outside".
- A **match badge** is `"badge": "red"` (or orange, yellow, green, cyan, blue, violet, pink) on that outside link. The app shows the same coloured dot where the link meets the sub-flow box and on its `pin:` point inside. Same colour = same connection.

## Load one into the app

1. Open the file and copy all of it.
2. In the app, click **JSON**, paste it, then click **Load pasted JSON**.
3. To keep it, click **Setups**, then **Save as new setup**. (Loading from JSON doesn't overwrite any saved setup.)

## Update a snapshot

After you change a setup in the app, ask Claude to "re-export the workflows". It reads the app's database and refreshes these files.
