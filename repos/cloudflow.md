# cloudflow

## One-liner
A maintained fork of Lightbend Cloudflow, which upstream is deprecated: streamlets wired by a
blueprint, packaged as images, run on Kubernetes under its operator with Kafka between stages.
Now on Apache Pekko.

## Why it exists
ankka services publish their events to Kafka, and a central eventually consistent graph should be
built from those topics by streaming pipelines rather than a hand-written service. Cloudflow was
the right shape, but the fork ran on commercial Akka with a licence key injected into every
pipeline pod, and ankka exists on Pekko precisely so that nothing on its path carries a BSL
licence. So the plan was: port Cloudflow to Pekko, make it fit to consume ankka's topics, then
build the graph pipelines on it. Phases 0 to 2 were done. Phases 3 and 4 (run beside ankka, graph
projection) were never started here; that work was built in ankka-flow instead.

## Status
- Stage: maintenance. Frozen for its existing consumers; new pipeline work goes to ankka-flow
- As of: 2026-09-30
- Current release: `v0.1.0` (the last Akka commit, 9a6db01b; tag is on origin). Everything after
  it on `master` is the Pekko line and has never been released
- Visibility: public

## Where it lives
- Repo: github.com/thinkmorestupidless/cloudflow
- Local: ~/Documents/projects/thinkmorestupidless/cloudflow
- Publishes: Maven artifacts under `com.lightbend.cloudflow` to GitHub Packages at
  `maven.pkg.github.com/thinkmorestupidless/cloudflow`; operator image
  `ghcr.io/thinkmorestupidless/cloudflow-operator`. Only 0.1.0 has ever been published
- Runs: its existing consumers' clusters (see below)

## Depends on / depended on by
- **Consumers pin 0.1.0**, the Akka line: `sbt-cloudflow` and Cloudflow `0.1.0` from GitHub
  Packages, operator image `0.1.0`. Never re-tag `v0.1.0`, never publish by hand, and a patch for
  that line branches from `v0.1.0`, never `master`. Moving them to Pekko is a migration, not an
  upgrade; the rename list is in commit 6815ec71. The consumer is the eitheror platform (branches
  `eitheror-platform-integration`, `eitheror-integration`), which is the other company's product.
- **Same Pekko line as `ankka`**: pekko 1.7.0, pekko-http 1.4.0, pekko-connectors-kafka 1.2.0,
  pekko-management 1.2.1, kafka-clients 3.9.2. Pinned whole via `pekkoFamilyOverrides`.
- **Ancestor of `ankka-flow`**, which carried six pieces by copy. Not a dependency in either direction.

## Read first
1. `ROADMAP.md`: the plan, phases with checkboxes, and the decisions table at the end. Read before changing anything structural.
2. `CLAUDE.md`: consumers and what must not break, build rules, versions and releases, what still says Akka, surprising behaviours.
3. `README.md`: the fork summary at the top, then upstream's README.
4. `core/project/Dependencies.scala`, `core/project/Common.scala`: versions and publish target.
5. `.github/workflows/release.yml`: the release gate (refuses unless dynver version equals the tag).

## Conventions specific to this repo
- No spec-kit. `ROADMAP.md` is the plan and the decision record.
- Build from `core/` with `sbt -mem 4096`. Scala 3.3.5 for most modules, 2.12 for the sbt-plugin chain, blueprint cross-built.
- Images built by Cloudflow's own `DockerImageBuild`, not sbt-docker; base image must be Alpine.
- CRD group stays `cloudflow.lightbend.com`; changing it orphans every existing resource. Organization stays `com.lightbend.cloudflow`.
- A handful of "Akka" and "nakka" mentions are history and stay.

## Open questions
- 2026-09-30: no explicit "retired" statement exists in the repo; CLAUDE.md still names graph pipelines as "next on it". Reconcile with ankka-flow's existence (see `../decisions/0004-ankka-flow-succeeds-cloudflow.md`).
- Phase 0 leftovers: native CLI job, JMX agent upgrade from 0.11.0, operator image still on Java 11 base.
- Phase 1 leftovers: docs prose still says Akka, two broken Avro doc includes, test-source headers.

## Recent moves
- 2026-09-20: PR #15, Kafka clients named after streamlet and port so lag can be attributed. Last commit; untouched since ankka-flow began.
- 2026-09 (PRs #10 to #14): Phase 2 complete: `Record[T]` with key and headers, `cloudflow-json` codec, unmanaged topics, operator-driven `reset-offsets`, `sinkCommittingAfter` with `OffsetFirstObserved`.
- 2026-09 (PR #9): Phase 1, the Pekko port, every Akka name renamed including wire names; Lightbend resolvers, tokens and licence removed.
- 2026-09 (PR #8): Phase 0, tag-driven releases, `v0.1.0` frozen as the Akka line, Java 25 base image.
