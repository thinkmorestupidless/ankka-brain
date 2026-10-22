# ankka-brain: agent context

This repository is the family-level context for the public ankka repositories. Read the always-on
files below before cross-repo work; load the rest on demand. The private members of the family
(the hosted product, the infrastructure, products built for customers) are mapped in the sibling
checkout `../ankka-brain-private`, if present; its `CLAUDE.md` says what to read there.

## Always on

@family.md
@principles.md
@repos/README.md

## Load on demand

- `conventions.md`: how every repo is built, tested, released; agent rules. Load before writing code in any family repo.
- `glossary.md`: cross-repo vocabulary. Load when a term is unfamiliar.
- `infrastructure.md`: registries, package coordinates, public hostnames. Load before release work.
- `roadmap.md`: what is in flight and what waits on what. Load before planning.
- `audiences.md` and `voice.md`: load before writing anything public.
- `reuse-map.md`: what is shared and what deliberately is not.
- `repos/<name>.md`: one per repo; load the one you are working in and any it depends on.
- `decisions/`: cross-repo decisions; the README lists where each repo's own log is.
- `workspace/`: the parent-directory `CLAUDE.md` and how to install it.

## Operating notes

- Each repo's own `CLAUDE.md`, `docs/`, design documents and decision logs stay authoritative for
  anything inside that repo. This brain points at them and never restates them.
- Status claims here decay. Check the date line before trusting one; update it when you touch it.
- A decision that changes what two repos must do goes in `decisions/`, then gets linked from each
  repo's in-repo record. If a private repo is affected, it goes in the private brain.
- Nothing private goes here: no installation values, cluster or project names, secret names beyond
  the public repos' own CI, no private repo's status or pins. A private repo may be named by role.
- Checkouts are siblings under one directory. Relative paths in this brain assume that.
