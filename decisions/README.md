# Decisions

ADR-style log of decisions that touch **more than one repository** in the ankka family, or that
explain why the family is shaped the way it is. Entries that involve a private repository live in
`ankka-brain-private/decisions/`, which has its own numbering; the two logs link each other.

## Where decisions live

The repos keep their own logs, and those stay authoritative for anything inside one repo:

| Repo | In-repo log |
|---|---|
| ankka | `docs/` and `CLAUDE.md` |
| cloudflow | `ROADMAP.md`, decisions section at the end |
| satisfactory | `DESIGN.md` (rationale throughout) and `API.md` (every divergence from Timefold, justified) |
| ankka-flow | `docs/design/version-one.md` |
| ankka-reasoning | `specs/NNN-name/research.md`, and `notes/ankka-requests.md` for what it asks of ankka and ankka-flow |
| ankka-contrib | none yet; the README |
| ankka-predictions | `docs/decisions.md` (D1, D2), and `notes/` for what it asks of ankka, ankka-reasoning and its consumer |

The private repos' logs are listed in the private brain.

This log is for the rest. When an in-repo decision turns out to constrain a sibling, add an entry
here that states the cross-repo consequence and links to the in-repo entry. Do not copy the
in-repo text.

## Entries moved to the private brain
- `0005` installation values and design treatments live in the infrastructure repo: now
  `ankka-brain-private/decisions/0001`.
- `0010` one identity realm, hub and spokes, the hosted product owns the product realm: now
  `ankka-brain-private/decisions/0002`.

The numbers 0005 and 0010 are retired here and not reused.

## When to log here
- A choice that changes what two or more public repos must do or must not do.
- A choice a sibling repo would have to know about to stay compatible.
- A reversal of an earlier entry (link it, mark the old one superseded).
- Anything you would want to explain in six months without re-deriving it.

## When not to log here
- Routine implementation choices.
- Anything already obvious from code, commit history, or an in-repo log.
- Decisions with one repo's blast radius. Those go in that repo's log.
- Anything whose "repos affected" includes a private repo. That goes in the private brain, with
  a one-line pointer here if a public repo is also affected.

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
