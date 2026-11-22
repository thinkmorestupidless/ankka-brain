# 0013 — ankka-flow returns as engine-specific streamlets; it is not retired

## Status
Accepted 2026-11-22. Reverses ankka-flow's retirement, which was recorded on 2026-10-07 in a
note that was never pushed and is now dropped. Leaves ankka spec 037 standing. Amends 0004.

## Repos affected
- ankka-flow: not retired and not archived. Version two changes what a streamlet is: logic written
  against one engine's own API and run by that engine, where version one ran every streamlet as a
  process beside a sidecar that owned Kafka. Two engines to begin with, Apache Flink and Pekko
  Streams. The descriptor, typed inlets and outlets, blueprint verification, the operator's
  render and execute split and the `flow` CLI stay. The sidecar, the `ankka.flow.v1` protocol and
  the process SDKs are version one's and are not carried forward.
- ankka: unchanged in scope. Per-record, per-key work with durable state stays an entity, a view
  or a consumer, as spec 037 says. ankka-flow reads a project's declared topics and their
  contracts rather than keeping a second registry. ankka needs no change: to ankka a pipeline is
  a registered machine, and a project grants it topics, routes and gRPC methods as it grants any
  machine (`docs/platform/cross-project-access.md`).
- ankka-contrib: unchanged. The graph merge sink and the Neo4j store stay where 037 put them.
- cloudflow: unchanged, still frozen. This is its original model, rebuilt on the sidecar-free
  half of ankka-flow rather than on the fork.

## Context
ankka-flow version one was a set of streamlets between Kafka topics, each a process handed
batches by a platform sidecar. Spec 037 observed that such a streamlet and an ankka consumer over
a topic are the same machine, gave ankka the few things the consumer lacked, and ankka-flow was
marked for retirement. That reasoning holds for what version one was.

What it left out is what Cloudflow was for: streamlets whose logic runs on a stream engine, under
one set of contracts and one deploy. 037 says so itself, in its list of what is not carried:
"batches handed to a handler (an aggregation across messages belongs in an entity or a view)".
That is right per key and leaves no home for computation over sets and time: windows, event time
and late data, joins across streams, large keyed state, recomputation over history, and cleaning
or reshaping incoming data at volume before it reaches a project. None of that belongs in the
platform's core. All of it sits naturally between the topics ankka services already publish and
read, with the codecs already aligned.

No single use case forces this today. It is recorded as a judgement from experience that they
will come, and the first cut is kept small for that reason.

## Decision
1. **ankka-flow is the family's home for engine-run stream processing.** It runs beside ankka with
   its own operator, not as a workload kind inside it.
2. **Streamlets are engine-specific; the contract is not.** A streamlet is written against its
   engine's API and declares typed inlets and outlets in the shared descriptor. A blueprint wires
   streamlets of different engines over topics and is verified before anything runs. No
   abstraction is built over the engines' logic.
3. **The developer picks the engine per streamlet.**
   - Flink where the work needs time or state: windows, joins, event time, exactly-once.
   - Pekko Streams where it needs neither: stateless cleaning, filtering, reshaping, routing,
     enrichment by a call, ingestion from or delivery to a system outside Kafka. ankka-flow adds
     no windowing or state management to it; needing them is the signal to choose Flink.
   - An ankka consumer, not a flow, where the work is one service reacting to a topic.
4. **Results reach ankka two ways.** To a declared topic, checked against its contract; or by a
   direct call into a service, for an aggregate upserted into an entity under a key that makes a
   repeat harmless.
5. **Same repository and name.** The retirement commits were local to branch `008-ankka-topics`
   and never pushed; `main` and every published artefact say nothing of retirement. They were
   dropped on 2026-11-22. Feature 008 is absorbed into ankka-flow's
   `specs/009-engine-streamlets`: two of its three open questions are answered by what ankka now
   has (a topic's contract, a machine on the broker).

## Alternatives considered
1. **Stay retired; meet analytics with a sink into a columnar store.** Still right for a product
   whose aggregation the store can do alone, and nothing here prevents it. Lost as the general
   answer: it has no place for cleaning before a project, joins across streams, or logic that is
   not a query.
2. **One engine.** Flink alone covers the capability. Lost: a stateless stage does not justify a
   Flink job, and a second engine from the start is what keeps the seam between contract and
   execution honest.
3. **Portable logic, one streamlet run by any engine.** Lost: it is either a lowest common
   denominator over the engines or SQL only. Cloudflow never did this either.
4. **A workload kind inside ankka.** Lost: it brings the engines, their operators and their Scala
   lines into the platform's core.

## Consequences
- ankka-flow leaves the one Pekko line in part. The Pekko Streams runner stays on it. Flink
  streamlets are written against Flink's Java API and Flink's own dependency line; the versions
  are pinned in ankka-flow and are not mirrored from ankka.
- Flink brings its Kubernetes operator underneath ankka-flow's. Running an operator over another
  operator is where Cloudflow cost most; the plan must say who installs it and who upgrades it.
- Delivery differs by engine and the documentation must say so per engine: at least once with
  commit after write for Pekko Streams, as version one; Flink's checkpointed guarantees for Flink.
- A stateless Pekko Streams streamlet and an ankka consumer overlap by design. The line is drawn
  in decision 3 and belongs on ankka-flow's first concepts page and in ankka's topics page.
- Spark is not ruled out and not planned. It is the third engine if recomputation over history
  becomes the need.
- To correct once this is acted on:
  - [x] `repos/ankka-flow.md` and its row in `repos/README.md` (2026-11-22, with feature 009).
  - [x] `family.md`'s role line, and its docs tool pin and Pekko line (2026-11-22).
  - [ ] decision 0012's "ankka-flow: none. It is retiring".
  - [ ] ankka spec 037's title and context, which name the retirement (in ankka's checkout).
  - [x] the principle 1 check that Flink's dependencies carry no BSL licence: ankka-flow's
    research V9 (2026-11-22) found none, and no `com.typesafe.akka`; Flink's RPC jar embeds
    Apache Pekko 1.1.2.

## Related
- 0004 (ankka-flow succeeds cloudflow): its alternative 2 and this entry agree; what changes is
  what the "layer above" is.
- ankka `specs/037-pipelines/spec.md`
- ankka-flow `specs/009-engine-streamlets/spec.md`; `specs/008-ankka-topics/spec.md` on branch `008-ankka-topics`
