# Migration Handoff

This repo was migrated from the old MacBook to the new MacBook in July 2026.

The old Codex thread history did not reliably appear on the new laptop, so this
file is the practical handoff record. Treat the repo files and this document as
the source of truth for continuing the work.

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
0091140 Add migration handoff notes
```

That checkpoint includes:

- FileMaker clipboard support updates in `agent/scripts/clipboard.py`
- FileMaker deploy compatibility updates in `agent/scripts/deploy.py`
- layout-object XML2 clipboard knowledge in `agent/docs/knowledge/layout-object-clipboard.md`
- updated layout-design skill guidance
- updated knowledge manifest

There is also a local old-laptop commit that was deliberately **not pushed** to
GitHub because it contains private FileMaker project artifacts:

```text
dbd5110 Add migration project artifacts
```

Those artifacts should be copied directly between Macs instead of pushed to a
public/shared Git remote.

## Local Folders Copied Outside Git

The following folders contain local FileMaker work context and should exist on
the new Mac inside `~/agentic-fm/`:

```text
agent/sandbox
agent/context
plans
```

`agent/xml_parsed` is useful if copied, but it can be recreated from fresh
FileMaker XML exports if needed.

`xml_exports` was **not copied** during migration. On the old Mac it was about
9 GB. That folder is generated export data and should be recreated on the new
Mac by re-exporting the FileMaker XML when needed.

Do not wipe the old Mac until the new Mac has either:

- copied `agent/xml_parsed`, or
- confirmed that the FileMaker XML export/reparse workflow works and can
  recreate it.

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

## New Mac Verification Checklist

Before wiping the old Mac, run these checks on the new Mac.

### 1. Confirm Git and repo

```bash
cd ~/agentic-fm
git remote -v
git status --short
git log -5 --oneline
```

Expected:

- `origin` points to `https://github.com/kingkreg/agentic-fm.git`
- `git status --short` is empty or only shows known local copied artifacts
- recent commits include `0091140 Add migration handoff notes`

### 2. Confirm copied local work folders

```bash
cd ~/agentic-fm
du -sh agent/sandbox agent/context plans 2>/dev/null
find agent/sandbox -type f | wc -l
find agent/context -type f | wc -l
find plans -type f | wc -l
```

Expected old-Mac reference sizes/counts:

```text
agent/sandbox   about 18 MB, about 280 files
agent/context   about 7 MB, about 435 files
plans           about 32 KB
```

### 3. Check optional generated folders

```bash
cd ~/agentic-fm
du -sh agent/xml_parsed xml_exports 2>/dev/null
```

Old-Mac reference:

```text
agent/xml_parsed   about 1.1 GB
xml_exports        about 9 GB
```

It is okay if `xml_exports` is missing on the new Mac, as long as Kreg can
re-export FileMaker XML later. If `agent/xml_parsed` is missing, the new Codex
may need fresh exports before it can inspect the full solution.

### 4. Confirm Python tooling

```bash
cd ~/agentic-fm
python3 --version
python3 -m agent.fmlint --help
python3 agent/scripts/clipboard.py --help
python3 agent/scripts/deploy.py --help
```

Expected: all commands print help/version output without crashing.

### 5. Confirm macOS automation basics

```bash
command -v osascript
```

Expected: a path such as `/usr/bin/osascript`.

The first real FileMaker clipboard/deploy action may trigger macOS privacy
prompts. Allow ChatGPT/Terminal/FileMaker automation or accessibility access
when prompted.

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
We migrated from the old MacBook. Please read AGENTS.md and MIGRATION_HANDOFF.md, then help me verify this new machine can use agentic-fm. Assume prior chat history is unavailable. Check Git, Python, copied local folders, the FileMaker clipboard helper, and deploy.py before we wipe the old Mac.
```
