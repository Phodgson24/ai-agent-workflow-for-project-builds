# Read this first: the purpose of Agent Flow Sketchpad

## In the owner's words

> "The whole point of this app and the workflow we are creating is to create something clear to a human, but when done I would give this workflow to Codex and have it review it and then convert this workflow into an AGENTS.md file and/or create an AGENTS.md file based on the created workflow in this app."

## What that means

```
1. DESIGN (human)            2. HAND OFF                3. BUILD (AI agent)
Agent Flow Sketchpad   ──►   the workflow (JSON)   ──►  Codex reviews it, then writes
a visual workflow            from workflows/            an AGENTS.md from it
a person can read            or the app's JSON button
```

- **The app is a thinking tool for a person.** Its job is to make an agent workflow (orchestrator, workers, handovers, rules) clear and easy to follow for a human, including someone with ADHD: minimal, uncluttered and consistent.
- **The workflow is the real product.** A finished workflow is the spec an AI agent turns into an `AGENTS.md` (the instruction file Codex reads) for the project it describes.
- **Clarity for humans comes first.** When designing, favour what a person can read at a glance. Put detail in the places the app keeps it (rules, descriptions, link notes). They are all in the exported JSON, so nothing is lost for the agent.

## If you are an AI agent working in this repo

- **Building or changing the app** (`app/`): keep it serving that purpose, a clear visual workflow for a human. Read `CLAUDE.md` and `app/docs/HANDOFF.md` for how it is built.
- **Editing a workflow** (`workflows/`): keep it readable for a human, and make every rule complete enough to become an `AGENTS.md` instruction.
- **Converting a workflow into an `AGENTS.md`:**
  1. Read `workflows/README.md` first. Its "Reading the JSON" section explains sub-flows, `pin:` entry/exit points and badge colours.
  2. Review the workflow for gaps or contradictions and report them before writing anything.
  3. Turn boxes, links, conditions, rules and notes into plain instructions for the agents it describes (orchestrator, workers).
  4. The `AGENTS.md` you produce is for the **project the workflow describes** (for example `my-app`), not for this repo.
- **Don't change the app or a workflow unless the owner asks.** Review and suggest first.
