# satisfactory

## One-liner
Constraint solving as a service, built on Timefold Solver and deployed as ankka services. Submit
a planning problem, get back a solution, or follow the stream of improving ones.

## Why it exists
Two facts shape it. A Timefold problem is code plus data, so the service ships a model catalog
behind a Java SPI rather than a generic constraint wire format. And ankka has services, not jobs,
so solving is a pull-based runner pool rather than a pod per solve. It is deliberately a separate
product from ankka: there is no `Solver` component in ankka, ankka never depends on Timefold, and
an ankka agent reaches satisfactory through generated tools (declined in `DESIGN.md` §11.2b,
2026-09-22). It is also the first real ankka application, consuming ankka's docs tool, marketplace
and deploy story as a downstream user would. The public API reimplements the Timefold Platform
model API, with every divergence listed and justified, the same adopt-then-diverge method ankka
applied to Akka.

## Status
- Stage: released; phases 0 to 4 of the build order implemented, 110 of 111 tasks done
- As of: 2026-09-30
- Current release: `v0.1.0` (tag at commit 7ca2768, 2026-09-29)
- Visibility: public

## Where it lives
- Repo: github.com/thinkmorestupidless/ankka-satisfaction (renamed from `satisfactory` on 2026-10-19; the old URL redirects). The product, its artifacts, images, config prefix and docs host keep the name `satisfactory`
- Local: ~/Documents/projects/thinkmorestupidless/satisfactory
- Publishes: `com.thinkmorestupidless` artifacts `satisfactory-protocol`, `satisfactory-model-spi`,
  `satisfactory-client`, `ankka-satisfactory` via `sbt ci-release`; images `satisfactory-api`,
  `satisfactory-solver`; docs at satisfactory.ankka.cloud (GitHub Pages); Claude Code plugin
  `satisfactory` pushed into `thinkmorestupidless/ankka-marketplace` on `v*` tags
- Runs: any ankka installation, as project `satisfactory` with services `api` (exposed, 3 x
  `small`) and `solver` (3 x `large`, one slot each). Not deployed on a hosted installation as of 2026-09-30

## Depends on / depended on by
- **Consumes `ankka`** libraries `ankka-{sdk,runtime,http,agent,testkit}`. Pinned to a locally
  published snapshot `0.7.1+28-ed931dc0+20260928-1802-SNAPSHOT` in `project/Dependencies.scala`
  because it needs ankka feature 014 (certificate-authenticated databases, mutual TLS, commit
  0c2abd6) which 0.7.1 predates. Verified 2026-09-30: that branch was squash-merged as ankka
  PR #27 with an identical tree and is in v0.8.0, v0.9.0 and v0.9.1. Repin to v0.9.1.
- **Consumes ankka's docs tool** from the `shared-docs-tool` branch of ankka, not a tag.
- **Asks of ankka**, in the order it will hurt (`DESIGN.md` §8, `notes/ankka-requests.md`):
  run-to-completion workloads (`AnkkaJob`) plus machine credentials; a larger instance type;
  autoscaling on queue depth; SSE `id:` frames; body limits and gzip in one place; more than two
  path parameters; application-chosen error bodies; serving `RuntimeExtension.routes`; a clock in
  `CommandContext`. Phase 5 (dedicated pods, bring-your-own model) is blocked on `AnkkaJob`.
- **Direction rule**: ankka never depends on satisfactory; `protocol`, `client`, `model-spi`,
  `models` and `runner` never depend on ankka.

## Read first
1. `README.md`: the shape in five lines, module table, run it in one process.
2. `CLAUDE.md`: dependency direction, the three fold rules, docs rules, "things that are not what they look like".
3. `DESIGN.md`: how it works, what it asks of ankka (§8), build order (§10), declined routes (§11), three artefacts (§12).
4. `API.md`: the public contract, the "as built" note, divergences (§8), open items (§9).
5. `specs/001-constraint-solving-service/`: spec, plan, research with the V-list, tasks, quickstart with six test tiers.
6. `notes/ankka-requests.md`: what it wants from ankka and what it did instead.
7. `docs/concepts/architecture.md`, `docs/concepts/workers-and-leases.md`, `docs/deploy/deploy-on-ankka.md`.

## Conventions specific to this repo
- `.scalafmt.conf` copied from ankka. Constitution check in `plan.md` adopts ankka's `CLAUDE.md`
  principles; `.specify/memory/constitution.md` is still the unfilled template.
- Tests run one at a time (`Tags.limit(Tags.Test, 1)`); do not optimise this back.
- Models and the SPI are Java, because Timefold reads annotations off bean classes.
- Community licence only: `maxThreadCount` capped at 1, own throttle, score analysis by ablation.
  "Timefold" is their trademark; the product is "built on Timefold Solver", headers are `X-Satisfactory-*`.
- Docs pages tell no history (no feature numbers, no "decided on"); samples are included from tested code.

## Open questions
- 2026-09-30: `V.ankka` snapshot pin is stale; v0.8.0 or later carries what it waits for.
- 2026-09-30: T070 open, tier 5 deploy on a local ankka; a local kind cluster would switch the kubectl context away from the production cluster.
- 2026-09-30: Envoy's default 15 s route timeout cuts SSE streams through the gateway; ankka's operator does not yet override it on the `HTTPRoute`.
- 2026-09-30: V5 (gateway idle timeout, gzip through the gateway) and V6 (hour-long solve keeps heartbeats on `large`) unverified.
- 2026-09-30: `/internal/*` is internet-reachable with only the runner token in front.
- 2026-09-30: `deploy/api.json` leaves `SAT_PUBLIC_URL` and `SAT_METRICS_TOKEN` unset.
- 2026-09-30: `spec.md` still says `Status: Draft` although implemented.

## Recent moves
- 2026-09-29: `v0.1.0` tagged. PR #3: solver picks its control channel by presence of `ankka.tls.service-directory`. Branch `architecture-diagram` two commits ahead: hand-drawn SVG architecture diagram, README trimmed.
- 2026-09-28: feature 001 implemented and merged (PR #1); docs site, llms.txt and four skills (PR #2); repinned to ankka snapshot for cert-auth databases and mutual TLS; tier 5 run on a local kind ankka built from ankka HEAD.
- 2026-09-26: design documents and first feature spec.
