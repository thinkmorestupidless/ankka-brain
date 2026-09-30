# ankka-brain: agent context

This repository is the family-level context for the ankka repositories. Read the always-on files
below before cross-repo work; load the rest on demand.

## Always on

@family.md
@principles.md
@repos/README.md

## Load on demand

- `conventions.md`: how every repo is built, tested, released; agent rules. Load before writing code in any family repo.
- `glossary.md`: cross-repo vocabulary. Load when a term is unfamiliar.
- `infrastructure.md`: registries, clusters, domains, secret names. Load before deploy or release work.
- `roadmap.md`: what is in flight and what waits on what. Load before planning.
- `audiences.md` and `voice.md`: load before writing anything public.
- `reuse-map.md`: what is shared and what deliberately is not.
- `repos/<name>.md`: one per repo; load the one you are working in and any it depends on.
- `decisions/`: cross-repo decisions; the README lists where each repo's own log is.

## Operating notes

- Each repo's own `CLAUDE.md`, `docs/`, design documents and decision logs stay authoritative for
  anything inside that repo. This brain points at them and never restates them.
- Status claims here decay. Check the date line before trusting one; update it when you touch it.
- A decision that changes what two repos must do goes in `decisions/`, then gets linked from each
  repo's in-repo record.
- Checkouts are siblings under `~/Documents/projects/thinkmorestupidless/`. Relative paths in this
  brain assume that.
