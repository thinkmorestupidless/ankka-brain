# The workspace file

`CLAUDE.md` in this directory is meant to live one level above the family checkouts, not in any
repository. Claude Code loads a `CLAUDE.md` from every directory above the one it starts in, so a
session started in any family repo gets the brain's always-on files without the repo having to
import them.

## Install

```bash
cd ~/Documents/projects/thinkmorestupidless     # or wherever the checkouts are siblings
git clone git@github.com:thinkmorestupidless/ankka-brain.git
cp ankka-brain/workspace/CLAUDE.md CLAUDE.md
```

Then clone the family repos beside it: `ankka`, `ankka-flow`, `cloudflow`, `satisfactory`,
`ankka-reasoning`, `ankka-contrib`. The file's imports are relative to its own location, so the
directory name of the brain must stay `ankka-brain`.

Those with access to the private repos also clone `ankka-brain-private` beside it and copy its
`workspace/CLAUDE.local.md` to `CLAUDE.local.md` in the same directory. Claude Code loads the two
side by side. Without the private checkout nothing is missing and nothing fails.

## What to expect

- The first session in each repo asks once to approve "external imports", because the workspace
  file imports from `ankka-brain/`, which is outside that repo. Approve them. In non-interactive
  runs (`claude -p`) those imports are skipped without an error, so the workspace file's own text
  still loads but the imported files do not.
- `/context` lists the memory files loaded; the brain's files should appear under the workspace
  `CLAUDE.md`.
- If the directory also holds unrelated projects, the workspace file is loaded there too. Its
  first lines say to ignore it when the task is not in a family repo. To silence it for one repo,
  add that file's path to `claudeMdExcludes` in that repo's `.claude/settings.local.json`.

## Keeping it current

The file is a copy, so a change here does not reach installed copies. Keep it to imports and a
few lines, so that it rarely changes; everything that changes lives in the brain's files it
imports.
