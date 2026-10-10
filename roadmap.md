# Roadmap

What is in flight across the public family and what waits on what. Per-repo detail is in each
repo's `specs/` and its file under `repos/`. This page is only the cross-repo picture. The private
members' chains (the hosted product going live, the installations, the downstream products) are in
`ankka-brain-private/roadmap.md`.

*As of 2026-09-30, except where a line is dated later.*

## Version table

| Repo | Released | Branch in flight | Pins ankka |
|---|---|---|---|
| ankka | v0.9.1 | `018-judgments`, 36 of 37 tasks | |
| ankka-flow | v0.1.0 | `002-graph-merge-sink`, 42 of 43, not pushed | none |
| cloudflow | v0.1.0 (Akka line) | none | none |
| satisfactory | v0.1.0 | `architecture-diagram` | 0.7.1 snapshot |
| ankka-reasoning | 0.1.1 | none | 0.10.0; ankka-flow 0.3.0 |
| ankka-contrib | v0.1.0 (2026-10-08) | none | 0.11.0 |

Known to be behind as of 2026-10-10: ankka is at v0.11.0 with specs 038 to 048 merged or in pull
requests; ankka-flow is at v0.5.0 and its README calls it deprecated, its capabilities becoming
ankka feature 037 and the Neo4j sink moving to ankka-contrib. Refresh the rows above when touched.

## Chains: what waits on what

**Getting satisfactory back on releases**
1. Repin `V.ankka` to v0.9.1 or later. Verified 2026-09-30: commit 0c2abd6 reached main by squash as PR #27 and is in v0.8.0 onward.
2. Repin the docs tool to `ankka@main` or a tag instead of the `shared-docs-tool` branch, which is not an ancestor of main.
3. Run tier 5 (T070) against a local kind ankka, then decide whether satisfactory deploys on a hosted installation.
4. Phase 5 (dedicated pods, bring-your-own model) waits on ankka growing `AnkkaJob` and machine credentials. ankka has no spec for it yet; satisfactory suggested `specs/008-jobs`.

**Finishing ankka 018 and cutting v0.10.0**
1. T036 release gate: full awake test run, `JevProviderLiveSuite` with a live key, quickstart by hand.
2. Open the PR, merge, tag. Commit or revert the dirty console package files first.
3. Out of scope for 018 and unowned: judgment client for workflows, endpoints and consumers; tool-call guardrail hook; sidecar and wasm support for judgments; console display.

**ankka-flow feature 002, then retirement**
1. Push `002-graph-merge-sink`, finish T037 (tier 6 on kind beside ankka, recorded), PR, tag v0.2.0.
2. Next named: sidecar p90 latency (23 ms).
3. Waiting on ankka: the operator configuring Kafka for services in a cluster by default.
4. Added 2026-10-10: ankka-flow's capabilities become ankka feature 037; the Neo4j sink is ankka-contrib's. ankka-reasoning still pins ankka-flow's sidecar image and should move.

**ankka-reasoning (added 2026-10-06)**
1. Needs an ankka release after 0.10.0 for `ankka-auth-oidc` and a single-file `GherkinSuite`; both on ankka's `main`.
2. Needs ankka-flow's sidecar package on GHCR made public, or to move to ankka-contrib's sink.
3. Asks ankka for a `refresh-interval` setting of its own; asks the siblings for a project namespace before the first service and a pipeable `flow generate`.

**cloudflow**
1. Write down its status: frozen for its existing consumer, new work in ankka-flow and now ankka. Update `CLAUDE.md`'s "next on it" line. See `decisions/0004`.

## Things ankka's downstream has asked for
Collected from satisfactory's `notes/ankka-requests.md`, ankka-flow's research and
ankka-reasoning's `notes/ankka-requests.md`, so they are in one place. None had an ankka spec as of
2026-09-30; check `ankka/specs/` before assuming that still holds.
- Run-to-completion workloads (`AnkkaJob`) and machine credentials
- A larger instance type; autoscaling on an application signal
- SSE `id:` frames and a gateway route timeout override on `HTTPRoute` (Envoy's 15 s default cuts streams)
- Request body limits and gzip in one place; more than two path parameters; application-chosen error bodies
- Serving `RuntimeExtension.routes`; a clock in `CommandContext`
- Operator configuring Kafka for services by default
- A projection refresh interval as a setting; a project namespace before its first service
