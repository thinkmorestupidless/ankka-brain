# Conventions

How every repo in the family is built, tested, released and worked on. A repo file lists only its
deviations from this.

## Feature work: spec-kit
- `.specify/` with speckit 0.14.1, Claude integration, sequential numbering, and two extensions:
  `speckit-bdd` (`speckit-bdd` v0.2.0: the `bdd` preset, `features/`, `GLOSSARY.md`, the checker
  as `just check`) and `review` (ankka's `.specify/extensions/review/` and
  `.claude/skills/speckit-review/`, with the `after_implement` hook in `.specify/extensions.yml`;
  copied from ankka, where it is kept). A repo without either gets it before its next spec.
- `specs/NNN-name/` holds `spec.md`, `plan.md`, `research.md`, `data-model.md`, `contracts/`,
  `quickstart.md`, `tasks.md`, `checklists/`. Scenarios live once, in `features/`; the spec
  names them.
- `research.md` is the per-feature decision record: numbered R-entries, a "verify at
  implementation" list, a "found during implementation" section, measurements at the end.
- `spec.md` status lines are not maintained; the tick count in `tasks.md` is the real status.
- `.specify/memory/constitution.md` is the unfilled template in every repo. Plans use ankka's
  `CLAUDE.md` principles as the constitution check instead.
- Skills under `.claude/skills/speckit-*`.

### The feature workflow (2026-10-27)
One feature, one branch `NNN-name`, one worktree. Two commands, from the `design-build` spec-kit
extension (ankka spec 061), each on its own model:

| Command | Model | Runs | Produces | Asks Trevor |
|---|---|---|---|---|
| `/speckit-design <description>` | **Fable** (`model: fable` in its skill) | specify (the bdd hook writes the scenarios and glossary terms), the spec's clarifications, clarify (the bdd hook runs the checker first), the checker until it reports nothing, plan, tasks, analyze with every remediation that changes no decision and no scenario applied and analyze run again, at most three rounds, until nothing above low remains; a commit after each stage | `spec.md`, `features/`, `GLOSSARY.md`, `plan.md`, `research.md`, `data-model.md`, `contracts/`, `quickstart.md`, `tasks.md`, ending with the line that starts the build | every `NEEDS CLARIFICATION`, clarify's questions one at a time, each checker finding that is a question, the proposed glossary terms, and every remediation that would change a decision or a scenario; always through AskUserQuestion, so the turn and its model hold |
| `/speckit-build` | **Opus** (`model: opus` in its skill) | implement, each task committed and the next started without a report; the review before the pull request (`/speckit-review`: the `review` hook's reviewer with a fresh context running `pr-review`); the draft pull request on the right base; `gh pr checks --watch` (no `--fail-fast`) until every check finishes; `gh pr ready` when each is green or shown to be a flake of `main`; it refuses a task list whose `research.md` lacks the design's `## Design finished` | the code and its suites, the ticks in `tasks.md`, `review.md` with a resolution under every finding, a pull request ready for review | only a decision the spec and plan left open, a change to a contract, a check it cannot make pass, or a blocking review finding it disagrees with |

Rules that go with the table:
- **The models are the skills' `model:` lines**, applied for the turn the command is typed in, and
  nothing else records them. The deeper-thinking model makes every decision; the cheaper one writes
  the code once everything is decided. The build reads `.specify/feature.json`, the plan and
  `tasks.md` and nothing of the design's session, so it may run in a fresh one.
- **The design refuses** on the default branch, without `bdd` or `review`, in a session that cannot
  ask (a headless run, a subagent), and for a `--from <stage>` whose inputs are missing. **The build
  refuses** with no `tasks.md`, without `review`, without the `pr-review` plugin in the session
  (`/plugin install pr-review@ankka`), or with uncommitted work outside implementing. Both read
  where they stand from the tree, so typing the command again resumes a run that was cut short.
- **The review before the push (2026-10-24).** The reviewer is a subagent given the spec, the plan
  and the branch and nothing of the implement session: the implementer knows what it meant and
  reads that instead of what it wrote. Blocking and should-fix findings are fixed in commits grouped
  by concern or declined with a reason; `review.md` records each; one re-review of the fixes at most.
  The PR body carries the reviewer's verdict after the fixes, never the implementer's own.
- A feature that depends on a sibling repository's unmerged work codes against that work's
  approved `contracts/`, never against its routes or code, and says so in the PR.
- **The base branch.** The PR targets `main` unless the feature's branch was cut from another
  feature branch that is not yet merged; then it targets that branch and the PR's first line says
  which PR it waits on. When the base PR merges, the stacked PR is retargeted to `main` and rebased
  before it is reviewed.
- **The PR.** Title `<type>(<NNN>): short description` (the Git section below); body from
  `.github/pull_request_template.md`: the spec's path and the tick count under Why, the commands run
  and their results under How it is tested, the contracts touched (ideally none) and what it waits
  on under Left out, the review lines, and the Claude Code trailer.
- **CI before a human.** The PR is a draft until every check passes; only then is it marked ready
  and Trevor told a review is waiting. A red check is fixed on the branch, or shown with evidence
  (the same job failing the same way on a recent run of `main`) to be a flake of `main`, said in the
  PR. A PR whose base or body changes goes back through the same gate. Trevor's time is spent on
  green PRs only (2026-10-22).
- **Repairing one stage.** Every stage is still its own command (`/speckit-specify`,
  `/speckit-clarify`, `/speckit-plan`, `/speckit-tasks`, `/speckit-analyze`, `/speckit-implement`,
  `/speckit-review`); run one alone, then `/speckit-design --from <stage>` or `/speckit-build` picks
  up from what the tree holds.
- Nothing is committed to `main`, tagged, published or deployed by an agent at any step, and Trevor
  approves and merges every PR; `main` is branch-protected everywhere.

## Build and test (JVM repos)
- Scala 3.9.0, JDK 21, sbt 1.12.15, munit, testcontainers. Docker required for tests.
- `.scalafmt.conf` copied from ankka: 3.9.4, scala3 dialect, maxColumn 100, `align.preset = more`,
  `SortModifiers` deliberately absent.
- `.githooks/pre-commit` runs the scalafmt native CLI on staged Scala and `.githooks/commit-msg`
  checks the message is a Conventional Commit; enable both with `just hooks`.
- Tests are serialised: `Tags.limit(Tags.Test, 1)` and `parallelExecution := false`. Do not
  optimise this back. Long suites run under `caffeinate -i`.
- Warning-free compile (`-Wunused`).
- No test names an image by a literal tag. No `ThisBuild / version`.
- `Justfile` is deliberately thin: every recipe is one command, and everything works without
  `just`. Logic lives in scripts (`kustomization/deploy-local.sh`).

## Git
- Feature branches as git worktrees, either under `.claude/worktrees/<name>` or as a sibling
  directory `../<repo>-<name>`. Dir names drift from branch names; check `git worktree list`.
- `main` is branch-protected; PRs only, squash-merged: the PR title becomes the commit on `main`
  (the repository setting takes the PR title when the PR has more than one commit).
- **Commit messages are Conventional Commits 1.0.0 (2026-10-24).** Header
  `<type>(<scope>)!: <description>`, under 72 characters: a type from `feat fix docs test refactor
  perf build ci chore style revert`; the scope is the spec number for spec work (`feat(057): project
  tokens and the administrator`), otherwise the area (`fix(operator): ...`, `docs: ...`, `build(deps):
  ...`); `!` when the change breaks a contract, with a `BREAKING CHANGE: <what and how to migrate>`
  footer; the description a lower-case imperative phrase with no trailing period. The body says
  why and what the diff cannot, wrapped at 72, in the family's voice (the resulting fact, the
  reason after a colon). Spec-kit task commits name their tasks in a `Tasks: T037, T038` footer.
  Before this date messages were sentence-case statements ("Feature 009: ...", "Tasks: T037"); do
  not rewrite history to match. `.githooks/commit-msg` refuses anything else (`just hooks`);
  Dependabot and Scala Steward are configured to `build(deps)`.
- **The PR title is the same header**, since it becomes `main`'s commit: `feat(057): short
  description`. The spec number is the scope, not a prefix of its own.
- **The PR body follows `.github/pull_request_template.md`**: Why, What changed, Left out, How it
  is tested, Risk and rollback; under about 300 words outside the fenced block of commands and
  results, bullets of at most two lines, the detail in the spec, `research.md` or the docs and
  linked. A body that has to be scrolled is a defect in the body. Agents write it from the
  template file; `gh pr create --body-file` does not insert it for them.
- Trailer `Co-Authored-By: Claude Fable 5.1 <noreply@anthropic.com>` on commits made with Claude,
  and `🤖 Generated with [Claude Code](https://claude.com/claude-code)` at the end of a PR body an
  agent wrote.
- The template, the hook and these rules are the defaults of every application started from
  ankka's templates (`ankka init`, `sbt new thinkmorestupidless/ankka.g8`), not only the family's
  own repositories.

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

## The brains are pushed on every commit

`ankka-brain`, `ankka-brain-private` and `voice-brain` are how sessions and agents synchronise:
a coordinator writes the ledger, a worker reads it, a fresh session resumes from it, Trevor reads
it from anywhere. A commit that stays on one machine synchronises nobody. So a brain commit is
pushed in the same step (`git commit && git push`), small and often, directly to `main`; the
brains carry no code and need no review gate. Before writing to a brain, pull. (Rule from
2026-10-24: run 1 of programme 001 left 54 private-brain commits unpushed for two days, and the
remote, the thing the experiment was meant to test as the synchronisation channel, said nothing
had happened.)
