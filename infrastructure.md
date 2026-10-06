# Infrastructure

Where things run and publish. Pointers and names only; no values. Secret names are listed so an
agent can find them, never their contents.

*As of 2026-09-30.*

## Accounts and organizations
- GitHub org `thinkmorestupidless`. CODEOWNERS `@thinkmorestupidless`.
- Maven Central namespace `com.thinkmorestupidless`, via Central Portal (`sbt ci-release`, `sonaBundle`). No snapshot entitlement.
- PyPI, npm, crates.io: trusted publishing from GitHub environments `pypi`, `npm`, `crates-io`.
- GCP: projects `ankka-ops` (shared services, number 846467939619), `ankka-arrakis` (production), dev project not yet created. Region `europe-west2`, zone `europe-west2-a`.
- DNS: DNSimple, zone `ankka.cloud`. ACME contact trevor@thinkmorestupidless.com, Let's Encrypt DNS-01 via `cert-manager-webhook-dnsimple`.
- Stripe: one account; test mode locally, live keys only in the hub Secret.

## Domains
| Host | What | Served by |
|---|---|---|
| `ankka.cloud` | apex, redirect to www once `apex.yaml` is enabled | hub gateway |
| `www.ankka.cloud` | the ankka-cloud website | hub, namespace `ankka-cloud` |
| `api.ankka.cloud` | arrakis control plane | arrakis |
| `auth.ankka.cloud` | Keycloak, issuer `https://auth.ankka.cloud/realms/ankka` | arrakis |
| `*.ankka.cloud` | services, `<service>-<project>.ankka.cloud` | arrakis gateway, wildcard cert `ankka-wildcard` |
| `docs.ankka.cloud` | ankka docs | GitHub Pages |
| `flow.ankka.cloud` | ankka-flow docs | GitHub Pages |
| `satisfactory.ankka.cloud` | satisfactory docs | GitHub Pages |
| `reasoning.ankka.cloud` | ankka-reasoning docs | GitHub Pages |
| `dev.ankka.cloud` | planned dev environment | not created |
| `127.0.0.1.sslip.io:8443` | local kind installation | laptop |

## Registries and packages
| Where | What | Written by |
|---|---|---|
| `ghcr.io/thinkmorestupidless/*` | public images: ankka-operator, ankka-controlplane, ankka-sidecar, ankka-console, sample-shopping-cart, ankka-flow-sidecar, ankka-flow-operator, ankka-flow samples, cloudflow-operator | each repo's release job via `GITHUB_TOKEN` |
| `europe-west2-docker.pkg.dev/ankka-ops/ghcr` | remote cache of ghcr.io; clusters pull public images here | ankka's release warms it |
| `europe-west2-docker.pkg.dev/ankka-ops/ankka` | private images (ankka-cloud); legacy ankka releases up to 0.6.4 | release identity `ankka-release@ankka-ops` |
| Maven Central `com.thinkmorestupidless` | ankka-{core,sdk,runtime,http,agent,testkit,controlplane-api}_3; satisfactory-{protocol,model-spi,client}, ankka-satisfactory | ankka, satisfactory |
| GitHub Packages `maven.pkg.github.com/thinkmorestupidless/cloudflow` | `com.lightbend.cloudflow` 0.1.0 only | cloudflow |
| PyPI | `ankka`, `ankka-flow` | ankka, ankka-flow |
| npm | `ankka`, `ankka-console` | ankka |
| crates.io | `ankka` | ankka |
| Homebrew `thinkmorestupidless/tap` | `ankka` CLI | subtree from ankka |
| GitHub releases on ankka | native CLI tarballs per platform, JVM zip | ankka |

## Release identity
Workload identity federation in `ankka-ops`: pool `github-actions`, provider `github`, service
account `ankka-release@ankka-ops.iam.gserviceaccount.com`. Attribute condition admits repo
`thinkmorestupidless/ankka` on refs `refs/tags/v*`; ankka-deployments PR #9 adds `ankka-cloud`.
No key exists. Defined in `ankka-deployments/modules/artifact-registry`.

## Clusters
| Cluster | Where | Runs | ankka tag | State |
|---|---|---|---|---|
| `ankka-arrakis` | GKE, `ankka-arrakis`, zonal, private nodes, `n2d-standard-4` x3 to 9 | control plane, operator, Keycloak, gateway | v0.6.4 | live since 2026-09-26 rebuild; awake or asleep not recorded |
| `ankka-dev` | planned | | v0.1.0 placeholder | never applied |
| `ankka` (kind) | laptop | full installation | HEAD | as needed |

Lifecycle scripts in ankka-deployments: `up.sh`, `down.sh` (edge kept, data lost), `sleep.sh`,
`wake.sh` (node pool only, databases hibernated). Terraform state in
`<project>-terraform-state` buckets; Flux v2.9.5 bootstrapped per cluster with a read-only deploy
key.

## Namespaces on an installation
`flux-system`, `ankka-auth`, `cert-manager`, `ankka-gateway`, `ankka-controlplane`,
`ankka-operator`, `ankka-console`, `ankka-cloud` (hub), `ankka-<project>` per project,
`envoy-gateway-system`.

## Secret names (never values)
- GitHub Actions: `PGP_SECRET`, `PGP_PASSPHRASE`, `SONATYPE_USERNAME`, `SONATYPE_PASSWORD`, `TEMPLATE_REPO_TOKEN`, `MARKETPLACE_REPO_TOKEN`, `GITHUB_TOKEN`.
- Terraform variables: `dnsimple_token`, `dnsimple_account_id`; gitignored `*.tfvars`, `backend.hcl`.
- Kubernetes: `ankka-keycloak-admin` (ankka-auth), `dnsimple-credentials` (cert-manager), `ankka-wildcard-tls` (ankka-gateway), `ankka-console-secrets`, `ankka-cloud-stripe`, `ankka-cloud-oidc`, `ankka-cloud-idp`, `ankka-cloud-smtp`, `ankka-cloud-db-app`, `satisfactory-secrets` (ankka-satisfactory), `<service>-db`, `<service>-{cluster,service,database}`, `kafka-cluster-<name>` (ankka-flow), `neo4j-local`.
- Laptop: `~/.ankka/config.json`, `~/.ankka/credentials.json`, `~/.ankka/local-ca.crt`; per-repo `.env` files, gitignored; env `ANTHROPIC_API_KEY`, `TYPESAFE_API_KEY`.

## Local development stacks
- ankka: `just cluster-create` (kind), `just up`, or `docker-compose.yml` for Postgres 17 and Keycloak 26.7.4.
- ankka-cloud: docker compose with Postgres, MailHog on 8026, Keycloak on 8081, the control plane image on 9001; website on 9000; `stripe listen`.
- ankka-flow: Kafka on `localhost:9094`, Neo4j 5.26 via compose; k3s in testcontainers.
- satisfactory: `just db` for Postgres; `just run-api` with the in-process runner.
