# Decisions

ADR-style log of decisions that touch **more than one repository** in the ankka family, or that
explain why the family is shaped the way it is.

## Where decisions live

The repos keep their own logs, and those stay authoritative for anything inside one repo:

| Repo | In-repo log |
|---|---|
| ankka-deployments | `docs/decisions.md` (numbered D1, D2, ...) |
| cloudflow | `ROADMAP.md`, decisions section at the end |
| satisfactory | `DESIGN.md` (rationale throughout) and `API.md` (every divergence from Timefold, justified) |
| ankka-flow | `docs/design/version-one.md` |
| ankka-cloud | `docs/design/hosted-ankka.md` |
| ankka | `docs/` and `CLAUDE.md` |
| ankka-reasoning | `specs/NNN-name/research.md`, and `notes/ankka-requests.md` for what it asks of ankka and ankka-flow |
| ankka-casino | `.specify/memory/constitution.md` in the repo; the rest in the eitheror brain, `brain/projects/ankka-casino.md`, and the build plan's comment threads it links |

This log is for the rest. When an in-repo decision turns out to constrain a sibling, add an entry
here that states the cross-repo consequence and links to the in-repo entry. Do not copy the
in-repo text.

## When to log here
- A choice that changes what two or more repos must do or must not do.
- A choice a sibling repo would have to know about to stay compatible.
- A reversal of an earlier entry (link it, mark the old one superseded).
- Anything you would want to explain in six months without re-deriving it.

## When not to log here
- Routine implementation choices.
- Anything already obvious from code, commit history, or an in-repo log.
- Decisions with one repo's blast radius. Those go in that repo's log.

## File naming
`NNNN-slug.md`. Increment; never reuse a number.

## Template

```
# NNNN — Title

## Status
Accepted YYYY-MM-DD | Superseded by NNNN | Reversed YYYY-MM-DD

## Repos affected
Which repos, and what each must do or must not do as a result.

## Context
The situation and the forces at play.

## Decision
What was chosen. One paragraph.

## Alternatives considered
One to three real alternatives, with the actual reason each lost.

## Consequences
What this makes easier. What this makes harder. What every repo now has to remember.

## Related
In-repo logs, specs, prior entries.
```
