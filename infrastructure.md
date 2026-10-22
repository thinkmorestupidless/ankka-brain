# Infrastructure

Where the public repos publish and where their documentation is served. Pointers and names only;
no values. Everything about installations (cloud projects, clusters, DNS, release identity,
Kubernetes and Terraform secret names) is installation-specific and lives in
`ankka-brain-private/infrastructure.md`.

*As of 2026-10-10.*

## Accounts and organizations
- GitHub org `thinkmorestupidless`. CODEOWNERS `@thinkmorestupidless`.
- Maven Central namespace `com.thinkmorestupidless`, via Central Portal (`sbt ci-release`, `sonaBundle`). No snapshot entitlement.
- PyPI, npm, crates.io: trusted publishing from GitHub environments `pypi`, `npm`, `crates-io`.

## Public hostnames
| Host | What | Served by |
|---|---|---|
| `docs.ankka.cloud` | ankka docs | GitHub Pages |
| `flow.ankka.cloud` | ankka-flow docs | GitHub Pages |
| `satisfactory.ankka.cloud` | satisfactory docs | GitHub Pages |
| `reasoning.ankka.cloud` | ankka-reasoning docs | GitHub Pages |
| `www.ankka.cloud`, `api.ankka.cloud`, `auth.ankka.cloud`, `<service>-<project>.ankka.cloud` | the hosted product, its control plane, its realm, its services | the hosted installation; see the private brain |
| `127.0.0.1.sslip.io:8443` | local kind installation | laptop |

## Registries and packages
| Where | What | Written by |
|---|---|---|
| `ghcr.io/thinkmorestupidless/*` | public images: ankka-operator, ankka-controlplane, ankka-sidecar, ankka-console, sample-shopping-cart, ankka-reasoning, ankka-graph-sink-neo4j, ankka-flow-sidecar, ankka-flow-operator, ankka-flow samples, cloudflow-operator | each repo's release job via `GITHUB_TOKEN`. A new package is private until made public by hand once |
| Maven Central `com.thinkmorestupidless` | ankka-{core,sdk,runtime,http,agent,testkit,controlplane-api}_3; satisfactory-{protocol,model-spi,client}, ankka-satisfactory; ankka-reasoning-{graph,belief,market}_3; ankka-contrib-neo4j-graph-store_3 | ankka, satisfactory, ankka-reasoning, ankka-contrib |
| GitHub Packages `maven.pkg.github.com/thinkmorestupidless/cloudflow` | `com.lightbend.cloudflow` 0.1.0 only | cloudflow |
| PyPI | `ankka`, `ankka-flow` | ankka, ankka-flow |
| npm | `ankka`, `ankka-console` | ankka |
| crates.io | `ankka` | ankka |
| Homebrew `thinkmorestupidless/tap` | `ankka` CLI | subtree from ankka |
| GitHub releases on ankka | native CLI tarballs per platform, JVM zip | ankka |

An installation may mirror the public images into a registry of its own so clusters pull with
their node identity; that is the private brain's concern (`decisions/0009`).

## Namespaces on an installation
From ankka's components: `flux-system`, `ankka-auth`, `cert-manager`, `ankka-gateway`,
`ankka-controlplane`, `ankka-operator`, `ankka-console`, `ankka-<project>` per project,
`envoy-gateway-system`.

## Secret names (never values)
- GitHub Actions, public repos: `PGP_SECRET`, `PGP_PASSPHRASE`, `SONATYPE_USERNAME`, `SONATYPE_PASSWORD`, `TEMPLATE_REPO_TOKEN`, `MARKETPLACE_REPO_TOKEN`, `GITHUB_TOKEN`.
- Laptop: `~/.ankka/config.json`, `~/.ankka/credentials.json`, `~/.ankka/local-ca.crt`; per-repo `.env` files, gitignored; env `ANTHROPIC_API_KEY`, `TYPESAFE_API_KEY`.
- The secret names ankka's own components create are in its manifests; an installation's extra ones are in the private brain.

## Local development stacks
- ankka: `just cluster-create` (kind), `just up`, or `docker-compose.yml` for Postgres 17 and Keycloak 26.7.4.
- ankka-flow: Kafka on `localhost:9094`, Neo4j 5.26 via compose; k3s in testcontainers.
- satisfactory: `just db` for Postgres; `just run-api` with the in-process runner.
- ankka-reasoning: `just up`, `just run`; Kafka and Neo4j from compose.
