# 0009 — Public images on ghcr.io through the ops cache; private images in ankka-ops

## Status
Accepted 2026-09-26/27 (recorded 2026-09-30)

## Repos affected
- ankka, ankka-flow, cloudflow: public images to `ghcr.io/thinkmorestupidless/*`; ankka's release
  also warms `europe-west2-docker.pkg.dev/ankka-ops/ghcr` and compares digests.
- ankka-cloud: private image to `europe-west2-docker.pkg.dev/ankka-ops/ankka`, using the ops
  release identity (PR #9 in ankka-deployments admits its workflow).
- ankka-deployments: owns both Artifact Registry repos and the pull grants; overlays should pull
  public images through the cache. The arrakis overlay still names `ankka-ops/ankka` images.

## Context
GKE cannot pull keylessly from GitHub's registry, and one registry per environment would mean a
credential per environment. Public images belong somewhere anyone can pull without a GCP account.

## Decision
ghcr.io is where public images are published. A remote-cache repository in `ankka-ops` mirrors
ghcr.io so clusters pull with their node identity and no token. `ankka-ops/ankka` holds private
images and the legacy releases up to 0.6.4.

## Alternatives considered
1. **Artifact Registry only.** Lost: public consumers would need a GCP account or a public bucket.
2. **ghcr.io only, pull secrets in clusters.** Lost: a token per cluster to rotate (D11).

## Consequences
- The arrakis overlay must switch image names to the cache path at its next release commit.
- The release identity's attribute condition is the list of repos allowed to push; adding a
  private-image repo is a Terraform change in `ankka-deployments`.

## Related
- ankka-deployments `docs/decisions.md` D8, D11; `modules/artifact-registry`
- ankka `CLAUDE.md` "Publishing" (images)
