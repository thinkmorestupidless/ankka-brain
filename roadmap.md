# Roadmap

What is in flight across the family and what waits on what. Per-repo detail is in each repo's
`specs/` and its file under `repos/`. This page is only the cross-repo picture.

*As of 2026-09-30.*

## Version table

| Repo | Released | Branch in flight | Pins ankka |
|---|---|---|---|
| ankka | v0.9.1 | `018-judgments`, 36 of 37 tasks | |
| ankka-cloud | v0.1.0 (image never published) | none | 0.9.0 |
| ankka-deployments | n/a | `design-wasm-hosting`; PR #9 open | v0.6.4 |
| ankka-flow | v0.1.0 | `002-graph-merge-sink`, 42 of 43, not pushed | none |
| cloudflow | v0.1.0 (Akka line) | none | none |
| satisfactory | v0.1.0 | `architecture-diagram` | 0.7.1 snapshot |
| ankka-casino | unreleased | `main`, specs 001, 002, 012, 013 of 19 | none yet; needs 038 to 044 |

## Chains: what waits on what

**ankka-casino on ankka (added 2026-10-08)**
1. ankka specs 038 to 044 are written on ankka's `specs-038-044` branch, driven by
   ankka-casino's real-money needs: 038 secret store backends, 039 GCS object storage, 040
   cross-project access, 041 backups and recovery, 042 personal data erasure, 043 topic
   retention, 044 cloud provider. Pull request to open in ankka; none implemented.
2. Create `ankka-gcp` after 044 is planned: the Google cloud provider, holding the
   owner-equivalent IAM power the operator must not.
3. ankka-casino pins the ankka release that carries 038 to 044, records the original with
   `ankka-port`, and ports services in its build order. Detail in the eitheror brain.

**Getting ankka-cloud live on the hub**
1. Merge ankka-deployments PR #9 (release identity admits ankka-cloud), then `terraform apply` in `environments/ops/terraform`.
2. Re-run or re-push ankka-cloud `v0.1.0` so the image exists in `ankka-ops/ankka`.
3. Move arrakis from ankka v0.6.4 to a release at or above v0.8.0 (the quota route), preferably
   v0.9.1 to match what ankka-cloud embeds. One commit in ankka-deployments moving the tag and
   every image tag; the overlay should also switch to pulling public images through the ghcr cache.
4. Write the hub composition in ankka-deployments: `ankka-cloud` GitRepository at a tag, a
   Kustomization depending on `ankka-platform`, the `$patch: delete` of the platform's realm import,
   the four Secrets and the namespace from `platform-secrets`.
5. Decide the apex listener (`apex.yaml`, currently commented out) and Stripe live keys.

**Getting satisfactory back on releases**
1. Repin `V.ankka` to v0.9.1. Verified 2026-09-30: commit 0c2abd6 reached main by squash as PR #27 and is in v0.8.0 onward.
2. Repin the docs tool to `ankka@main` or a tag instead of the `shared-docs-tool` branch, which is not an ancestor of main.
3. Run tier 5 (T070) against a local kind ankka, then decide whether satisfactory deploys on arrakis.
4. Phase 5 (dedicated pods, bring-your-own model) waits on ankka growing `AnkkaJob` and machine credentials. ankka has no spec for it yet; satisfactory suggested `specs/008-jobs`.

**Finishing ankka 018 and cutting v0.10.0**
1. T036 release gate: full awake test run, `JevProviderLiveSuite` with a live key, quickstart by hand.
2. Open the PR, merge, tag. Commit or revert the dirty console package files first.
3. Out of scope for 018 and unowned: judgment client for workflows, endpoints and consumers; tool-call guardrail hook; sidecar and wasm support for judgments; console display.

**ankka-flow feature 002**
1. Push `002-graph-merge-sink`, finish T037 (tier 6 on kind beside ankka, recorded), PR, tag v0.2.0.
2. Next named: sidecar p90 latency (23 ms).
3. Waiting on ankka: the operator configuring Kafka for services in a cluster by default.

**ankka-deployments**
1. Backup story for CNPG before production traffic (Cloud SQL is the alternative).
2. Create the `dev` environment and fix its overlay, so the "arrakis runs only tags dev has run" rule can hold.
3. Regional control plane and node floor of three for production.
4. PR for `design/wasm-hosting.md` edits.

**cloudflow**
1. Write down its status: frozen for the eitheror consumer, new work in ankka-flow. Update `CLAUDE.md`'s "next on it" line. See `decisions/0004`.

## Things ankka's downstream has asked for
Collected from satisfactory's `notes/ankka-requests.md`, ankka-flow's research and ankka-cloud's
follow-ons, so they are in one place. None has an ankka spec yet.
- Run-to-completion workloads (`AnkkaJob`) and machine credentials
- A larger instance type; autoscaling on an application signal
- SSE `id:` frames and a gateway route timeout override on `HTTPRoute` (Envoy's 15 s default cuts streams)
- Request body limits and gzip in one place; more than two path parameters; application-chosen error bodies
- Serving `RuntimeExtension.routes`; a clock in `CommandContext`
- Operator configuring Kafka for services by default
- Per-installation scoping of the `platform-admin` credential; usage metering; hosting the console in ankka-cloud
