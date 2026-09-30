# Reuse map

What is shared across the family, what should be, and what deliberately is not.

## Shared today

| Asset | Source of truth | Used by | Mechanism |
|---|---|---|---|
| Docs tool `ankka-docs` | `ankka/tools/docs` | ankka, ankka-flow, satisfactory | git dependency in each `tools/docs/pyproject.toml` |
| Claude Code marketplace | `thinkmorestupidless/ankka-marketplace` | ankka, ankka-flow, satisfactory | each release job replaces only `plugins/<project>/` |
| `.scalafmt.conf` | `ankka/.scalafmt.conf` | every JVM repo | copied by hand |
| `.githooks/pre-commit` | ankka | every JVM repo | copied by hand |
| Pekko version line | `ankka/project/Dependencies.scala` | cloudflow, ankka-flow | mirrored by hand |
| Operator render/execute pattern | `ankka/operator` | ankka-flow | reimplemented, same shape |
| Conformance-suite pattern | `ankka/protocol` | ankka-flow | reimplemented |
| Kustomize components plus symlinked resources | ankka | ankka-flow, ankka-cloud | copied pattern |
| Design treatment `hosted-ankka.md` | `ankka-deployments/design/` | ankka-cloud (copy in `docs/design/`) | duplicated; the deployments copy is canonical |
| Cloudflow pieces (blueprint verification, JSON contracts, commit-after-write, reset, topic creation, client-id naming) | cloudflow, Lightbend headers kept | ankka-flow | copied with tests, listed in `NOTICE` |
| Release identity and registry | ankka-deployments `modules/artifact-registry` | ankka, ankka-cloud | workload identity federation |

## Candidates: should be shared, currently is not

| Thing | Where it lives now | Where it should live | Effort |
|---|---|---|---|
| spec-kit constitution | unfilled template in every repo; plans use ankka's CLAUDE.md principles | one `constitution.md` here, referenced by each `.specify/memory/` | low |
| `.scalafmt.conf`, pre-commit hook, Justfile skeleton | copied per repo | a template repo or a `just init` recipe in ankka.g8 | low |
| The "ankka requests" list | satisfactory `notes/ankka-requests.md`, ankka-flow research, ankka-cloud follow-ons | `roadmap.md` here, then ankka specs | done here; specs pending |
| `ankka-cloud/docs/design/hosted-ankka.md` | duplicated from ankka-deployments | link, not copy | low |
| Test PKI (`modules/test-pki`) | ankka | satisfactory and ankka-cloud test against mTLS too | medium |

## Deliberately not shared

| Thing | Why | Date |
|---|---|---|
| A `Solver` component in ankka | satisfactory stays a separate product; ankka never depends on Timefold | 2026-09-22 |
| ankka-flow depending on cloudflow or ankka | six pieces carried by copy; the sidecar model removes the rest of Cloudflow's structure | 2026-09-28 |
| Cluster add-ons in Terraform | they stay in ankka's components, pinned beside the code that depends on their CRDs | ankka-deployments D2 |
| Kubernetes Events from ankka's operator | ankka-flow writes them, ankka does not; a deliberate departure | ankka-flow R13 |
| pekko-http, pekko-grpc in ankka-flow | grpc-java plus ScalaPB; kept out by rule | ankka-flow R1 |
| ankka-cloud through the operator | it must hold the apex hostname and must not be a suspendable service | ankka-cloud D2 |
