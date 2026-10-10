# ankka-contrib

## One-liner
Integrations built on ankka's published SDK that are reusable but not the platform's: one module
per integration, released on its own cadence against a published ankka version.

## Why it exists
The Neo4j graph store behind ankka's graph sink needs the Neo4j driver and a database to test
against, which the platform should not carry for every service. It moved out of ankka into its own
repository so the platform's SDK stays free of integration dependencies and each integration can
release when it is ready. A new integration is a new module here, not a new repository.

## Status
- Stage: released
- As of: 2026-10-10
- Current release: `v0.1.0` (2026-10-08)
- Visibility: public

## Where it lives
- Repo: github.com/thinkmorestupidless/ankka-contrib
- Local: ~/Documents/projects/thinkmorestupidless/ankka-contrib
- Publishes, on a `v*` tag: Maven Central `com.thinkmorestupidless:ankka-contrib-neo4j-graph-store_3`;
  image `ghcr.io/thinkmorestupidless/ankka-graph-sink-neo4j`. The signed bundle is uploaded to the
  Central Portal the way ankka's is
- Runs: the image deploys into any ankka project as an ordinary service, with `"database": "none"`

## Depends on / depended on by
- **Consumes `ankka`** at 0.11.0 from Maven Central: the SDK and the graph sink module
  (`ankka-graph-sink`), whose rules are the platform's and are documented at
  `docs.ankka.cloud/deploy/graph-sink`.
- **Gives** a `GraphStore` over Neo4j 5.26 or later (merge statements, version guard, placeholder
  endpoints, tombstones, password kept out of every message) and a deployable sink image driven by
  `ANKKA_GRAPH_SINK_*` and `NEO4J_*` variables.
- **Took over from `ankka-flow`** the Neo4j sink: ankka-flow's README says its capabilities are
  ankka's and the Neo4j sink is ankka-contrib's.
- Nothing in the family pins it yet.

## Read first
1. `README.md`: the module table, the sink in a service of your own and as the image, the variables.
2. `build.sbt`: the ankka version and the two modules.
3. `neo4j-graph-store/` and `graph-sink-neo4j/`: the store and the service.

## Conventions specific to this repo
- One sbt build, one module per integration, each with its own artifact or image.
- Released against a published ankka version only; never against a snapshot.
- No `CLAUDE.md` as of 2026-10-10.

## Open questions
- 2026-10-10: no `CLAUDE.md` and no `docs/`; the README is the whole documentation.
- 2026-10-10: nothing in the family pins it; ankka-reasoning still names ankka-flow's merge sink.

## Recent moves
- 2026-10-08: `v0.1.0`. Release uploads the signed bundle to the Central Portal as ankka's does.
- 2026-10-08: built against ankka 0.11.0, the release that ships the graph sink module.
- 2026-10-08: the Neo4j graph store and the sink image moved here from ankka.
