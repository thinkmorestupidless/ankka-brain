# 0006 — satisfactory is a separate product, not an ankka component

## Status
Accepted 2026-09-22 (recorded 2026-09-30)

## Repos affected
- ankka: no `Solver` component, no dependency on Timefold, no `WorkItem` supertype. A future
  `AnkkaJob` workload kind is an ankka feature that satisfactory would use, not a satisfactory feature.
- satisfactory: an ankka application and three published artifacts; ankka never depends on it;
  its `protocol`, `client`, `model-spi`, `models` and `runner` never depend on ankka.

## Context
Constraint solving could have been offered as a component type inside ankka. It has a different
licence surface (Timefold), a different audience, and a runtime shape (long CPU-bound solves in a
pull-based pool) that would fight ankka's cluster membership model.

## Decision
satisfactory is its own product built on ankka. An ankka agent reaches it through generated tools
in `ankka-satisfactory`; a workflow owns the wait. What satisfactory cannot do on ankka becomes an
ankka request (`notes/ankka-requests.md`).

## Alternatives considered
1. **`Solver` component in ankka.** Declined in `DESIGN.md` §11.2b.
2. **A `SolveJob` CRD.** Kept only ever as an adapter (§11.1 B); `AnkkaJob` is the real answer (C).

## Consequences
- satisfactory is the model for every future downstream product: same docs tool, marketplace
  plugin, deploy descriptors, and a request list back to ankka.
- The first cross-product ankka feature request, run-to-completion workloads, has no spec yet.

## Related
- satisfactory `DESIGN.md` §8, §11, §12; `notes/ankka-requests.md`
- `../roadmap.md` "Things ankka's downstream has asked for"
