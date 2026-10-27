# 0002 — Pekko, not Akka, and one Pekko line across the family

## Status
Accepted (predates this log; recorded 2026-09-30)

## Repos affected
ankka, cloudflow, ankka-flow, and by extension every ankka application (satisfactory, ankka-reasoning, the hosted product).
No repo may add a dependency that reintroduces a BSL licence on the path a customer runs. Pekko
family versions are bumped in ankka first and mirrored elsewhere.

## Context
ankka exists to offer Akka's component model with no licence constraint on who runs it or what
they run on it. Cloudflow, forked to build graph pipelines over ankka's topics, ran on commercial
Akka with a licence key injected into every pipeline pod.

## Decision
Apache Pekko everywhere on the JVM: pekko 1.7.0, pekko-http 1.4.0, pekko-connectors-kafka 1.2.0,
pekko-management 1.2.1, kafka-clients 3.9.2, pinned whole via family overrides in each build.
Cloudflow was ported to Pekko (its Phase 1); ankka-flow started on Pekko. ankka's documentation
listed every deliberate departure from Akka until 2026-10-27, when the page was retired and the
platform was described on its own terms.

## Alternatives considered
1. **Commercial Akka with a licence key.** Lost: the constraint transfers to every customer.
2. **Akka 2.6 frozen.** Lost: unmaintained; Pekko is the maintained continuation.

## Consequences
- Every JVM repo shares one version line. A Pekko bump is a family change, not a repo change.
- ankka-flow additionally excludes pekko-http and pekko-grpc by rule (its R1); that is a repo
  decision, not a family one.
- cloudflow's frozen 0.1.0 line is the one place Akka survives, for an existing consumer.

## Related
- ankka `README.md` "Why Pekko"; `project/Dependencies.scala`
- cloudflow `ROADMAP.md` Phase 1 and decision 3
- `0004-ankka-flow-succeeds-cloudflow.md`
