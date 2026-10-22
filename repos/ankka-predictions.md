# ankka-predictions

## One-liner
The domain of a prediction market as two libraries on `ankka-core`: `exchange`, a limit order
book per outcome where outcomes pay one unit, and `markets`, the lifecycle of a market from draft
to final; each a pure domain and a components artefact with ledger, gate and resolution ports.

## Why it exists
A prediction market was first specified here as a product reusing a casino's services. Working
through a shared wallet and the same players across casino and market made the market a module
the casino's owner enables per brand, and that owner is another company. What is left is neither
theirs nor gambling's: the rules of a book over outcomes that pay one unit, and a market's
lifecycle. Those depend on `ankka-core` alone, so they are a public repository; the module that
composes them, with its gates, wallet, facts and brands, is the other company's and private. The
repo's `docs/decisions.md` D1 and D2 say why.

## Status
- Stage: designing; specs 004 to 006 drafted, nothing of the libraries built; the template's stub
  is still at the root
- As of: 2026-10-22
- Current release: unreleased
- Visibility: public by decision (D2); created private on 2026-10-22 and made public once the
  pages that restate the consuming module's contracts are out of the tree

## Where it lives
- Repo: github.com/thinkmorestupidless/ankka-predictions
- Local: ~/Documents/projects/thinkmorestupidless/ankka-predictions
- Publishes: nothing yet. On a `v*` tag: `exchange-domain`, `exchange-components`,
  `markets-domain`, `markets-components` to Maven Central under `com.thinkmorestupidless`
- Runs: nowhere; libraries, not a service

## Depends on / depended on by
- **ankka**: `ankka-core` alone for the domains; the SDK and testkit for the components. Pinned
  in `build.sbt`; will follow the family's current release.
- **ankka-reasoning**: not a dependency. The consuming module writes the market layer; the
  libraries hold no reasoning vocabulary.
- **Depended on by** a private module outside the family's repositories, which registers the
  components, supplies the ports and owns the endpoints, facts and brands. What that module needs
  flows here as requirements only, never as code; what this repo asks of it is `notes/casino-requests.md`.
- What must not break: the events, wire names and manifests of the two components artefacts.
  They are a persisted protocol in the consumer's journals and evolve additively (spec 004).

## Read first
1. `CLAUDE.md`: the spec table and which specs the checker reads.
2. `docs/decisions.md`: D1 (two libraries, not a product) and D2 (public; the module private).
3. `specs/004-libraries/spec.md`: the split, the ports, the journal as a contract, publishing.
4. `specs/005-exchange-domain/spec.md` and `specs/006-markets-domain/spec.md`: the two values,
   their commands, events and rules.
5. `GLOSSARY.md` and `features/exchange/`, `features/markets/`, `features/libraries/`: the words
   and the scenarios the specs name.
6. `docs/product.md` and `docs/architecture.md`: the earlier product shape; read as history until
   rewritten.

## Conventions specific to this repo
- Spec-kit with the bdd extension (as voice); the checker reads specs from 004, and 001 to 003 are
  kept as written and superseded.
- One sbt build; the pure domains under `modules/<name>-domain/` on `ankka-core` alone, the
  components beside them.
- Money is minor units as `Long`; every money command is keyed; events that move money name the
  amount, the owner and the key, and the libraries never hold money themselves.

## Open questions
- 2026-10-22: the pages that restate the consuming module's contracts (`docs/architecture.md`'s
  wallet routes, specs 001 to 003, `features/money/`, `features/projects/`) are still in the tree;
  the repository stays private until they are out.
- 2026-10-21: spec 005 carries two clarifications (whether limits may change on an open exchange;
  whether a taker gets the better price across own-outcome sells and other-outcome buys); spec
  006 one (whether a resolved market may be voided within its review window).
- 2026-10-21: the constitution's principles I, III, VI and VII still describe the product; II was
  amended to one value per market.

## Recent moves
- 2026-10-22: repository created on GitHub, private for now; D2 recorded; spec 004 amended to
  publish to Maven Central.
- 2026-10-21: D1; specs 005 and 006 written; the features reshaped to the libraries; 001 to 003
  superseded; the checker at 0 findings over 156 scenarios.
- 2026-10-20: scaffolded from the ankka template beside the product specs 001 to 003.
