# 0011 — Two brains: this one for the public repos, a private one beside it

## Status
Accepted 2026-10-10

## Repos affected
- ankka-brain (this repo): covers only the public repositories. Nothing installation-specific, no
  private repo's status, pins, secret names or design, even by pointer beyond a name. Ships
  `workspace/CLAUDE.md`, the parent-directory file that wires the brain into Claude Code.
- ankka-brain-private: covers the private repositories and everything installation-specific.
  Extends every file here (family, roadmap, glossary, infrastructure, reuse map, decisions, repos)
  rather than repeating it. Ships `workspace/CLAUDE.local.md`, loaded beside the public file.
- Every public family repo: may name a private repo; never carries its content. A pointer line to
  `../ankka-brain` is enough; the private brain is found through the workspace file.
- Every private family repo: points at both brains.

## Context
The brain was private because it mixed the public platform's map with the hosted product, the
infrastructure and a customer's repo. That made it unreadable by the people the public repos are
for, and it was growing a second class of private member (products built on ankka for customers,
and a repo owned by another company). Visibility is a trust boundary in this family (principle 5);
the brain was the one place that crossed it.

## Decision
Split by visibility. The public brain holds what anyone working on the public repos needs; it may
say that private members exist and what role they play, nothing more. The private brain holds the
rest and reads as an extension: same file names, same sections, only the private rows and chains.
Claude Code picks both up through one parent directory: `CLAUDE.md` from the public brain and
`CLAUDE.local.md` from the private one, each importing its brain's always-on files; a missing
private checkout costs nothing because the local file is simply absent.

## Alternatives considered
1. **One brain, kept private, public repos point at it anyway.** Lost: the pointer is dead for
   everyone outside; the public repos' agents work blind.
2. **One brain with private files gitignored or in a submodule.** Lost: gitignored content has
   no history; a private submodule in a public repo leaks its existence and path and fails to
   clone for everyone else.
3. **A single parent `CLAUDE.md` importing both brains.** Works, since a missing import is skipped
   silently (verified 2026-10-10), but puts the private brain's name and layout in a public file
   and gives no place for private-only wiring. Kept as a fallback; `CLAUDE.local.md` is cleaner.

## Consequences
- Two things to keep current, with the same maintenance ritual. A release in a public repo touches
  only the public brain; a release in a private one touches only the private brain, unless a pin
  crosses the line, in which case both.
- Decision numbering diverges: this log continues from 0011; the private log starts at 0001. 0005
  and 0010 moved there as 0001 and 0002; their numbers are retired here.
- Imports from the parent file into a sibling checkout are "external imports" to Claude Code and
  need a one-time approval per repo in interactive sessions; in non-interactive runs they are
  skipped. The workspace README says so.
- This repo can be made public once this entry is merged. Until then its visibility is a choice,
  not a constraint.

## Related
- `0001-adopt-ankka-brain.md`; `../principles.md` 5
- `../workspace/README.md`
- `ankka-brain-private/decisions/README.md`
