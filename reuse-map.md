# Reuse map

What is shared across the public family, what should be, and what deliberately is not. Rows that
involve a private repo are in `ankka-brain-private/reuse-map.md`.

## Shared today

| Asset | Source of truth | Used by | Mechanism |
|---|---|---|---|
| Docs tool `ankka-docs` | `ankka/tools/docs` | ankka, ankka-flow, satisfactory, ankka-reasoning | git dependency in each `tools/docs/pyproject.toml` |
| Claude Code marketplace | `thinkmorestupidless/ankka-marketplace` | ankka, ankka-flow, satisfactory, ankka-reasoning | each release job replaces only `plugins/<project>/` |
| `.scalafmt.conf` | `ankka/.scalafmt.conf` | every JVM repo | copied by hand |
| `.githooks/pre-commit` | ankka | every JVM repo | copied by hand |
| Pekko version line | `ankka/project/Dependencies.scala` | cloudflow, ankka-flow | mirrored by hand |
| Operator render/execute pattern | `ankka/operator` | ankka-flow | reimplemented, same shape |
| Conformance-suite pattern | `ankka/protocol` | ankka-flow | reimplemented |
| Kustomize components plus symlinked resources | ankka | ankka-flow | copied pattern |
| Cloudflow pieces (blueprint verification, JSON contracts, commit-after-write, reset, topic creation, client-id naming) | cloudflow, Lightbend headers kept | ankka-flow | copied with tests, listed in `NOTICE` |
| Graph sink rules and the delta contract `ankka.graph-delta.v1` | ankka | ankka-contrib (store), ankka-reasoning (producer), ankka-flow (merge sink) | library dependency; contract by name |
| Release workflow shape (tag only, dynver, Central Portal bundle) | ankka `.github/workflows/release.yml` | satisfactory, ankka-reasoning, ankka-contrib | copied and trimmed |

## Candidates: should be shared, currently is not

| Thing | Where it lives now | Where it should live | Effort |
|---|---|---|---|
| spec-kit constitution | unfilled template in most repos; plans use ankka's CLAUDE.md principles | one `constitution.md` here, referenced by each `.specify/memory/` | low |
| `.scalafmt.conf`, pre-commit hook, Justfile skeleton | copied per repo | a template repo or a `just init` recipe in ankka.g8 | low |
| The "ankka requests" list | satisfactory and ankka-reasoning `notes/ankka-requests.md`, ankka-flow research | `roadmap.md` here, then ankka specs | done here; specs pending |
| The parent-directory `CLAUDE.md` | `workspace/CLAUDE.md` here, copied by hand | an install recipe | low |

## Deliberately not shared

| Thing | Why | Date |
|---|---|---|
| A `Solver` component in ankka | satisfactory stays a separate product; ankka never depends on Timefold | 2026-09-22 |
| ankka-flow depending on cloudflow or ankka | six pieces carried by copy; the sidecar model removes the rest of Cloudflow's structure | 2026-09-28 |
| Kubernetes Events from ankka's operator | ankka-flow writes them, ankka does not; a deliberate departure | ankka-flow R13 |
| pekko-http, pekko-grpc in ankka-flow | grpc-java plus ScalaPB; kept out by rule | ankka-flow R1 |
| The Neo4j driver in ankka | integrations with their own dependencies live in ankka-contrib | 2026-10-08 |
| Private content in this brain | visibility is a trust boundary; the private brain extends this one | `decisions/0011` |
