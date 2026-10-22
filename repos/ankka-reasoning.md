# ankka-reasoning

## One-liner
A reasoning graph on ankka: evidence, claims, hypotheses, beliefs and, since the process layer,
the runs of blueprints that wrote them, as records that are never edited, published as graph
deltas for ankka's graph sink to keep in Neo4j.

## Why it exists
ankka's graph consumer and graph sink copy a service's entities into Neo4j. That is the pipe, not
the graph: nothing said what a reasoning graph holds, who owns a link, how history is kept or how
an answer is read out. It is its own repo because it is an ankka application and a domain, not
platform, and what it cannot do becomes a request to ankka or ankka-contrib.

Three decisions (2026-10-06): a prediction market is the first domain; the vocabulary is layered
(belief, market, process); history is kept by immutable revisions. One more (2026-10-18): the
process layer hosts ankka's blueprints and adds nothing of its own to run one; what it adds is
the act, the record of which run wrote a record, and the tools a run writes through.

## Status
- Stage: released; spec 002 (the process layer) built and merged to `main` in seven phases
  (PRs #7, #9, #10, #12, #13, #14; the last merged 2026-10-22). The feature is built; a release
  (0.2.0) is owed and not yet tagged
- As of: 2026-10-22
- Current release: 0.1.1 (2026-10-06; the belief and market layers). The process layer is in no
  release yet; the next tag publishes four libraries and the service with it
- Visibility: public

## Where it lives
- Repo: github.com/thinkmorestupidless/ankka-reasoning
- Local: ~/Documents/projects/thinkmorestupidless/ankka-reasoning
- Publishes: documentation to `https://reasoning.ankka.cloud/` (GitHub Pages, from `main`). On a `v*` tag, by `release.yml`: `ankka-reasoning-{graph,belief,market,process}_3` to Maven Central, the image `ghcr.io/thinkmorestupidless/ankka-reasoning`, the plugin to ankka-marketplace, and a release page with the service descriptor, the sink descriptor and blueprint attached
- Runs: on a laptop (`just up`, `just run`), and once on the local kind installation as project
  `reasoning` (release 0.1.1, 2026-10-07; the process layer not yet). Never on a cloud installation

## Depends on / depended on by
- **An ankka application** (Scala 3.9.0), pinned to **ankka 0.12.0** from Maven Central:
  entities, graph consumers, views, endpoints, `ankka-agent` (blueprints, runs, the test
  providers), `ankka-auth-oidc` and the test kits.
- **Runs beside ankka's graph sink** in **ankka-contrib's image
  `ankka-graph-sink-neo4j:0.1.0`** (`linux/amd64` only; emulated on Apple silicon), over one
  compacted delta topic the project declares. Bound by `ankka.graph-delta.v1`: whole state, one
  writing entity per element, plain property values. Nothing of ankka-flow is pinned.
- **Live only**: Anthropic's model and TypeSafe AI's Jev through ankka's providers, from
  `ANTHROPIC_API_KEY` and `TYPESAFE_API_KEY`; the build never sets them.
- **Needs from outside the family**: Kafka and Neo4j 5.26+, supplied by whoever deploys. ankka has
  no graph client, so the service brings the Neo4j Java driver for reads.
- Depended on by `voice` (the VOICE project's product), which runs the released image beside
  itself as its evidence service; see `voice-brain`.

## Read first
1. `README.md` and `docs/index.md` (the site's home, https://reasoning.ankka.cloud/): what it is and
   the example every page and feature shares.
2. `CLAUDE.md`: the seven rules.
3. `specs/001-belief-layer/spec.md` and `specs/002-process-layer/spec.md`, each with its link
   table; `research.md` beside each for why, and "Found during implementation" for what changed.
4. `features/` and `GLOSSARY.md`: the scenarios the specs name and their words.

## Conventions specific to this repo
- Living features from spec 001 on (no `specs-from`).
- Six sbt modules, `graph` ← `belief` ← `market` ← `process` ← `service`, and `seed` apart: the
  layering is the dependency direction. A layer names nothing above it.
- Every record that carries text a writer supplied is a key value entity, never an event, because
  ankka never removes an event and text has to be withdrawable. Only a belief's line is event
  sourced.
- The process layer writes the act after its record, and every check before either; a record has
  one act, the first run's. The blueprint is the writer `blueprint:<name>` and speaks for the
  holders the service registers at start.
- The golden set (`golden-set/`) is the one set of documents every suite, page, live run and
  laptop walkthrough reads; its sources are registered by the service at start.
- Requests to the platform are kept in `notes/ankka-requests.md`, each with what it would remove.
- Docs as in the family: `docs/` built by ankka's docs tool (`ankka-docs`, branch `main`), one
  skill `ankka-reasoning` rendered into `marketplace/plugins/ankka-reasoning/`, pushed to
  ankka-marketplace by the release.
- Release as in the family: a tag is the only thing that publishes; the version is dynver's. The
  image is named `ankka-reasoning`; the service it runs is `reasoning`.

## Open questions
- 2026-10-21: the live suite has not been run with keys ten times (SC-007); the figure is owed in
  spec 002's research.md.
- 2026-10-21: spec 002's tier 5 (the process layer on a local kind installation) is not run; the
  kind installation `kind-ankka` was down when phase 7 reached it. Tier 4 by hand is not run
  either: the compose stack's Kafka and Neo4j ports were held by another project's kind cluster
  that day; the pages' replies come from the docs capture suite under the graph fixture.
- 2026-10-06: the service sets Pekko's `refresh-interval` to 1s itself to meet its five-second
  time to the graph; asked of ankka as a setting of its own (request 10).
- 2026-10-06: prior art is another company's repository, not a dependency. The spec carries its
  lessons as rules and names nothing from it.

## Recent moves
- 2026-10-21: phase 7 of spec 002 opened as a pull request: `just seed golden-set`,
  `just measure-golden-set`, five new pages (the process layer, run a blueprint, the appraiser,
  the golden set, live) with the service's real replies captured by a suite, every sink page
  rewritten, the requests note refreshed, and three fixes found on the way: a run of an unknown
  blueprint answered `500`, the release job named three Central modules where the build publishes
  four, and the capture had to wait for the acts' lag.
- 2026-10-20: phase 6 merged (PR #13): the live suite against Anthropic and Jev, skipped without
  keys; it runs against the graph fixture's service with its providers switched (F-v).
- 2026-10-19: phase 5 merged (PR #12): the appraiser's versions over HTTP and the golden set's
  eleven scenarios; the four runs take about 6 s on a laptop (SC-006). Phase 4 (PR #10): runs,
  endings and versions in the graph, answers with acts.
- 2026-10-18: phases 1 to 3 merged (PRs #7, #9): the sink moved to ankka-contrib's image, ankka
  0.12.0, the `process` module, the appraiser and its three tools, `POST /process/runs`.
- 2026-10-14: ankka v0.12.0 released with blueprints, `ankka-auth-oidc` and the single-file
  `GherkinSuite`; both of this repo's open requests on ankka closed.
- 2026-10-07: tier 5 of spec 001 run on kind: the service as `service:shoppingcart/cart`'s writer
  recorded the launch example and explained it through the cluster's Kafka, sink and Neo4j.
- 2026-10-06: 0.1.1 released: the three libraries are on Maven Central and the image is on
  ghcr.io. Documentation site live at reasoning.ankka.cloud; spec 001 implemented, 119 scenarios.
