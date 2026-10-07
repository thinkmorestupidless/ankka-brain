# ankka-reasoning

## One-liner
A reasoning graph on ankka: evidence, claims, hypotheses and beliefs as records that are never
edited, published as graph deltas for ankka-flow's merge sink to keep in a graph database.

## Why it exists
ankka's graph consumer and ankka-flow's merge sink copy a service's entities into Neo4j. That is
the pipe, not the graph: nothing said what a reasoning graph holds, who owns a link, how history is
kept or how an answer is read out. It is its own repo because it is an ankka application and a
domain, not platform, and what it cannot do becomes a request to ankka or ankka-flow.

Three decisions (2026-10-06): a prediction market is the first domain; the vocabulary is layered
(belief, then market, with a process layer later); history is kept by immutable revisions.

## Status
- Stage: released (first feature on `main`; first full release 0.1.1, 2026-10-06)
- As of: 2026-10-06
- Current release: 0.1.1 (0.1.0 is an image and a release page only: its run failed before the libraries)
- Visibility: public

## Where it lives
- Repo: github.com/thinkmorestupidless/ankka-reasoning
- Local: ~/Documents/projects/thinkmorestupidless/ankka-reasoning
- Publishes: documentation to `https://reasoning.ankka.cloud/` (GitHub Pages, from `main`). On a `v*` tag, by `release.yml`: `ankka-reasoning-{graph,belief,market}_3` to Maven Central, the image `ghcr.io/thinkmorestupidless/ankka-reasoning`, the plugin to ankka-marketplace, and a release page with the service descriptor and blueprint attached. First published at 0.1.1
- Runs: on a laptop (`just up`, `just run`), and on the local kind installation as project
  `reasoning` (release 0.1.1, deployed 2026-10-07). Never on a cloud installation

## Depends on / depended on by
- **An ankka application** (Scala 3.9.0), pinned to **ankka 0.10.0** from Maven Central: entities,
  graph consumers, views, endpoints and the test kits. Agents come with a later feature.
- **Runs beside an ankka-flow pipeline** that is the built-in Neo4j merge sink alone, over one
  compacted delta topic the pipeline declares. Pinned to **ankka-flow 0.3.0**'s sidecar image.
  Bound by `ankka.graph-delta.v1`: whole state, one writing entity per element, plain property
  values.
- **Needs from outside the family**: Kafka and Neo4j 5.26+, supplied by whoever deploys. ankka has
  no graph client, so the service brings the Neo4j Java driver for reads.
- Nothing depends on it.

## Read first
1. `README.md` and `docs/index.md` (the site's home, https://reasoning.ankka.cloud/): what it is and
   the example every page and feature shares.
2. `CLAUDE.md`: the seven rules.
3. `specs/001-belief-layer/spec.md`: the first feature, with the link table.
4. `features/` and `GLOSSARY.md`: the scenarios the spec names and their words.

## Conventions specific to this repo
- Living features from spec 001 on (no `specs-from`).
- Five sbt modules, `graph` ← `belief` ← `market` ← `service` and `seed` apart: the layering is
  the dependency direction.
- Every record that carries text a writer supplied is a key value entity, never an event, because
  ankka never removes an event and text has to be withdrawable. Only a belief's line is event
  sourced.
- Requests to the platform are kept in `notes/ankka-requests.md`, each with what it would remove.
- Docs as in the family: `docs/` built by ankka's docs tool (`ankka-docs`, branch `main`), one
  skill `ankka-reasoning` rendered into `marketplace/plugins/ankka-reasoning/`, pushed to
  ankka-marketplace by the release.
- Release as in the family: a tag is the only thing that publishes; the version is dynver's. The
  image is named `ankka-reasoning`; the service it runs is `reasoning`.

## Open questions
- 2026-10-06: **needs an ankka release after 0.10.0** for `ankka-auth-oidc` (a person's token; the
  gateway is refused with 401 until then) and a `GherkinSuite` that takes one file. Both are on
  ankka's `main`.
- 2026-10-06: **ankka-flow's sidecar package on GHCR is private**, so the sink's image is built from
  the `v0.3.0` tag: by hand on a laptop, and by two steps in this repo's CI that no pull request has
  yet run.
- 2026-10-06: the service sets Pekko's `refresh-interval` to 1s itself to meet its five-second
  time to the graph; asked of ankka as a setting of its own.
- 2026-10-07: deployed to the local kind installation (release 0.1.1), end to end; never to a
  cloud one. Found: a project's namespace exists only after its first service is applied, and
  `flow generate` 0.4.0 cannot be piped to `kubectl` (its note is on stdout); both asked of the
  siblings in `notes/ankka-requests.md`.
- 2026-10-06: prior art is another company's repository, not a dependency. The spec carries its
  lessons as rules and names nothing from it.

## Recent moves
- 2026-10-07: tier 5 run on kind: the service as `service:shoppingcart/cart`'s writer recorded the
  launch example and explained it through the cluster's Kafka, sink and Neo4j.
- 2026-10-06: 0.1.1 released: the three libraries are on Maven Central and the image is on
  ghcr.io. Still to do by hand: make the GHCR package public, and give `MARKETPLACE_REPO_TOKEN`
  write access to ankka-marketplace, then re-run the release's `marketplace` job, which has failed
  twice with a 403.
- 2026-10-06: release workflow added. It needs five repository secrets (`PGP_SECRET`,
  `PGP_PASSPHRASE`, `SONATYPE_USERNAME`, `SONATYPE_PASSWORD`, `MARKETPLACE_REPO_TOKEN`), all set,
  and the GHCR package made public after its first push.
- 2026-10-06: Apache 2.0 licence added; the nine glossary terms that were *Proposed.* accepted.
- 2026-10-06: pull request 1 merged: spec 001 and the documentation site, which is live at
  reasoning.ankka.cloud. Pull request 2 adds the release workflow.
- 2026-10-06: documentation site added: 24 pages under `docs/`, `mkdocs.yml`, `tools/docs`, the
  `docs` workflow and one skill. Pages was already enabled with the custom domain.
- 2026-10-06: spec 001 implemented: 76 tasks, 75 done (the on-cluster walkthrough is not), 119
  scenarios each run by a suite, a seeded set of 555 records. Measured: nine in ten records in the
  graph within 3.8 s; an explanation over a 1,000-record question in 14 ms.
- 2026-10-06: repository directory created with spec-kit 0.14.1 and speckit-bdd 0.2.0; spec 001
  (the belief layer) written, clarified, planned and tasked.
