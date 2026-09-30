# Conventions

How every repo in the family is built, tested, released and worked on. A repo file lists only its
deviations from this.

## Feature work: spec-kit
- `.specify/` with speckit 0.14.1, Claude integration, sequential numbering.
- `specs/NNN-name/` holds `spec.md`, `plan.md`, `research.md`, `data-model.md`, `contracts/`,
  `quickstart.md`, `tasks.md`, `checklists/`.
- `research.md` is the per-feature decision record: numbered R-entries, a "verify at
  implementation" list, a "found during implementation" section, measurements at the end.
- `spec.md` status lines are not maintained; the tick count in `tasks.md` is the real status.
- `.specify/memory/constitution.md` is the unfilled template in every repo. Plans use ankka's
  `CLAUDE.md` principles as the constitution check instead.
- Skills under `.claude/skills/speckit-*`.

## Build and test (JVM repos)
- Scala 3.9.0, JDK 21, sbt 1.12.15, munit, testcontainers. Docker required for tests.
- `.scalafmt.conf` copied from ankka: 3.9.4, scala3 dialect, maxColumn 100, `align.preset = more`,
  `SortModifiers` deliberately absent.
- `.githooks/pre-commit` runs the scalafmt native CLI on staged Scala; enable with `just hooks`.
  It refuses unformatted commits.
- Tests are serialised: `Tags.limit(Tags.Test, 1)` and `parallelExecution := false`. Do not
  optimise this back. Long suites run under `caffeinate -i`.
- Warning-free compile (`-Wunused`).
- No test names an image by a literal tag. No `ThisBuild / version`.
- `Justfile` is deliberately thin: every recipe is one command, and everything works without
  `just`. Logic lives in scripts (`kustomization/deploy-local.sh`).

## Git
- Feature branches as git worktrees, either under `.claude/worktrees/<name>` or as a sibling
  directory `../<repo>-<name>`. Dir names drift from branch names; check `git worktree list`.
- `main` is branch-protected; PRs only. Merge commits carry the PR number.
- Commit messages are sentence-case declarative statements of the resulting fact, often with the
  why after a colon. "Feature 009: ..." and "Tasks: T037" for spec-kit work.
- Trailer `Co-Authored-By: Claude Fable 5.1 <noreply@anthropic.com>` on commits made with Claude.

## CI
- GitHub Actions on `ubuntu-24.04`, Node 24 actions. Path-filtered jobs (dorny/paths-filter)
  gate what runs; extend the filter when a job reads a new part of the tree.
- CI never holds a cloud credential except the keyless release identity on tag builds.

## Release
- Push a `v*` tag. Only a tag publishes anything. The workflow refuses a dirty tree or a version
  that does not match the tag.
- Versions come from sbt-dynver. Placeholders `0.0.0` in the tree (pyproject, package.json,
  Cargo.toml, formula, plugin manifest) are rewritten by the release job.
- Published names are immutable (Maven Central, PyPI, GitHub Packages); never re-tag.
- First publish of a new npm or crates package is by hand; commands in ankka's CLAUDE.md.

## Documentation
- One tree `docs/` of Markdown with YAML frontmatter, built by ankka's docs tool (`tools/docs`,
  package `ankka-docs`), navigation in `mkdocs.yml`, everything repo-specific in `extra.docs`.
- A page stands alone and tells no history: no feature numbers, no `specs/` paths, no "decided
  on". `docs check` refuses them.
- Samples are included from tested code via `docs:start`/`docs:end` markers and `just docs-sync`.
- A behaviour change is a docs change. A new page must be in the nav and in a skill's `pages:`.
- Skills are curated in `tools/docs/skill/<name>/SKILL.md` and rendered into the marketplace
  plugin. Design records (`DESIGN.md`, `API.md`, `ROADMAP.md`) are not pages.

## Kubernetes
- Manifests canonical under `kustomization/components/`; JVM resources are symlinks into them.
- Operators follow render/execute: pure `render(resource, settings, observed)` returning actions,
  one executor doing server-side apply.
- Installation values never in a public repo. Overlays live in ankka-deployments.

## Agents working in the family
- Read the repo's `CLAUDE.md` before anything, then the family map here.
- Internal design treatments go to `ankka-deployments/design/`.
- Cross-repo decisions go to `decisions/` here, with a link from the in-repo record.
- Never publish by hand. Never re-tag. Never put a secret value in git.
