# ankka-papercast

## One-liner
A podcast of the scientific literature on ankka: a listener saves a search with a cadence, and on
that cadence the service searches arXiv, PubMed and Semantic Scholar, summarises the papers, writes a
script and records it.

## Why it exists
It is the application built on camera in ankka's first long-form "build it" video (`B1` in the
videos plan): non-trivial, with both a deterministic half (accounts, schedules, a recurring timer, a
durable pipeline) and a non-deterministic one (two model calls, a check on what the model wrote, a
text-to-speech call that can fail). Its own repo so that `main` is a fixed starting point every take
resets to. The name `papercast` on GitHub belongs to an earlier version on another platform, which
the video never shows.

## Status
- Stage: designing (spec 001 written, checker green; nothing built)
- As of: 2026-10-30
- Current release: unreleased
- Visibility: private until it is made public; public by decision

## Where it lives
- Repo: github.com/thinkmorestupidless/ankka-papercast
- Local: ~/Documents/projects/thinkmorestupidless/ankka-papercast
- Publishes: nothing yet; the template's `deploy.yml` deploys on a `v*` tag once its secrets exist
- Runs: nowhere yet

## Depends on / depended on by
- ankka 0.14.2: `ankkaVersion` in `build.sbt` and `runtime` in `service.json`, moved together;
  Maven Central. The spec-kit extensions are copied from ankka at the same version.
- Nothing depends on it.

## Read first
- `specs/001-papercast/spec.md`: the six stories and their requirements
- `features/` and `GLOSSARY.md`: the 34 scenarios and their words
- `CLAUDE.md`: the principles a plan checks against
- `docs/limitations.md`: what it will not do

## Conventions specific to this repo
- `main` is the recording's starting point: three commits (`ankka init papercast`, spec-kit, the
  spec) and a README fix. A build runs on branch `001-papercast` with `/speckit-design --from plan`
  then `/speckit-build`; a dry run's pull request is closed, not merged.
- No docs site and no marketplace plugin.

## Open questions
- 2026-10-30: when to make the repository public (before the dry run or with the video).
- 2026-10-30: the installation, issuer and credentials the deploy half of the recording uses.

## Recent moves
- 2026-10-30: created from `ankka init papercast` at 0.14.2, with spec-kit and spec 001.
