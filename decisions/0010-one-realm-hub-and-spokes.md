# 0010 — One identity realm, hub and spokes, and ankka-cloud owns the product realm

## Status
Accepted 2026-09-24 (recorded 2026-09-30)

## Repos affected
- ankka: realm name `ankka`, clients `ankka-cli` and `ankka-console`, scope `ankka-controlplane`,
  role `platform-admin` are wire names now; renaming any of them breaks ankka-cloud. A spoke trusts
  a hub via `ANKKA_AUTH_ISSUER` and `ANKKA_AUTH_JWKS_URL`. `ANKKA_ORGANIZATION_CREATION=platform-admin`
  makes an installation hosted.
- ankka-cloud: owns `realm-import.json` for the hub, same realm name, with registration, verified
  email, social login and the `ankka-web` and `ankka-cloud` clients.
- ankka-deployments: on the hub, deletes the platform's `KeycloakRealmImport ankka-realm` with a
  `$patch: delete` and applies ankka-cloud's instead; writes the four Secrets the realm needs.

## Context
The hosted product needs one place a stranger can register and one Stripe account, while
installations may be many. Every installation already runs Keycloak with realm `ankka`.

## Decision
One realm, on the hub, trusted by every spoke. The website is only an OIDC client of it. An
organization lives on exactly one installation; the registry of installations is configuration.
arrakis is both the hub and the first installation.

## Alternatives considered
1. **A realm per installation with federation.** Lost: N places to register, N Stripe mappings.
2. **The website holding a Keycloak admin credential.** Lost: the website must hold nothing able
   to act on Keycloak; realm changes after import are console jobs.

## Consequences
- The realm import is one-shot; changing it later is an operator task, not a redeploy.
- `platform-admin` breadth across installations is accepted for v1; per-installation scoping is a
  named follow-on.
- The hub composition in ankka-deployments does not exist yet (see `../roadmap.md`).

## Related
- ankka-deployments `design/hosted-ankka.md` D3, D7, D8; ankka `specs/011-hosted-ankka/`
- ankka-cloud `specs/001-hosted-product/contracts/keycloak-realm.md`
