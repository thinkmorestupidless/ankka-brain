# ankka-deployments

## One-liner
Where an ankka installation lives: the cloud project, cluster, registry, addresses, DNS records,
installation secrets, each cluster's platform overlay and the ankka tag it runs. Terraform for the
cloud, Flux for the cluster.

## Why it exists
Split out of `ankka` by lifecycle (its D1): infrastructure changes rarely and dangerously, the
application changes per commit. Two forces wanted the split. Permissions: this repo's CI holds
cloud credentials and never builds an image, while `ankka`'s CI builds images and holds only a
registry-writer identity issued here. Reuse: the same cluster, registry and DNS shape serves more
than one product. Private, because `ankka` is public and none of this is ankka's to publish.

## Status
- Stage: building; one production cluster live
- As of: 2026-09-30
- Current release: no releases of its own. `arrakis` pins ankka `v0.6.4`
- Visibility: private

## Where it lives
- Repo: github.com/thinkmorestupidless/ankka-deployments
- Local: ~/Documents/projects/thinkmorestupidless/ankka-deployments
- Publishes: nothing. Owns the registry others publish to (see `../infrastructure.md`)
- Runs: GCP, `europe-west2`. Environments `ops` (shared registry and push identity), `arrakis`
  (production, `ankka.cloud` at the apex), `dev` (planned, never applied)

## Depends on / depended on by
- **Consumes `ankka`** at a pinned tag: Flux includes ankka's `kustomization/` components into
  this repo's `clusters/<env>/platform/` overlay (its D14). A release here is one commit that moves
  the ankka tag and every image tag together; `scripts/render-platform.sh` refuses otherwise.
- **Issues `ankka` its release identity**: workload identity federation, no key, restricted to
  repo `thinkmorestupidless/ankka` and refs `refs/tags/v*`. Two values cross by hand:
  `docker_repository` and `release_workload_identity`.
- **Will host `ankka-cloud`** beside the control plane on the hub cluster (planned in
  `design/hosted-ankka.md`, D8). Not started. PR #9 adds ankka-cloud to the push identity.
- **Holds the family's design treatments** in `design/`, moved from ankka's `docs/design/` on
  2026-09-27 because they are internal. ankka's `specs/` still link to the old path.

## Read first
1. `CLAUDE.md`: the validation commands and six rules that are not style.
2. `README.md`: the here-versus-there split table and the runbooks.
3. `docs/decisions.md`: D1 to D14, open items, and the traps sections.
4. `environments/arrakis/README.md`, `environments/ops/README.md`: what each project holds.
5. `clusters/arrakis/platform/kustomization.yaml` and `acme-issuer.yaml`: the overlay, commented.
6. `scripts/up.sh`, `down.sh`, `sleep.sh`: the lifecycle, each with a step list in its header.
7. `design/`: hosted-ankka, wasm-hosting, polyglot-runtimes and the others.

## Conventions specific to this repo
Beyond `../conventions.md`:
- Terraform puts nothing in a cluster but namespaces and Secrets. Flux is the seam.
- Two Terraform roots per environment: `edge/` outlives the cluster, `terraform/` is rebuilt.
- No script auto-approves an apply. Every script checks what exists first.
- Production clusters are named after Dune planets; `arrakis` first, then `caladan`, `giedi-prime`, `kaitain`.
- CI (`validate.yml`) runs terraform fmt and validate, kustomize renders, yamllint, gitleaks. It
  holds no cloud identity.

## Open questions
- 2026-09-30: no database backup story. CNPG in-cluster today; Cloud SQL is the alternative. Decide before production traffic.
- 2026-09-30: `dev` has no platform overlay and no project. Its `ankka-platform.yaml` points at a path that only exists in ankka tags up to v0.6.4.
- 2026-09-30: ankka's public images are moving to ghcr.io and the `ghcr` cache repo exists, but the arrakis overlay still pulls from `ankka-ops/ankka`.
- 2026-09-30: regional control plane and a node floor of three for production (its D7) are deferred.
- 2026-09-30: ankka-cloud hub composition (Flux source, Kustomization, Terraform-written Stripe and SMTP secrets) not started.

## Recent moves
- 2026-09-30: PR #9 open, admits `ankka-cloud`'s release workflow to the push identity after its v0.1.0 release was rejected by the attribute condition. Needs an ops apply after merge.
- 2026-09-28/29: `design/wasm-hosting.md` edited on branch `design-wasm-hosting`, four commits ahead of main, no PR yet.
- 2026-09-27: arrakis overlay moved here at v0.6.4 (PR #5); design treatments moved here (PR #6); sleep refuses a plan that destroys more than the node pool (PR #7).
- 2026-09-26: edge root split and up/down scripts (PR #1); sleep/wake (PR #3); arrakis rebuilt on ankka v0.6.3.
- 2026-09-22: arrakis first applied. Destroyed 09-23, rebuilt 09-26.

## Housekeeping
- `ankka-deployments-ghcr-cache/` beside this checkout is a stale worktree on branch `ghcr-cache`, already merged as PR #4. Safe to `git worktree remove`.
- Merged local branches not deleted: `actions-node24`, `arrakis-overlay-lives-here`, `design-treatments`, `sleep-destroys-only-the-pool`, `ghcr-cache`.
- `clusters/arrakis/platform/kustomization.yaml` mentions `scripts/fetch-ankka.sh`, which does not exist. The script is `render-platform.sh`.
