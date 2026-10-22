# Conventions

How every repo in the family is built, tested, released and worked on. A repo file lists only its
deviations from this.

## Feature work: spec-kit
- `.specify/` with speckit 0.14.1, Claude integration, sequential numbering, and the
  `speckit-bdd` extension (`speckit-bdd` v0.2.0: the `bdd` preset, `features/`, `GLOSSARY.md`,
  the checker as `just check`). A repo without the extension gets it before its next spec.
- `specs/NNN-name/` holds `spec.md`, `plan.md`, `research.md`, `data-model.md`, `contracts/`,
  `quickstart.md`, `tasks.md`, `checklists/`. Scenarios live once, in `features/`; the spec
  names them.
- `research.md` is the per-feature decision record: numbered R-entries, a "verify at
  implementation" list, a "found during implementation" section, measurements at the end.
- `spec.md` status lines are not maintained; the tick count in `tasks.md` is the real status.
- `.specify/memory/constitution.md` is the unfilled template in every repo. Plans use ankka's
  `CLAUDE.md` principles as the constitution check instead.
- Skills under `.claude/skills/speckit-*`.

### The feature workflow (2026-10-22)
One feature, one branch `NNN-name`, one worktree. The steps, in order, and who runs them:

| Step | Command | Model | Produces | Stop for Trevor? |
|---|---|---|---|---|
| 1 specify | `/speckit-specify` | Fable | `spec.md`; the bdd hook writes the scenarios into `features/` and the glossary | **yes**: every `NEEDS CLARIFICATION` the spec raises is put to Trevor as options with implications; the spec is not planned until he answers |
| 2 clarify | `/speckit-clarify` | Fable | the bdd hook runs the checker first and each finding becomes a question; answers are encoded into the spec | **yes**: the questions are Trevor's |
| 3 plan | `/speckit-plan` | Fable | `plan.md`, `research.md`, `data-model.md`, `contracts/`, `quickstart.md` | no, unless the plan finds a decision the spec left open |
| 4 tasks | `/speckit-tasks` | Fable | `tasks.md` | no |
| 5 analyze | `/speckit-analyze` | Fable | findings across spec, plan and tasks | no: every remediation it suggests is applied, then analyze runs again; it stops when no finding above low remains, or the rest is put to Trevor |
| 6 implement | `/speckit-implement` | **Opus** (`claude --model opus` in the worktree) | the code, the ticks in `tasks.md`, the suites | **yes** on anything the spec or plan left open or that would change a contract |
| 7 pull request | `gh pr create` | Opus | the PR, from the branch, on the right base (below), as a draft until its CI is green | **yes**, once CI passes: Trevor approves and merges; an agent never merges |

Rules that go with the table:
- Steps 1 to 5 are one session on Fable; step 6 is a fresh session on Opus started in the same
  worktree, which reads `.specify/feature.json`, `tasks.md` and the plan and nothing from the
  earlier session. The switch is the point: the planning model is not asked to keep the context.
- `just check` is green before step 3 and before step 7.
- A feature that depends on a sibling repository's unmerged work codes against that work's
  approved `contracts/`, never against its routes or code, and says so in the PR.
- **The base branch.** The PR targets `main` unless the feature's branch was cut from another
  feature branch that is not yet merged; then it targets that branch and the PR says which PR it
  waits on. When the base PR merges, the stacked PR is retargeted to `main` and rebased before
  it is reviewed. Never target a branch whose PR Trevor has not approved without saying so in the
  PR's first line.
- **The PR.** Title in the family's commit style; body with the spec's path, the tick count, the
  commands run and their results, the contracts touched (ideally none), what it waits on, and the
  Claude Code trailer. Trevor approves and merges; `main` is branch-protected everywhere.
- **CI before a human.** A PR is raised as a draft (`gh pr create --draft`) and stays one until
  every CI check on it passes; only then is it marked ready (`gh pr ready`) and Trevor told a
  review is waiting. A red check is the agent's to attend to first: read the failing job, fix it on
  the branch or show with evidence that it is a known flake of `main`, and say which in the PR. A
  PR whose base or body changes (a stacked PR merged into it, a rebase) goes back through the same
  gate before the next request for review. Trevor's time is spent on green PRs only (2026-10-22).
- A remediation from step 5 that would change a scenario goes through the features file first and
  `just check` again; a remediation that would change a decision is a question for Trevor.
- Nothing is committed to `main`, tagged, published or deployed by an agent at any step.

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
- Installation values never in a public repo. Overlays live in the private infrastructure repo.

## Agents working in the family
- Read the repo's `CLAUDE.md` before anything, then the family map here.
- Internal design treatments go to the private infrastructure repo, never a public one; the private brain names it.
- Cross-repo decisions go to `decisions/` here, or to the private brain when a private repo is affected, with a link from the in-repo record.
- Never publish by hand. Never re-tag. Never put a secret value in git.
