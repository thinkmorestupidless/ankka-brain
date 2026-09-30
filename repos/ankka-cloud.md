# ankka-cloud

## One-liner
The hosted ankka product: a website where a person signs up, pays for a monthly subscription
through Stripe, and gets an organization on a running ankka installation to deploy into with the
ankka CLI.

## Why it exists
The platform authenticates its operators and bounds tenancy, but nothing turns a stranger into a
customer. It is its own repo (its D1) for lifecycle, trust (Stripe keys have no place in the
public platform repo) and readers, and it doubles as proof that the published `ankka-*` artifacts
work for a real application built from Maven Central alone. It is itself an ankka application:
entities, a consumer, a workflow, timers and one HTTP endpoint, server-rendered, no JS frontend.
It deploys beside the control plane as a platform component rather than through the operator,
because the process that suspends organizations must not itself be a suspendable organization's
service (its D2).

## Status
- Stage: released; not yet deployed
- As of: 2026-09-30
- Current release: `v0.1.0` (tag at 9ebf372, 2026-09-30). The release workflow failed at GCP
  auth, so no image exists in the registry yet
- Visibility: private

## Where it lives
- Repo: github.com/thinkmorestupidless/ankka-cloud
- Local: ~/Documents/projects/thinkmorestupidless/ankka-cloud
- Publishes: image `ankka-cloud` to `europe-west2-docker.pkg.dev/ankka-ops/ankka` on `v*` tags.
  No libraries
- Runs: will run on the hub cluster (`arrakis`) at `www.ankka.cloud`, namespace `ankka-cloud`,
  applied by Flux from `ankka-deployments`. Not composed there yet

## Depends on / depended on by
- **Consumes `ankka`** libraries `ankka-{sdk,runtime,http,controlplane-api,testkit}` at
  `ankkaVersion = "0.9.0"` from Maven Central, and the control plane's HTTP API over exactly seven
  routes: list, get, members, create-for-owner, quota, disable, enable, plus `GET /auth`. Never a
  project or service route, never a delete, never a member change.
- **Got from `ankka`**: feature 011 (hosted ankka: `ANKKA_ORGANIZATION_CREATION`, create with
  owner, published `ankka-controlplane-api`, `ankka-http` responses with headers; in v0.5.0),
  feature 015 (organization quotas; in v0.8.0), per-project pull credentials (0.7).
- **Would break it**: renaming any of the seven routes; changing `OrganizationSummary`,
  `MembersResponse`, `Owner`, `Quota` or `Role` wire types; changing the runtime DDL (caught by
  `schemaCheck`); renaming the `ankka-controlplane` scope, the `platform-admin` role, the realm
  name `ankka`, the `management` port name, or the image name `ankka-controlplane`.
- **Owns the product realm**: `kustomization/components/cloud/realm-import.json` replaces the
  platform's `KeycloakRealmImport ankka-realm` on the hub (registration, verified email, GitHub
  and Google login, clients `ankka-web` and `ankka-cloud`). Same realm name so every issuer path
  and the CLI's discovery hold.
- **Needs from `ankka-deployments`**: the hub composition in `docs/operating.md` (a Flux
  `GitRepository` at a tag, a `Kustomization` depending on `ankka-platform`, the realm-import
  delete patch, four Secrets written by Terraform, the `ankka-cloud` namespace). None of it is on
  ankka-deployments main. PR #9 there admits its release workflow to the push identity.
- **Version skew to resolve before deploying**: arrakis runs ankka v0.6.4, which lacks the quota
  route (v0.8.0). With a plan configured, the `capacity` step would 404, retry 30 times, abandon,
  and cancel the Stripe subscription. The hub must be on 0.8.0 or later first.
- ankka's feature 017 names ankka-cloud as "meant to be a second host" of the console package. Not reflected here yet.

## Read first
1. `CLAUDE.md`: what it is, commands, the rules that are not style, the traps.
2. `docs/design/hosted-ankka.md`: the treatment, D1 to D10, journeys, settled questions, follow-ons.
3. `specs/001-hosted-product/spec.md`, `research.md` (R1 to R14), `contracts/` (http-routes, stripe, control-plane-calls, keycloak-realm, configuration, kubernetes).
4. `specs/002-plan-capacity/spec.md`: quota set once after checkout, never re-set.
5. `docs/operating.md`: Stripe dashboard, Keycloak, refunds, deploying on the hub, the Secrets table.
6. `docs/local-development.md`: the compose stack.
7. `Cloud.scala`, `billing/Subscription.scala`, `provisioning/Provisioning.scala`, `provisioning/Enforcer.scala`: the inventory and the three mechanisms that carry money.

## Conventions specific to this repo
- Reads as the person (their token from the session), writes as the product (the `ankka-cloud`
  client-credentials token with `platform-admin`). Never mixed.
- The website holds no Keycloak credential and no card data. Every Stripe notice is verified and
  idempotent. Nothing is ever deleted or refunded by the website. Never re-enable what the website
  did not disable (`disabledByUs`).
- Upgrading ankka is changing `ankkaVersion` and running `just schema`; `schemaCheck` on every test run pins the committed DDL to the artifact.
- The realm import is one-shot; later realm changes are console jobs. Placeholders must be declared in `spec.placeholders` and filled by `compose/render-realm.sh`.
- Traps: realm `clientScopes` are exclusive, so request `openid` only; usernames are emails; MailHog on host port 8026.

## Open questions
- 2026-09-30: `v0.1.0` release run failed ("rejected by the attribute condition"). Re-run after ankka-deployments PR #9 merges and the ops root is applied.
- 2026-09-30: hub Flux composition not written; arrakis version skew (0.6.4 vs 0.8.0 minimum).
- 2026-09-30: `CLAUDE.md`, `build.sbt`, quickstart and task T045 reference `EndToEndSuite` and `ANKKA_CLOUD_E2E=on`, but no such suite exists in `src/test`.
- 2026-09-30: design D8 says `just up` targets kind; the Justfile's `up` is docker compose.
- Follow-ons from the design: per-installation scoping of the `platform-admin` credential, usage metering, seat pricing, free tier, hosting the ankka console.
- Terms and privacy pages are bundled placeholders until `ANKKA_CLOUD_TERMS_PATH` and `ANKKA_CLOUD_PRIVACY_PATH` are set.

## Recent moves
- 2026-09-30: PR #1 `002-plan-capacity`: ankka 0.9.0, quota provisioning step, capacity table, pricing copy. Tagged `v0.1.0`; release failed at auth.
- 2026-09-26: built against ankka 0.5.0, the release carrying the platform slice (feature 011).
- 2026-09-25 to 09-29: feature 001 specified, planned and implemented in ten phases; ran the real compose stack and found the clientScopes, email-username and MailHog-port traps; 48 cases green.
