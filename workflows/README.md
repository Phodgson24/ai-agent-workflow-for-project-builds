# Workflows

Setups saved in the Agent Flow Sketchpad, exported as canvas JSON. The live copies are in the app's database (collection `setups`). These files are snapshots.

| File | Name in the app | Boxes · links | Saved (UTC) | App db id |
|---|---|---|---|---|
| ★ `named-workers-v2.json` | user agent + project folder (named workers)v2 | 12 · 5 | 2026-09-24 20:00 | `dhhsk0icnb92x7` |
| `archive/named-workers.json` | … (named workers) | 11 · 6 | 2026-09-24 19:14 | `namedworkers01` |
| `archive/sub-flow.json` | … (sub-flow) | 11 · 5 | 2026-09-24 15:00 | `subflowrelay01` |
| `archive/improved.json` | … (improved) | 4 · 5 | 2026-09-24 14:31 | `improvedrelay01` |
| `archive/original.json` | user agent + project folder | 4 · 6 | 2026-09-24 14:25 | `rz9azhghfmjxsh` |

★ = main, the one to reference and build on. "Boxes" counts boxes on every level; "links" counts only the top level.

## Load one into the app

1. Open the file and copy all of it.
2. In the app, click **JSON**, paste it, then click **Load pasted JSON**.
3. To keep it, click **Setups**, then **Save as new setup**. (Loading from JSON doesn't overwrite any saved setup.)

## Update a snapshot

After you change a setup in the app, ask Claude to "re-export the workflows". It reads the app's database and refreshes these files.
