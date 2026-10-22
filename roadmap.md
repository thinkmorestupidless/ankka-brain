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
| ankka-reasoning | 0.1.1 | `002-process-layer`, all seven phases merged to `main` (2026-10-22), release 0.2.0 owed | 0.12.0; ankka-contrib's sink image 0.1.0 (no ankka-flow) |
| ankka-contrib | v0.1.0 (2026-10-08) | none | 0.11.0 |
| ankka-predictions | unreleased | `main` only; specs 004 to 006 drafted (2026-10-22) | not yet pinned |

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
4. Added 2026-10-10: ankka-flow's capabilities become ankka feature 037; the Neo4j sink is ankka-contrib's. Done 2026-10-18: ankka-reasoning moved to ankka-contrib's sink image (spec 002, phase 1) and pins nothing of ankka-flow.

**ankka-reasoning (added 2026-10-06, refreshed 2026-10-21)**
1. Done: ankka 0.12.0 carries `ankka-auth-oidc`, the single-file `GherkinSuite` and the blueprints; the service pins it. Done: moved to ankka-contrib's sink image; nothing of ankka-flow is pinned.
2. Spec 002, the process layer, is built and merged to `main` (phases 1 to 7, PRs #7 to #14, last merged 2026-10-22). Next a release (0.2.0) with the process layer and four library modules; the release job's module check was widened to four in phase 7.
3. Asks ankka for the worker in `RunRef`, tools with more than three parameters, a tool that fails its run, a judgment provider over a text model, `RunEntity.start` answering the started state, and the `refresh-interval` setting; asks ankka's graph sink for a way to ask whether elements have been applied; asks ankka-contrib for an `arm64` sink image (`notes/ankka-requests.md`, 6, 10, 14 to 19).
4. Not yet run: tiers 4 (by hand on a laptop) and 5 (a local kind installation) of spec 002's quickstart; the live suite ten times with keys (SC-007).

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
