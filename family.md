# The ankka family

*As of 2026-09-30.*

## Thesis
ankka is a serverless application platform for agentic AI on the actor model: Akka's component
model reimplemented in Scala 3 on Apache Pekko, so that nothing on the path carries a BSL licence.
You write components; the platform supplies sharding, persistence, projections, durable
orchestration, timers, HTTP and the agent loop, and a control plane, operator and CLI run it on
Kubernetes. Services can be written in Scala, Python, TypeScript or Rust.

The rest of the family exists to prove and sell that platform by being its own customers. Each
sibling is either an ankka application, the infrastructure ankka runs on, or a layer that runs
beside it.

## Roles

| Repo | Is | Relationship to ankka |
|---|---|---|
| ankka | the platform | root of the family |
| ankka-cloud | an ankka application | the hosted product: turns a stranger into a customer with an organization on an installation |
| satisfactory | an ankka application, separate product | constraint solving as a service; first real downstream user of the docs tool, marketplace and deploy story |
| ankka-deployments | infrastructure | where installations live; owns the registry and the release identity ankka publishes with |
| ankka-flow | a layer beside ankka | streaming pipelines over the Kafka topics ankka services publish; not a dependency in either direction |
| cloudflow | ancestor of ankka-flow | maintained fork on the same Pekko line; frozen for its existing consumers |
| ankka-reasoning | an ankka application, first feature built | a reasoning graph whose records are ankka entities, published as graph deltas for ankka-flow's merge sink |
| ankka-casino | an ankka application, owned by eitheror | eitheror's casino and payments as services on ankka; the first real-money consumer, and the first outside thinkmorestupidless |

The control plane inside `ankka` is itself an ankka application. The operator deliberately is not.

ankka-casino is `eitheror-io/ankka-casino`, not a thinkmorestupidless repo, and its checkout is
not a sibling here. It is in the map because it consumes ankka and because what it needs shapes
ankka's specs. Planned beside it: `ankka-gcp`, the Google cloud provider that implements ankka
spec 044, holding the owner-equivalent IAM power the operator must not.

## Dependency and pinning chain

| Consumer | Consumes from | What | Pinned at | Mechanism |
|---|---|---|---|---|
| ankka-cloud | ankka | `ankka-{sdk,runtime,http,controlplane-api,testkit}`, control plane HTTP routes | 0.9.0 | `ankkaVersion` in build.sbt, Maven Central; `schemaCheck` pins the DDL |
| satisfactory | ankka | `ankka-{sdk,runtime,http,agent,testkit}` | 0.7.1+28 local snapshot | `V.ankka` in project/Dependencies.scala; waits for feature 014, which v0.8.0 already carries |
| satisfactory | ankka | docs tool `ankka-docs` | branch `shared-docs-tool` | git dependency in tools/docs/pyproject.toml |
| ankka-flow | ankka | docs tool `ankka-docs` | branch `main` | git dependency |
| ankka-reasoning | ankka | docs tool `ankka-docs` | branch `main` | git dependency in tools/docs/pyproject.toml |
| ankka-deployments | ankka | `kustomization/components/`, images | tag v0.6.4 | Flux GitRepository `ankka` with `include`; four `newTag`s plus sidecar tag move together |
| ankka | ankka-deployments | release identity, registry | n/a | workload identity federation, values copied by hand |
| ankka-cloud | ankka-deployments | hub composition, four Secrets, namespace | not yet written | Flux from `clusters/<env>/` |
| satisfactory, ankka-flow | ankka-marketplace | one plugin directory each | n/a | release job pushes `plugins/<project>/` |
| ankka-reasoning | ankka, ankka-flow | SDK and runtime; the graph delta contract and the merge sink | ankka 0.10.0; ankka-flow 0.3.0 (sidecar image) | pinned 2026-10-06. Wants the next ankka release (person tokens, single-file feature suites) and ankka-flow's sidecar package made public |
| ankka-casino | ankka | SDK and runtime; specs 038 to 044 (secret store backends, GCS, cross-project access, backups and recovery, personal data erasure, topic retention, cloud provider); `ankka-port`; the `ankka@ankka` skills | not yet pinned | specifying as of 2026-10-08; waits on a release carrying 038 to 044, then `ankka-gcp` for its GKE installation |

Two pins are out of policy today: satisfactory on a snapshot and a branch, ankka-deployments three
minor versions behind a release its hosted product needs. See `roadmap.md`.

## One Pekko line
Every JVM repo pins the same family: pekko 1.7.0, pekko-http 1.4.0, pekko-connectors-kafka 1.2.0,
pekko-management 1.2.1, kafka-clients 3.9.2, Scala 3.9.0 (cloudflow 3.3.5), JDK 21, sbt 1.12.15.
Bump in ankka first, then mirror. ankka-flow deliberately excludes pekko-http and pekko-grpc.

## Where each kind of thing lives
- Public documentation: each repo's `docs/`, built by ankka's docs tool, published to `docs.ankka.cloud`, `flow.ankka.cloud`, `satisfactory.ankka.cloud`, `reasoning.ankka.cloud`.
- Internal design treatments: `ankka-deployments/design/`. Never in a public repo.
- Feature specs: `specs/NNN-name/` in the repo the feature lands in (spec-kit).
- Installation-specific values: `ankka-deployments` only.
- Claude Code skills: rendered from each repo's `tools/docs/skill/` into `ankka-marketplace`.
- The family map, cross-repo decisions and conventions: here.

## Release channels
Only a git tag publishes anything, in every repo. Versions come from sbt-dynver; placeholders
`0.0.0` in the tree are rewritten by release jobs. See `infrastructure.md` for where each artifact
lands.
