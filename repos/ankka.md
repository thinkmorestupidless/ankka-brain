# ankka

## One-liner
A serverless application platform for agentic AI, built on the actor model: a component model in
Scala 3 on Apache Pekko, with services in Scala, Python, TypeScript or Rust. You write
components; ankka supplies the runtime. A control plane, an operator and a CLI deploy, expose,
scale and observe the result on Kubernetes.

## Why it exists
It is a component model for stateful services and agents in Scala 3 on Apache Pekko, so the
programming model carries no BSL constraint on who runs it or what they run on it. Its public
documentation describes the platform on its own terms and compares it with no other platform; what
it does not do is `docs/reference/limitations.md`. It was called
`nakka` until September 2026, before anything was released; git history before the rename reads
`nakka` throughout.

## Status
- Stage: released, active daily; feature 018 (judgments) in flight
- As of: 2026-09-30
- Current release: `v0.14.2` (tag on 28d41b9, 2026-10-28; spec 061, design and build: the `/speckit-design` and `/speckit-build` skills and the Get started page, with dependency updates, on top of v0.14.1, the documentation review). 25 tags since v0.1.0
- Visibility: public, Apache-2.0

## Where it lives
- Repo: github.com/thinkmorestupidless/ankka
- Local: ~/Documents/projects/thinkmorestupidless/ankka (plus worktrees, see Housekeeping)
- Publishes, on a `v*` tag only: Maven Central `com.thinkmorestupidless:ankka-{core,sdk,runtime,
  http,agent,testkit,controlplane-api}_3`; PyPI `ankka`; npm `ankka` and `ankka-console`;
  crates.io `ankka`; GHCR images `ankka-operator`, `ankka-controlplane`, `ankka-sidecar`,
  `ankka-console`, `sample-shopping-cart`; native CLI tarballs on the GitHub release; Homebrew
  `thinkmorestupidless/tap/ankka`; subtree pushes to `ankka.g8`, `ankka-action`,
  `ankka-marketplace`, `homebrew-tap`; docs to docs.ankka.cloud
- Runs: any Kubernetes 1.32+ cluster with its components; the hosted installation is in the
  private brain; locally on kind cluster `ankka`

## Depends on / depended on by
ankka depends on no sibling. Everything else depends on it. What each consumes and therefore what
must not break:
- **satisfactory**: `ankka-{sdk,runtime,http,agent,testkit}`, `AnkkaTestKit`, Jackson 2.21
  alignment, the docs tool. Pinned at a local 0.7.1 snapshot although v0.8.0 carries what it wants.
- **ankka-flow**: the docs tool at `tools/docs` and its `extra.docs` mkdocs contract; the
  marketplace layout (ankka's release job replaces only `plugins/ankka/`); ankka's conventions.
  Needed `ProjectionRuntime.fromEnv()` to read `ANKKA_KAFKA_BOOTSTRAP_SERVERS`.
- **cloudflow**: no code dependency; shares the Pekko line. Bump Pekko here, mirror there.
- **ankka-contrib**: `ankka-sdk` and the graph sink module at 0.11.0.
- **Private consumers** (the hosted product, the infrastructure repo, the downstream products):
  what each pins and what must not break for them is in `ankka-brain-private/repos/ankka.md`.

## Read first
1. `CLAUDE.md`: architecture, commands, publishing, and about 120 dated traps. Read before any change.
2. `docs/concepts/architecture.md`, `docs/concepts/designing-services.md`, `docs/reference/limitations.md`: required before any design decision.
3. `docs/concepts/polyglot.md`, `protocol/README.md`, `docs/contributing/language-sdks.md`: before touching an SDK or the sidecar.
4. `docs/concepts/tenancy-and-access.md`, `docs/platform/identity.md`: before control plane or auth work.
5. `docs/platform/install-local.md`, `install-cloud.md`, `networking.md`: before manifests or deployments work.
6. `docs/reference/limitations.md`, `docs/reference/glossary.md`.
7. `specs/<current>/`: today `specs/018-judgments/`.

## Conventions specific to this repo
Beyond `../conventions.md`:
- Module direction is law: `core → sdk → runtime → {http, agent} → testkit`; `crd` depends on
  nothing, `operator` only on `crd`, `runtime` never on `http` or `agent` (they plug in as
  `RuntimeExtension`s). The operator is deliberately not an ankka application.
- Effects are inert data; the runtime interprets them. No classpath scanning. Wire names are
  declared, never derived from method names. Entities never see other entities.
- Never add `ThisBuild / version`. Nothing in the build may write a tracked file during publish.
- Never render an HPA. `exit-jvm` only in the kubernetes overlay. Two services never share a
  Postgres database. DDL is additive within a supported range.
- Internal design treatments go to the private infrastructure repo, not here.
- Sidecar protocol version `1.2`; runtime compatibility is same major, minor equal or one below.

## Open questions
- 2026-09-30: feature 018 task T036, the release gate (full awake test run, live Jev suite, quickstart by hand), still open; PR not yet opened despite T037 ticked.
- 2026-09-30: working tree dirty: `console/package/package.json` at 0.9.0 and an untracked `console/package/package/`. Looks like a by-hand first publish of `ankka-console` left uncommitted.
- Doc inconsistencies: `architecture.md` still says "Python today"; agent concept pages list only scala and python.
- The honest not-implemented list is `docs/reference/limitations.md`. Do not promise past it.

## Recent moves
- 2026-10-12: spec 051, replicated entities, implemented on branch `051-replicated-entities` (worktree `.claude/worktrees/051-replicated-entities`): one home per entity, followers mirroring the home's journal record for record, handover and replication set as journal records; the `ReplicationTransport` seam (`runtime/replication`) is the contract the private `ankka-replication` builds against, with the runtime's own database transport for one machine; protocol 1.19 in every SDK. Not merged as of this note.
- 2026-10-11: specs 049 (images for both architectures) and 050 (remote clusters: a cluster the control plane does not run in, tethered by its operator) on branch `specs-049-050`, unmerged; written for `tupile`, a Raspberry Pi cluster, the first arm64 installation. Feature 049 in implementation on branch `049-multi-architecture-images` (worktree `.claude/worktrees/049-multi-architecture-images`, pushed; carries the specs commit): build, release and cluster workflows, the operator's wrong-architecture report, template and docs done; the k3s case and the arm64 leg proved on the cluster workflow; PR not yet opened. The Pi overlay is the infrastructure repo's.
- 2026-09-30: feature 018 judgments, 36 of 37 tasks, on branch `018-judgments`.
- 2026-09-29: `v0.9.1`, Rust crate on crates.io (PRs #36 to #38). Console package plus thin host (017, #34). WASM hosting (016, #35).
- 2026-09-27/28: autonomous agents (015, #32), organization quotas (015, #31), Kafka via env (#30), marketplace per project (#28, #29), docs tool shared (#26), zero trust clusters (014, #27).
- 2026-09-26: TypeScript SDK (013), native CLI (#11), `ankka init --language`, images to ghcr.io with a mirror for the installation (#13), design docs and the installation overlay moved to the infrastructure repo (#20, #21).
- 2026-09-22 to 25: polyglot runtimes (009), public docs and `ankka mcp` (010), hosted ankka (011), endpoint ACLs (012), Homebrew, PyPI.

## Housekeeping
- Worktrees as sibling dirs: `../ankka-010-documentation` (merged), `../ankka-docs-diagrams` (unmerged), `../ankka-fix-tls-ports` (unmerged, 2026-09-30). Under `.claude/worktrees/`: `011-hosted-ankka`, `012-endpoint-acls` (merged); `013-deploy-from-github` dir on `fix-crates-io-guard`, `013-pull-credentials` dir on `015-organization-quotas`, `013-typescript-sdk` dir on `mcp-terminal-notice` (all merged, dir names no longer match branches); `017-control-plane-console` (merged, one later commit); `marketplace-repo`, `shared-docs-tool` (merged).
- `../nakka/` and `../ankka-009-polyglot-runtimes/` are empty leftovers (`.metals` only).
- Spec numbering has gaps and duplicates: no 010 directory, two 013s, two 015s. Every `spec.md` says `Status: Draft`; real status is the tasks.md tick count.
