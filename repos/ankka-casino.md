# ankka-casino

## One-liner
eitheror's casino and payments as services on ankka, replacing the twin substrate
`eitheror-platform` runs today: each brand an ankka project, payments and the affiliate programme
shared projects.

## Why it exists
eitheror runs a real-money casino on its own substrate (digital twins, TL functions, Go handlers,
Neo4j, Cloudflow). ankka-casino rebuilds that as ordinary ankka services and operates them with
the ankka runtime, so the domain keeps its behaviour and ankka carries the platform. It is the
first consumer of ankka that is not a member of this family: eitheror owns it, thinkmorestupidless
does not, and it is the first real-money one. What a real-money operator needs and ankka lacks
becomes an ankka spec; specs 038 to 044 came from it.

## Status
- Stage: designing (specifying; nothing runs)
- As of: 2026-10-08
- Current release: unreleased
- Visibility: private (eitheror-io)

## Where it lives
- Repo: github.com/eitheror-io/ankka-casino
- Local: `~/Documents/projects/eitheror/ankka-casino`. Not a sibling under
  `~/Documents/projects/thinkmorestupidless/`; relative paths in this brain do not reach it.
- Brain: the eitheror brain, `~/Documents/projects/eitheror/brain/projects/ankka-casino.md`,
  holds its status, people and decisions. This file only places it in the family.
- Publishes: nothing yet. Private images and services, never to the family's registries.
- Runs: nowhere yet. Planned: self-hosted on GKE, provisioned through `ankka-gcp` (ankka spec 044),
  deployed with the ankka runtime. Not on arrakis or any hosted installation.

## Depends on / depended on by
- **An ankka application**, built with the ankka SDK and deployed with the runtime. Not yet pinned:
  it waits on an ankka release that carries specs 038 to 044.
- **Needs from ankka**, all on the `specs-038-044` branch: 038 secret store backends, 039 GCS
  object storage, 040 cross-project access, 041 backups and recovery, 042 personal data erasure,
  043 topic retention, 044 cloud provider. Also `ankka-port` to record the original, and the
  `ankka@ankka` skills to build and inspect services.
- **Gives back**: the real-money requirements that drove 038 to 044, and the first consumer of
  `ankka-gcp` once 044 lands.
- Nothing in the family depends on it.

## Read first
1. `README.md` and `CLAUDE.md`: what it is, the spec list in build order, how to work there.
2. `.specify/memory/constitution.md`: the rules every spec is held to (the running original is the
   specification; money is exact; a brand is a project; commands over HTTP, facts over topics;
   personal data is marked).
3. `GLOSSARY.md` and `features/`: the domain's words and what the system does.
4. The build plan and the ankka core features it depends on, both linked from its `CLAUDE.md`.

## Conventions specific to this repo
- Spec-kit with the bdd extension, as in ankka: living features from spec 001 on.
- 19 specs in build order, numbered up front in `CLAUDE.md`; written out of order as needed.
- One project per brand; shared projects named (`payments`, `programme`, `reasoner`).
- Scala by default; British spelling; `eitheror` lowercase; money in minor units as `Long`.
- The original (`~/Documents/projects/eitheror/eitheror-platform`) is the specification; it is
  read, recorded with `ankka-port`, and kept as the oracle.

## Open questions
- 2026-10-08: not pinned to any ankka release; 038 to 044 are written, none implemented.
- 2026-10-08: the thinkmorestupidless and eitheror agreement is not written down (ankka is
  Apache 2.0). Owned in the eitheror brain.
- 2026-10-08: `ankka-gcp` does not exist yet; it is created after 044 is planned.

## Recent moves
- 2026-10-08: four specs written: 001 brands and projects (one project per brand, the port
  method), 002 wallet, 012 attribution (the affiliate protocol a brand emits), 013 affiliate
  programme (a shared project). Living features and the glossary for all four.
- 2026-10-08: repository created with spec-kit and the bdd extension; constitution and the spec
  list in build order.
