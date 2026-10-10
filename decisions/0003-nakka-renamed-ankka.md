# 0003 — nakka renamed to ankka before first release

## Status
Accepted September 2026 (recorded 2026-09-30)

## Repos affected
ankka (done), cloudflow (text still says nakka; leave it, it is history), ankka-flow (older text),
the infrastructure repo (label domain and CRD names follow ankka). New repos use `ankka` only.

## Context
The platform was `nakka` through its unreleased life. Every package, artifact, image, namespace,
label domain, config key, environment variable, CRD and file path carried the name.

## Decision
Renamed everything to `ankka` in one commit before anything was published: packages
`com.thinkmorestupidless.ankka.*`, CRD `AnkkaService` short name `asvc`, label domain
`ankka.thinkmorestupidless.com`, env prefix `ANKKA_*`. Git history before that commit reads
`nakka` throughout and is not rewritten.

## Alternatives considered
1. **Keep nakka.** Lost: the reason is not recorded here; ask Trevor before assuming one.

## Consequences
- `~/.nakka/` on a machine, a kind cluster named `nakka`, the empty `../nakka/` directory and
  `../ankka-009-polyglot-runtimes/` are leftovers, not something the code reads.
- cloudflow's `CLAUDE.md` and `ROADMAP.md` say "nakka"; a handful of mentions stay as history.
- Grep for `nakka` before assuming a reference is live.

## Related
- ankka `CLAUDE.md` "What this is"
