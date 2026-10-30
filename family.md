# The ankka family

*As of 2026-10-10.*

## Thesis
ankka is a serverless application platform for agentic AI on the actor model, in Scala 3 on Apache
Pekko, so that nothing on the path carries a BSL licence.
You write components; the platform supplies sharding, persistence, projections, durable
orchestration, timers, HTTP and the agent loop, and a control plane, operator and CLI run it on
Kubernetes. Services can be written in Scala, Java, Kotlin, Python, TypeScript, Go or Rust.

The rest of the family exists to prove and sell that platform by being its own customers. Each
sibling is either an ankka application, the infrastructure ankka runs on, or a layer that runs
beside it.

## Public and private
This brain maps the public repositories only. The family also has private members: the hosted
product, the infrastructure installations run on, and products built on ankka for customers. They
are mapped in `ankka-brain-private`, a sibling checkout for those with access, which extends
every file here rather than repeating it. A public repo may name a private one; it never carries
its content, its values or its status.

## Roles

| Repo | Is | Relationship to ankka |
|---|---|---|
| ankka | the platform | root of the family |
| satisfactory | an ankka application, separate product | constraint solving as a service; first real downstream user of the docs tool, marketplace and deploy story |
| ankka-flow | a layer beside ankka | streaming pipelines over the Kafka topics ankka services publish; not a dependency in either direction |
| cloudflow | ancestor of ankka-flow | maintained fork on the same Pekko line; frozen for its existing consumers |
| ankka-reasoning | an ankka application, first feature built | a reasoning graph whose records are ankka entities, published as graph deltas for the merge sink |
| ankka-contrib | integrations on ankka's SDK | reusable pieces that are not the platform's, one module per integration; first the Neo4j graph store and sink image |
| ankka-papercast | an ankka application, a demonstration | a podcast of the scientific literature, built on camera in the first long-form video; `main` is the recording's starting point |
| ankka-predictions | two libraries on ankka-core | a prediction market's exchange and markets domains and their components, composed by a private module owned by another company; the first family library whose consumer is outside the family |

The control plane inside `ankka` is itself an ankka application. The operator deliberately is not.

Private members, by role only: a hosted product that turns a stranger into a customer with an
organization on an installation; an infrastructure repo that owns installations, the registry and
the release identity ankka publishes with; and downstream products and their consumers, including
one owned by another company. Their names, pins and status are in the private brain.

## Dependency and pinning chain

| Consumer | Consumes from | What | Pinned at | Mechanism |
|---|---|---|---|---|
| satisfactory | ankka | `ankka-{sdk,runtime,http,agent,testkit}` | 0.7.1+28 local snapshot | `V.ankka` in project/Dependencies.scala; waits for feature 014, which v0.8.0 already carries |
| satisfactory | ankka | docs tool `ankka-docs` | branch `shared-docs-tool` | git dependency in tools/docs/pyproject.toml |
| ankka-flow | ankka | docs tool `ankka-docs` | branch `main` | git dependency |
| ankka-reasoning | ankka | docs tool `ankka-docs` | branch `main` | git dependency in tools/docs/pyproject.toml |
| satisfactory, ankka-flow, ankka-reasoning | ankka-marketplace | one plugin directory each | n/a | release job pushes `plugins/<project>/` |
| ankka-reasoning | ankka, ankka-flow | SDK and runtime; the graph delta contract and the merge sink | ankka 0.10.0; ankka-flow 0.3.0 (sidecar image) | pinned 2026-10-06. Wants the next ankka release (person tokens, single-file feature suites) and ankka-flow's sidecar package made public |
| ankka-contrib | ankka | `ankka-sdk` and the graph sink module | 0.11.0 | Maven Central; released on its own cadence against a published ankka |
| ankka-papercast | ankka | `ankka-{sdk,runtime,http,agent,testkit}` from the template; the spec-kit extensions | 0.14.2 | `ankkaVersion` in build.sbt and `runtime` in service.json; Maven Central |
| ankka-predictions | ankka | `ankka-core` for the domains; the SDK and testkit for the components | not yet (spec 004 says the family's current release) | `build.sbt`, Maven Central; nothing built yet |

One pin is out of policy today: satisfactory on a snapshot and a branch. See `roadmap.md`. The
private members' pins, including the installation tag and the hosted product's version, are in the
private brain's chain table.

## One Pekko line
Every JVM repo pins the same family: pekko 1.7.0, pekko-http 1.4.0, pekko-connectors-kafka 1.2.0,
pekko-management 1.2.1, kafka-clients 3.9.2, Scala 3.9.0 (cloudflow 3.3.5), JDK 21, sbt 1.12.15.
Bump in ankka first, then mirror. ankka-flow deliberately excludes pekko-http and pekko-grpc.

## Where each kind of thing lives
- Public documentation: each repo's `docs/`, built by ankka's docs tool, published to `docs.ankka.cloud`, `flow.ankka.cloud`, `satisfactory.ankka.cloud`, `reasoning.ankka.cloud`.
- Internal design treatments: in the private infrastructure repo, never in a public repo. The private brain says where.
- Feature specs: `specs/NNN-name/` in the repo the feature lands in (spec-kit).
- Installation-specific values: never in a public repo; the private brain maps them.
- Claude Code skills: rendered from each repo's `tools/docs/skill/` into `ankka-marketplace`.
- The family map, cross-repo decisions and conventions: here, with the private extension beside it.

## Release channels
Only a git tag publishes anything, in every repo. Versions come from sbt-dynver; placeholders
`0.0.0` in the tree are rewritten by release jobs. See `infrastructure.md` for where each artifact
lands.
