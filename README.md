# AI agent workflow for project builds

**Agent Flow Sketchpad** is a one-page canvas for sketching AI-agent workflows (orchestrator → workers). This repo holds the app and the workflows designed with it, **in separate folders**.

```
├─ app/                          ← the HTML app (code + docs)
│   ├─ agent-flow-sketchpad.html    the whole app, one file
│   └─ docs/
│       ├─ HANDOFF.md               how the app is built, history, sources
│       └─ AGENTS-worker-naming.md  AGENTS.md section for Codex workers
│
└─ workflows/                    ← setups saved in the app (canvas JSON)
    ├─ README.md                    which is which, how to load one
    ├─ named-workers-v3.json        ★ MAIN (the current design)
    └─ archive/                     older versions, kept for reference
```

- **Live page:** https://claude.ai/artifact/FAht8ymX9NECxeoieiT26w
- **How changes flow:** edit `app/agent-flow-sketchpad.html` → test → commit → republish to the same live page.
