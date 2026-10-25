# Repos

One file per public repository. This table is the at-a-glance board; each file has the detail.
The private repositories have the same board in `ankka-brain-private/repos/README.md`.

| Repo | Role | Stage | Release | Pins ankka at | Last touched | Visibility |
|---|---|---|---|---|---|---|
| [ankka](ankka.md) | the platform | released, active daily | v0.14.0 (2026-10-25) | n/a | 2026-10-25 | public |
| [ankka-flow](ankka-flow.md) | streaming pipelines | released, feature 002 in flight | v0.1.0 (2026-09-28) | none (runs beside) | 2026-09-29 | public |
| [cloudflow](cloudflow.md) | Cloudflow fork | maintenance, frozen | v0.1.0 (Akka line) | none | 2026-09-20 | public |
| [ankka-satisfaction](satisfactory.md) | constraint solving on ankka (product `satisfactory`) | released | v0.1.0 (2026-09-29) | 0.7.1 snapshot | 2026-09-29 | public |
| [ankka-reasoning](ankka-reasoning.md) | reasoning graph on ankka | released | 0.1.1 | ankka 0.10.0, ankka-flow 0.3.0 | 2026-10-06 | public |
| [ankka-contrib](ankka-contrib.md) | integrations on ankka's SDK | released | v0.1.0 (2026-10-08) | 0.11.0 | 2026-10-08 | public |
| [ankka-predictions](ankka-predictions.md) | a prediction market's domain as libraries | designing, specs 004 to 006 drafted | unreleased | to be set in `build.sbt` | 2026-10-22 | public by decision; private until the consumer's pages are out |

Status as of 2026-10-10 for ankka-contrib; the other rows are as of the date in their last
column and are known to be behind (ankka-flow is at v0.5.0).

## Related repositories without a file here
Owned by the family but produced from `ankka` by subtree push on release; never edited directly:

| Repo | Produced from |
|---|---|
| `thinkmorestupidless/ankka.g8` | `ankka/ankka.g8` |
| `thinkmorestupidless/ankka-action` | `ankka/action` |
| `thinkmorestupidless/ankka-marketplace` | `ankka/marketplace`, plus `plugins/<project>/` pushed by satisfactory, ankka-flow and ankka-reasoning releases |
| `thinkmorestupidless/homebrew-tap` | `ankka/homebrew` |

## Stage legend
- **designing**: docs and specs only, nothing runs
- **building**: code exists, no release
- **released**: at least one tag published; may still be active daily
- **maintenance**: frozen for consumers, patches only
- **archived**: no further work

## How to add a repo
1. Decide which brain it belongs in: public repos here, private repos in `ankka-brain-private`.
2. Copy `_template.md` to `repos/<name>.md` and fill it from the repo's own docs.
3. Add a row above and to the map in `../family.md`.
4. If it consumes or is consumed by a sibling, add the pin to the chain table in `../family.md`.
5. Add the repo's in-repo decision log to the table in `../decisions/README.md`.
