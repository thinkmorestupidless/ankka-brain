# ankka-flow

## One-liner
Streaming pipelines beside ankka: streamlets with typed inlets and outlets, wired by a blueprint
over Kafka topics, each streamlet's logic in any language in its own container, with a Pekko
sidecar beside it that owns everything Kafka.

## Why it exists
The sidecar model removes the reason most of Cloudflow's structure existed: streamlets discovered
by scanning a JVM classpath, images built by sbt and Maven plugins, a runtime compiled into every
image, and the Scala 2.12 build tree. So it started fresh in its own repository (clarified
2026-09-28) rather than as a further phase of the cloudflow fork, which stays as it is for its
existing consumers. Six pieces were carried over from the fork by copy, each with its test.

Where ankka ends and ankka-flow begins: a single service reacting to a topic is an ankka
consumer. ankka-flow is the layer above, a graph of stages with contracts verified before anything
runs, fan-out, platform-owned topics, per-key ordering through a chain, rebuild from the start of
inputs, lag per stage, and sinks that commit after writing elsewhere. If a design needs none of
those, it is not a flow.

## Status
- Stage: released; feature 002 (Neo4j merge sink, the first built-in stage) in flight
- As of: 2026-09-30
- Current release: `v0.1.0` (tag at ddf3ca5, 2026-09-28)
- Visibility: public

## Where it lives
- Repo: github.com/thinkmorestupidless/ankka-flow
- Local: ~/Documents/projects/thinkmorestupidless/ankka-flow
- Publishes: GHCR images `ankka-flow-sidecar`, `ankka-flow-operator`, `sample-cart-router`,
  `sample-checkout-feed`, `sample-checkout-graph`; PyPI package `ankka-flow` (trusted publishing);
  Claude Code plugin `ankka-flow` pushed into `thinkmorestupidless/ankka-marketplace`; docs at
  flow.ankka.cloud. No Maven artifacts; every sbt module is `publish / skip`
- Runs: any Kubernetes cluster with Kafka; proven on kind beside ankka (tier 7)

## Depends on / depended on by
- **Runs beside `ankka`**, not on it. ankka services are visible to a pipeline only as Kafka
  topics (unmanaged topics such as `cart-checkouts`). Follows ankka's conventions so people move
  between the two: operator render/execute split, `protocol/` directory SDKs copy, conformance
  suite, `FLOW_*` env vars mirroring `ANKKA_*`, ports 9010/9011, CRD group under ankka's domain.
- **Consumes ankka's docs tool** from `git+https://github.com/thinkmorestupidless/ankka@main#subdirectory=tools/docs`.
- **Needed one change in ankka**: `ProjectionRuntime.fromEnv()` reading `ANKKA_KAFKA_BOOTSTRAP_SERVERS`, for tier 7. ankka's operator still configures no Kafka for services in a cluster by default.
- **Descends from `cloudflow`**: blueprint verification, JSON contracts by schema name,
  commit-after-write, consumer-group reset, topic creation, client-id naming and Prometheus rules.
  Lightbend headers kept, listed in `NOTICE`. Neither is a dependency of the other.
- **Must not break**: the Python SDK's copied `protocol/` (CI diffs byte for byte); PyPI versions
  (immutable); the marketplace plugin (never force-pushed); the Pages site.

## Read first
1. `README.md`: laptop and Kubernetes quickstart.
2. `CLAUDE.md`: commands, the rules table, docs rules, carried-code and manifest conventions, env vars, the "not in this build" list.
3. `specs/001-version-one/spec.md` then `research.md`: what version one does; R1 to R16 and what implementation changed.
4. `specs/001-version-one/contracts/`: protocol, descriptor, sidecar, resource and operator, CLI, Python SDK, conformance.
5. `specs/002-graph-merge-sink/`: the in-flight feature.
6. `protocol/README.md`, `protocol/DESCRIPTOR.md`: the wire rules SDKs copy.
7. `docs/concepts/` and `docs/reference/limitations.md`.

## Conventions specific to this repo
- Not in this build, by rule: pekko-http, pekko-grpc, Avro, spray-json, ScalaTest. Adding one means updating `research.md` first.
- gRPC via grpc-java plus ScalaPB, not pekko-grpc. Protocol version `1.0`.
- Carried code keeps its Lightbend header verbatim, brings its test as munit, and is added to `NOTICE`.
- Manifests canonical in `kustomization/components/`; JVM resources are symlinks into them.
- Deliberate departure from ankka: the operator writes Kubernetes Events (`PartitionStalled`, `Refused`, `TopicDiffers` and others). ankka writes none.
- Release: push a `v*` tag; images, then PyPI, then marketplace. Python `__version__` and plugin version are `0.0.0` in the tree and written from the tag.

## Open questions
- 2026-09-30: feature 002 task T037 open: run quickstart tier 6 on kind beside ankka and record it.
- 2026-09-30: branch `002-graph-merge-sink` has no upstream; 12 commits ahead of main, not pushed.
- 2026-09-30: sidecar p90 latency (23 ms) named "the next thing to work on".
- 2026-09-30: `ConstraintNotCreated` path verified by inspection only; no automated k3s case observes a stall Event.
- 2026-09-30: constitution never filled; plan 001 said to run it before the next feature and it was not.
- Limitations page: at-least-once only, JSON only, one SDK, one built-in stage, Neo4j 5.26+ write-only, no ingress, Kafka only, JVM CLI, records under 4 MiB.

## Recent moves
- 2026-09-29: feature 002 implemented end to end on the branch: driver, built-in descriptor, CLI and operator built-ins, stage, metrics, k3s scenario, `checkout-graph` sample, docs, skills, benchmark (5,345 deltas/s against a 1,000 floor). 42 of 43 tasks done.
- 2026-09-28: `v0.1.0`. PR #3: tier 7 beside ankka, SDK on PyPI, no gRPC keepalive. PR #4: `flow generate --delete-managed-topics`. Version one benchmarked at 3,500 to 5,640 records/s per partition with 6 to 7 ms added latency.
- 2026-09-28: version one specified and implemented in one day (PR #1), docs site and skills (PR #2).
