# 0005 — Installation values and design treatments live in ankka-deployments

## Status
Accepted 2026-09-26/27 (recorded 2026-09-30)

## Repos affected
- ankka: public; carries components and a placeholder-only example overlay; no real domain,
  address, project id or design treatment. Design docs moved out on 2026-09-27 (PRs #20, #21).
- ankka-deployments: private; owns every installation overlay, every credential able to create
  cloud resources, and `design/`.
- ankka-cloud: keeps a copy of `hosted-ankka.md` in `docs/design/`; the deployments copy is
  canonical.

## Context
ankka became public with a real cluster already running. Its overlay named a real address and
domain, and its `docs/design/` held treatments not meant for the public docs site. The
infrastructure repo already existed, split by lifecycle and trust (its D1).

## Decision
Nothing installation-specific goes into ankka. Overlays compose ankka's components at a pinned
tag from inside ankka-deployments (its D14). Internal design treatments live in
`ankka-deployments/design/`, numbered against ankka's specs.

## Alternatives considered
1. **A private `overlays/` directory in ankka.** Lost: there is no such thing in a public repo.
2. **Design docs in the public docs tree.** Lost: they tell history and name unbuilt things, which
   the docs rules forbid.

## Consequences
- A release is one commit in ankka-deployments moving the ankka tag and every image tag together.
- ankka's `specs/` still link to `docs/design/` paths that no longer exist there.
- A new design treatment starts in ankka-deployments, not in the repo it will land in.

## Related
- ankka-deployments `docs/decisions.md` D1, D14; `design/README.md`
- ankka `CLAUDE.md` rule on design treatments
