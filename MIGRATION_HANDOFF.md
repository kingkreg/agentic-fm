# Migration Handoff

This repo was migrated from the old MacBook to the new MacBook in July 2026.

## GitHub Repo

Use Kreg's fork as the active repo:

```bash
https://github.com/kingkreg/agentic-fm.git
```

The old upstream repo was:

```bash
https://github.com/petrowsky/agentic-fm.git
```

Kreg does not have push permission to the Petrowsky repo, so local clones should point `origin` at `kingkreg/agentic-fm`.

## What Was Preserved

The following migration checkpoint was pushed to `kingkreg/agentic-fm`:

```text
7965d05 Checkpoint FileMaker clipboard layout workflow
```

That checkpoint includes:

- FileMaker clipboard support updates in `agent/scripts/clipboard.py`
- FileMaker deploy compatibility updates in `agent/scripts/deploy.py`
- layout-object XML2 clipboard knowledge in `agent/docs/knowledge/layout-object-clipboard.md`
- updated layout-design skill guidance
- updated knowledge manifest

## New Mac Setup

On the new Mac, install Apple's command line tools if Git is missing:

```bash
xcode-select --install
```

Clone the repo:

```bash
git clone https://github.com/kingkreg/agentic-fm.git
cd agentic-fm
```

In the ChatGPT desktop app, add this folder as a local Codex project.

## Clipboard Workflow To Remember

FileMaker objects are moved through the repo's clipboard helper, not plain `pbcopy` / `pbpaste`:

```bash
python3 agent/scripts/clipboard.py read agent/sandbox/output.xml
python3 agent/scripts/clipboard.py write agent/sandbox/myscript.xml
```

Deployment uses:

```bash
python3 agent/scripts/deploy.py
```

## Prompt For New Codex Task

If the old thread is not visible on the new Mac, start a new Codex task in the `agentic-fm` local project and say:

```text
We migrated from the old MacBook. Please read AGENTS.md and MIGRATION_HANDOFF.md, then help me verify this new machine can use agentic-fm, including Git, Python, the FileMaker clipboard helper, and deploy.py.
```

