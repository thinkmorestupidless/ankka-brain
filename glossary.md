# Glossary

Vocabulary that crosses repo boundaries. Each repo's `docs/reference/glossary.md` has its own
full list; this file holds the terms an agent needs before it knows which repo it is in.

## The family
- **ankka**: the platform. Called `nakka` until September 2026; old text and `~/.nakka/` are leftovers.
- **installation**: one deployed ankka: control plane, operator, Keycloak, gateway, on one cluster. Lives in ankka-deployments.
- **hub**: the installation that also runs the product realm and the ankka-cloud website. `arrakis` is planned as the hub.
- **spoke**: an installation that trusts the hub's realm via `ANKKA_AUTH_ISSUER` and `ANKKA_AUTH_JWKS_URL`.
- **self-hosted**: an installation with its own realm, not part of the product.
- **environment**: ankka-deployments' name for an installation's cloud project plus cluster (`dev`, `arrakis`). `ops` is the shared-services project, not an installation.
- **arrakis, caladan, giedi-prime, kaitain**: production clusters are named after Dune planets. `arrakis` is the first, at `ankka.cloud`.
- **ankka-ops**: the shared GCP project holding the one registry, the ghcr cache and the release identity.
- **the seam**: Flux, between what Terraform creates (cloud resources, namespaces, Secrets) and what the cluster pulls from git.
- **platform overlay**: `clusters/<env>/platform/` in ankka-deployments, composing ankka's components with the installation's values.
- **components**: ankka's `kustomization/components/*` (cnpg, certmanager, envoy-gateway, crd, gateway, operator, postgres, keycloak-operator, keycloak, controlplane), included at a pinned tag.
- **design treatment**: a pre-spec design document, kept in `ankka-deployments/design/`.
- **ankka-docs**: the docs tool in `ankka/tools/docs`, used by every repo.
- **ankka-marketplace**: the Claude Code plugin marketplace repo, one plugin per project.

## The platform
- **component**: event sourced entity, key value entity, view, consumer, workflow, timed action, agent, autonomous agent, HTTP endpoint.
- **effect**: the inert description a handler returns; the runtime interprets it.
- **wire name**: a handler's declared protocol name, separate from the method name. Changing it is breaking.
- **descriptor**: `service.json`, a service's desired state (`name`, `image`, `runtime`, `hosting`, `env`, `resources`).
- **hosting**: `embedded` (Scala, default), `process` (Python, TypeScript; sidecar on 9011, process on 9010), `wasm` (Rust; module loaded into the runtime via Chicory).
- **sidecar**: the runtime image beside a process-hosted service; also hosts wasm. Protocol version 1.2.
- **control plane**: records organizations, projects and services; authorizes; projects desired state into one `AnkkaService` per service. Itself an ankka application.
- **operator**: reconciles `AnkkaService` resources into namespace contents. Not an ankka application.
- **AnkkaService, asvc**: the CRD, `ankka.thinkmorestupidless.com/v1alpha1`.
- **organization ⊃ project ⊃ service**: the tenancy model. A project is namespace `ankka-<project>` with its own CNPG cluster.
- **owner, member, platform-admin**: the roles. **deploy token**: `ankka_<id>_<secret>`, an ordinary member.
- **quota**: projects, services, instances per organization; reserve-first.
- **base domain, hostname**: `<service>-<project>.<base>`, one label deep.
- **realm `ankka`**: the Keycloak realm every installation uses. Clients `ankka-cli`, `ankka-console`, plus `ankka-web` and `ankka-cloud` on the hub.
- **zero trust**: every port mutual TLS with cert-manager certificates; identities `ankka://<project>/<service>`.
- **conformance suite**: the definition of a compatible SDK. ankka has one; ankka-flow has one.
- **limitations page**: `docs/reference/limitations.md`, the honest not-implemented list.

## The hosted product
- **organization as subscription unit**: one Stripe subscription per organization; enforcement is disable or enable, nothing else.
- **as the person, as the product**: reads use the person's token, writes the `ankka-cloud` service-account token.
- **product realm**: the realm import ankka-cloud owns, with registration on and social login.
- **provisioning, enforcement, not-ours guard**: the workflow after checkout; the consumer keeping the installation in step with Stripe; never re-enable what the website did not disable.

## Pipelines
- **streamlet, inlet, outlet, blueprint**: a typed stage, its ports, and the HOCON wiring over Kafka topics. Shared by cloudflow and ankka-flow.
- **managed topic**: one the platform creates and owns, `<pipeline>.<id>`. **unmanaged**: an existing topic, such as one an ankka service publishes to.
- **contract, fingerprint**: JSON schema name and Base64(SHA-256(name)); equal fingerprints connect.
- **commit after the write, `OffsetFirstObserved`**: offsets commit only after the sink has written elsewhere.
- **built-in streamlet**: `builtin/<name>`, logic inside the sidecar, no process container. First: `neo4j-merge-sink`.
- **graph delta**: `ankka.graph-delta.v1`, node, edge or tombstone with a version.
- **AnkkaFlow, aflow**: ankka-flow's CRD, `flow.ankka.thinkmorestupidless.com/v1alpha1`.
- **Akka line, Pekko line**: cloudflow's frozen `0.1.0` and its `master`.

## Constraint solving
- **model**: a compiled Java planning problem type behind `SolverModel`; **dataset**: one submitted problem and its lineage (formerly "job").
- **runner, pool, slot, lease, epoch**: the pull-based solver worker, the `solver` service, one concurrent solve, a heartbeat-renewed right to solve, its fencing token.
- **fold rules**: lease epoch fences; only a strictly better score is recorded; no event carries a body.
- **warm start, lineage, supersede**: continue from a solution; parent and origin chain; derive from a still-solving parent.
