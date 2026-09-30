# 0007 — One docs tool and one marketplace, one plugin per project

## Status
Accepted 2026-09-27/28 (recorded 2026-09-30)

## Repos affected
- ankka: owns `tools/docs` (package `ankka-docs`) and the `marketplace/` subtree; its release
  replaces only `plugins/ankka/` in the marketplace repo. Everything ankka-specific lives in
  `mkdocs.yml` `extra.docs`, not in the Python tool.
- satisfactory, ankka-flow: depend on `ankka-docs` by git dependency; each release pushes its own
  `plugins/<project>/` into `thinkmorestupidless/ankka-marketplace`, never force-pushing.
- Any future family repo does the same.

## Context
Three repos needed the same docs pipeline (frontmatter, includes from tested code, generated
reference blocks, skills rendered into a plugin) and the same Claude Code distribution.

## Decision
The docs tool is shared from ankka (PR #26, branch `shared-docs-tool`). The marketplace is one
repo with one plugin per project (PRs #28, #29). Users add one marketplace and install
`ankka@ankka`, `satisfactory@ankka`, `ankka-flow@ankka`.

## Alternatives considered
1. **A docs tool per repo.** Lost: three copies of the same Python drifting.
2. **A marketplace per repo.** Lost: three things for a user to add; the family is one product surface.
3. **Publish `ankka-docs` to PyPI.** Not yet; git dependency is enough while the tool changes weekly.

## Consequences
- Downstream should pin the docs tool to `ankka@main` or a tag. satisfactory pins the
  `shared-docs-tool` branch, which is merged content but not an ancestor of main; repin.
- `tools/docs` behaviour and the `extra.docs` contract are a public interface of ankka now.

## Related
- ankka `docs/contributing/documentation.md`; `marketplace/README.md`
- `../reuse-map.md`
