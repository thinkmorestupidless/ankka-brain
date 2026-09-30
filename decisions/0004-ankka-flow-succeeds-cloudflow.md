# 0004 — ankka-flow succeeds cloudflow; cloudflow is frozen for its consumers

## Status
Accepted 2026-09-28 (recorded 2026-09-30)

## Repos affected
- cloudflow: no new features. Phases 3 and 4 of its ROADMAP are not going to happen there. Patches
  for the 0.1.0 Akka line branch from `v0.1.0`; the Pekko `master` stays unreleased unless a
  consumer asks.
- ankka-flow: the home of all new pipeline work. Carries Cloudflow pieces by copy only, with
  Lightbend headers and tests, listed in `NOTICE`.
- ankka: pipelines over its topics are ankka-flow's concern; the operator should eventually
  configure Kafka for services by default.

## Context
Cloudflow's Phase 2 delivered what ankka's pipelines needed on the JVM. The next step, streamlet
logic in any language, would have meant fighting Cloudflow's structure: classpath scanning, sbt
and Maven image plugins, a runtime in every image, the Scala 2.12 tree. A sidecar that owns Kafka
removes the reason that structure exists.

## Decision
A new project, `ankka-flow`, in its own repository, with a Pekko sidecar per pod and the process
in any language. The fork stays as it is for the consumers it has. Neither depends on the other.

## Alternatives considered
1. **Phase 3 and 4 inside cloudflow.** Lost: pre-deprecation baggage to maintain forever.
2. **Pipelines inside ankka.** Lost: a single service reacting to a topic is already an ankka
   consumer; the layer above (verified graphs, fan-out, owned topics, reset, lag per stage) is a
   different product with a different lifecycle.

## Consequences
- cloudflow's `CLAUDE.md` still says graph pipelines are "next on it". It should say this instead.
- The graph projection cloudflow's Phase 4 planned was built as ankka-flow feature 002.
- Two repos share the word "streamlet" with slightly different semantics; the glossary notes both.

## Related
- ankka-flow `specs/001-version-one/spec.md` Clarifications 2026-09-28
- cloudflow `ROADMAP.md` decision 2 (consumers stay on 0.1.0)
