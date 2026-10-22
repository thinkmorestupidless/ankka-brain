# 0009 — Public images are published on ghcr.io

## Status
Accepted 2026-09-26/27 (recorded 2026-09-30; split 2026-10-10, the private half is
`ankka-brain-private/decisions/0003`)

## Repos affected
- ankka, ankka-flow, cloudflow, ankka-reasoning, ankka-contrib: public images go to
  `ghcr.io/thinkmorestupidless/*`, pushed by the release job with `GITHUB_TOKEN`. A new package is
  private on first push and must be made public by hand once.
- Private images and the registry an installation pulls from are the infrastructure repo's concern;
  see the private brain.

## Context
Public images belong somewhere anyone can pull without a cloud account. A managed cloud cluster
cannot pull keylessly from GitHub's registry, so an installation needs a mirror of its own, but
that is an installation's problem, not the public repos'.

## Decision
ghcr.io is where public images are published. ankka's release additionally warms whatever mirror
the installation uses and compares digests; the mirror's name is configuration held privately.

## Alternatives considered
1. **A cloud registry only.** Lost: public consumers would need a cloud account or a public bucket.
2. **ghcr.io only, pull secrets in clusters.** Lost: a token per cluster to rotate.

## Consequences
- Every public repo's release workflow pushes to ghcr.io with the repository's own token; no
  cloud credential in a public repo's CI.
- The first push of a new image name leaves a private package; ankka-reasoning and ankka-flow's
  sidecar both hit this.

## Related
- ankka `CLAUDE.md` "Publishing" (images)
- `ankka-brain-private/decisions/0003` for the mirror and the private registry
