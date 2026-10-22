# 0008 — Versions come only from tags; downstream pins releases

## Status
Accepted (predates this log; recorded 2026-09-30)

## Repos affected
Every repo: no `ThisBuild / version`, no version file; sbt-dynver from the nearest `v*` tag;
placeholders `0.0.0` in the tree rewritten by release jobs; only a tag publishes; the workflow
refuses a dirty tree or a tag that does not match. Consumers pin a published release:
an application a Maven Central version, an installation a git tag, ankka-flow and satisfactory a
tag of the docs tool.

## Context
Published names are immutable on Maven Central, PyPI, GitHub Packages and crates.io. A version
written in a file drifts from the tag; a publish by hand cannot be reproduced.

## Decision
The tag is the version and the only trigger. Cross-repo pins name releases, not branches or
snapshots. A snapshot pin is a temporary exception that must name the commit it is waiting for.

## Alternatives considered
1. **Version in a file, bumped by commit.** Lost: two sources of truth.
2. **Publish snapshots for downstream.** Lost: Maven Central has no snapshot entitlement here, and
   a snapshot pin hides the real dependency.

## Consequences
- satisfactory's `V.ankka` snapshot pin (waiting for commit 0c2abd6 in a release) is out of
  policy and named in `../roadmap.md`.
- First publish of a new npm or crates package is by hand, once, then never again.

## Related
- ankka `CLAUDE.md` "Publishing"; cloudflow `ROADMAP.md` (tag-driven release, `v0.1.0` naming)
- the infrastructure repo's rule that a release is one commit (private brain)
