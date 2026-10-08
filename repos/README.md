# Repos

One file per repository. This table is the at-a-glance board; each file has the detail.

| Repo | Role | Stage | Release | Pins ankka at | Last touched | Visibility |
|---|---|---|---|---|---|---|
| [ankka](ankka.md) | the platform | released, active daily | v0.9.1 (2026-09-29) | n/a | 2026-09-30 | public |
| [ankka-cloud](ankka-cloud.md) | hosted product | released, not deployed | v0.1.0 (2026-09-30), release run failed | 0.9.0 | 2026-09-30 | private |
| [ankka-deployments](ankka-deployments.md) | infrastructure | building, arrakis live | none of its own | v0.6.4 | 2026-09-30 | private |
| [ankka-flow](ankka-flow.md) | streaming pipelines | released, feature 002 in flight | v0.1.0 (2026-09-28) | none (runs beside) | 2026-09-29 | public |
| [cloudflow](cloudflow.md) | Cloudflow fork | maintenance, frozen | v0.1.0 (Akka line) | none | 2026-09-20 | public |
| [satisfactory](satisfactory.md) | constraint solving on ankka | released | v0.1.0 (2026-09-29) | 0.7.1 snapshot | 2026-09-29 | public |
| [ankka-reasoning](ankka-reasoning.md) | reasoning graph on ankka | released | 0.1.1 | ankka 0.10.0, ankka-flow 0.3.0 | 2026-10-06 | public |
| [ankka-casino](ankka-casino.md) | eitheror's casino and payments on ankka (eitheror-io) | designing, 4 of 19 specs | unreleased | not yet; needs 038 to 044 | 2026-10-08 | private |

Status as of 2026-09-30.

## Related repositories without a file here
Owned by the family but produced from `ankka` by subtree push on release; never edited directly:

| Repo | Produced from |
|---|---|
| `thinkmorestupidless/ankka.g8` | `ankka/ankka.g8` |
| `thinkmorestupidless/ankka-action` | `ankka/action` |
| `thinkmorestupidless/ankka-marketplace` | `ankka/marketplace`, plus `plugins/<project>/` pushed by satisfactory and ankka-flow releases |
| `thinkmorestupidless/homebrew-tap` | `ankka/homebrew` |

## Stage legend
- **designing**: docs and specs only, nothing runs
- **building**: code exists, no release
- **released**: at least one tag published; may still be active daily
- **maintenance**: frozen for consumers, patches only
- **archived**: no further work

## How to add a repo
1. Copy `_template.md` to `repos/<name>.md` and fill it from the repo's own docs.
2. Add a row above and to the map in `../family.md`.
3. If it consumes or is consumed by a sibling, add the pin to the chain table in `../family.md`.
4. Add the repo's in-repo decision log to the table in `../decisions/README.md`.
