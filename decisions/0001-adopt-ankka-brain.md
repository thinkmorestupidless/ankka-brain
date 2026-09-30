# 0001 — A brain repository for the ankka family

## Status
Accepted 2026-09-30

## Repos affected
All six. Each gains a line in its `CLAUDE.md` pointing at `../ankka-brain`. None moves any content here.

## Context
Six repositories share a thesis, a Pekko line, a release model, a docs tool, a marketplace, an
identity realm and an infrastructure story. Each documents itself well, but the picture across
them (who pins what, what waits on what, which decisions constrain a sibling) lived only in
scattered `CLAUDE.md` paragraphs and in Trevor's head. Agents working in one repo routinely
needed facts from another. A thinkmorestupidless-wide brain was tried on 2026-05-06 and stayed a
skeleton; the ankka family is where the cross-repo knowledge actually is.

## Decision
A separate repository `ankka-brain`, sibling checkout to the family repos, holding only what has
no single-repo home: the family map, cross-repo decisions, shared conventions, vocabulary,
infrastructure pointers, roadmap and reuse map, plus one short file per repo that points into
that repo's own docs. Plain Markdown in git, wired into Claude Code by a `CLAUDE.md` with
`@imports` and a pointer line in each repo.

## Alternatives considered
1. **Fill the thinkmorestupidless-wide brain instead.** Lost: the company brain has to cover
   voice, hobby projects and consulting demos; the ankka family needs a denser, more technical
   map, and a family-scoped repo can be read by anyone who later works on ankka.
2. **A `docs/family.md` inside ankka.** Lost: ankka is public and the family includes a private
   infrastructure repo and a private product; the map would either leak or be incomplete.
3. **Nothing new; keep growing the `CLAUDE.md` files.** Lost: ankka's is already about 1,600
   lines, and the cross-repo facts were duplicated between them and drifting.

## Consequences
- Easier: one place to answer "what pins what" and "what waits on what".
- Harder: another thing to keep current. The maintenance ritual in `README.md` names the triggers.
- Rule: nothing installation-specific and no secret values, even though the repo may be private.

## Related
- `../README.md`, `../family.md`
- thinkmorestupidless brain `decisions/0001-adopt-brain-system.md` (2026-05-06)
