# ankka family workspace

Every directory beside this file is a checkout of one ankka family repository, or unrelated.
`ankka-brain/` is the family-level context: the map of the public repos, their pins, the
cross-repo decisions and the shared conventions. Read it before cross-repo work; each repo's own
`CLAUDE.md` stays authoritative inside that repo.

If the task is not in an ankka family repo, ignore the rest of this file.

@ankka-brain/family.md
@ankka-brain/principles.md
@ankka-brain/repos/README.md

Load on demand from `ankka-brain/`: `conventions.md` before writing code, `glossary.md` for a
term, `roadmap.md` before planning, `infrastructure.md` before a release, `audiences.md` and
`voice.md` before writing anything public, `repos/<name>.md` for the repo you are in and any it
depends on, `decisions/` for why things are shaped as they are.

A `CLAUDE.local.md` beside this file, when present, adds the private members of the family from
`ankka-brain-private/`. It is never committed to a public repo.
