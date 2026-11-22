# ankka-flow

## One-liner
Streaming pipelines beside ankka: streamlets with typed inlets and outlets, wired by a blueprint
over Kafka topics, each written against one engine's own API and run by that engine, Pekko
Streams or Apache Flink.

## Why it exists
The family's home for engine-run stream processing (decision 0013): windows, event time, joins,
keyed state and recomputation over history, and cleaning or reshaping data at volume before it
reaches a project. None of that belongs in ankka's core, and all of it sits between the topics
ankka services already publish and read.

Where ankka ends and ankka-flow begins: a single service reacting to a topic is an ankka
consumer. A stage that needs neither windows nor state is a Pekko Streams streamlet; one that
needs either is a Flink streamlet.

Version one (to v0.5.0) ran every streamlet as a process beside a Pekko sidecar that owned Kafka.
Feature 009 removed the sidecar, the `ankka.flow.v1` protocol, the Scala and Python process SDKs,
the built-in graph sink and `flow init`; the descriptor, blueprint verification, the operator's
render and execute split and the `flow` CLI stayed.

## Status
- Stage: released; feature 009 (engine streamlets) merged to `main` (#20, 2026-11-23), version
  two unreleased
- As of: 2026-11-23
- Current release: `v0.5.0` (2026-10-07), version one. Version two is unreleased
- Visibility: public

## Where it lives
- Repo: github.com/thinkmorestupidless/ankka-flow
- Local: ~/Documents/projects/thinkmorestupidless/ankka-flow
- Publishes, from version two: Maven Central `ankka-flow-descriptor_3`,
  `ankka-flow-pekko-streams_3`, `ankka-flow-flink`, `ankka-flow-flink-testkit`; GHCR images
  `ankka-flow-operator`, `sample-cart-cleaner`, `sample-cart-counter`; the native `flow` binaries
  and the shared Homebrew tap's formula; the Claude Code plugin `ankka-flow` (four skills) pushed
  into `thinkmorestupidless/ankka-marketplace`; docs at flow.ankka.cloud
- Runs: any Kubernetes cluster with Kafka, the Apache Flink Kubernetes Operator and an object store
  for Flink's state; proven on k3s and kind

## Depends on / depended on by
- **Runs beside `ankka`**, not on it. A blueprint names an ankka project's topic as
  `ankka = "<project>/<name>"`, checks it against the project's declared contracts
  (`flow verify --ankka-topics`), and reaches it as a registered machine the project grants. ankka
  needed no change for it.
- **Consumes ankka's docs tool** at tag `v0.18.0` (`tools/docs/pyproject.toml`, `uv.lock`).
- **Runs over the Flink Kubernetes Operator** (1.16.1, Flink 2.2.1), installed first; ankka-flow's
  operator renders a `FlinkDeployment` for each Flink streamlet.
- **Descends from `cloudflow`**: blueprint verification, JSON contracts by schema name,
  commit-after-write, consumer-group reset, topic creation, client-id naming and Prometheus rules.
  Lightbend headers kept, listed in `NOTICE`.
- **Consumed by nothing in the family.** ankka-reasoning moved to ankka-contrib's sink image on
  2026-10-18 and pins nothing of ankka-flow, so removing version one stranded no consumer.
- **Must not break**: the descriptor fixtures both SDKs reproduce byte for byte; Maven Central
  versions (immutable); the marketplace plugin and the Homebrew tap (never force-pushed).

## Read first
1. `README.md`: the two-engine sample, on a laptop and on Kubernetes.
2. `CLAUDE.md`: commands, the rules table, docs rules, carried code, manifests, env vars.
3. `specs/009-engine-streamlets/spec.md`, then `plan.md` and `research.md`: behaviour, decisions,
   and what implementation found.
4. `specs/009-engine-streamlets/contracts/`: descriptor, streamlet settings, resource and
   operator, CLI.
5. `features/` and `GLOSSARY.md`: the living features.
6. `docs/concepts/engines.md` and `docs/reference/limitations.md`.

## Conventions specific to this repo
- Not in this build, by rule: pekko-http, pekko-grpc, Avro, spray-json, ScalaTest.
- Leaves the one Pekko line in part (decision 0013): the Pekko Streams SDK is on it (Pekko
  1.7.1); the Flink SDK, its testkit and the counter sample are Java on Flink's own line,
  compiled `--release 17`, and are not mirrored from ankka.
- Two Scala versions: the published Scala modules compile with Scala 3.3 LTS so any Scala 3.3+
  project can depend on them; the rest is on 3.9.
- Carried code keeps its Lightbend header verbatim, brings its test as munit, and is in `NOTICE`.
- Deliberate departure from ankka: the operator writes Kubernetes Events and status conditions
  (`StateUnreadable`, `TopicNotGranted` and others).

## Open questions

## Recent moves
- 2026-11-23: CI on ankka's pattern (#21): pull requests only, `main` protected with checks up to date,
  a filter coverage check, the k3s suite nightly (`cluster.yml`), the site published by a release;
  every Scala warning an error (#22).
- 2026-11-23: feature 009 merged (#20); the native binary built and tested by its PR's `cli-native`.
- 2026-11-22: feature 009 built: engine streamlets, two SDKs, Flink under its operator with a
  state store and epochs, ankka topics as a machine, version one removed.
- 2026-11-22: decision 0013 reverses the retirement noted on 2026-10-07.
- 2026-10-07: `v0.5.0`, version one's last release.
