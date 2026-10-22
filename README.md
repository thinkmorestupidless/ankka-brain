# ankka-brain

Source of truth for everything an agent (or a new collaborator) needs to know about the public
ankka repositories that is not already written down inside one of them: what the family is, how
the repos relate, what each one owns, the decisions that cut across more than one, and the
conventions they all share.

The public family, as of 2026-10-10:

| Repo | Role |
|---|---|
| [ankka](repos/ankka.md) | the platform: runtime, operator, control plane, CLI, SDKs |
| [ankka-flow](repos/ankka-flow.md) | streaming pipelines beside ankka |
| [cloudflow](repos/cloudflow.md) | the maintained Cloudflow fork ankka-flow descends from |
| [satisfactory](repos/satisfactory.md) | constraint solving as a service, built on ankka |
| [ankka-reasoning](repos/ankka-reasoning.md) | a reasoning graph, built on ankka |
| [ankka-contrib](repos/ankka-contrib.md) | integrations on ankka's SDK that are not the platform's |
| [ankka-predictions](repos/ankka-predictions.md) | a prediction market's domain as libraries on ankka-core; the module that runs it is private |

The family also has private members: the hosted product, the infrastructure installations run on,
and products built on ankka for customers. They are mapped in `ankka-brain-private`, a sibling
checkout for those with access, which extends every file here. See `decisions/0011`.

## What lives here and what does not

Each repo already documents itself well: `CLAUDE.md`, `README.md`, `docs/`, design documents,
`ROADMAP.md`, `docs/decisions.md`. Those stay authoritative for anything inside one repo. This
brain never copies them; it points at them.

This brain holds only what has no single-repo home:

- the family map: thesis, who consumes what from whom, who pins which version of whom
- decisions that touch two or more repos
- conventions every repo follows, written once
- the vocabulary that crosses repo boundaries
- where things publish, as pointers
- status: what is in flight across the family and what is waiting on what

It never holds: installation values, cluster or cloud project names, secret names beyond the
public repos' own CI, a private repo's status or pins, or a design treatment. Those are the
private brain's.

## Layout

```
ankka-brain/
├── family.md            # the map: thesis, repo roles, dependency and pinning chain
├── principles.md        # beliefs that show up in real decisions across the family
├── conventions.md       # how every repo is built, tested, released; agent rules
├── glossary.md          # cross-repo vocabulary
├── infrastructure.md    # where things publish; pointers, never secrets
├── audiences.md         # who the family is for
├── voice.md             # how ankka talks in docs, READMEs, errors, CLI output
├── roadmap.md           # what is in flight and what waits on what, date-stamped
├── reuse-map.md         # what is shared, what should be, what deliberately is not
├── repos/               # one file per public repo, plus a status index
├── decisions/           # ADR-style log of cross-repo calls; links to in-repo logs
└── workspace/           # the CLAUDE.md to install in the directory above the checkouts
```

## Operating principles

1. **One source, many references.** If a fact lives in a repo, link to it. If it lives here, the
   repos link here. A fact in two places is wrong in one of them by next month.
2. **Short files beat long ones.** Under about 500 words each; the per-repo files run longer
   because they carry pins, open questions and housekeeping, and are the exception. Split
   anything else that grows past that.
3. **Be specific.** Exact versions, exact paths, exact names. "The current release" is useless
   in a month; "0.5.0, tagged 2026-09-26" is not.
4. **Date-stamp claims that decay.** Status, roadmap, what is in flight. Not principles or
   vocabulary.
5. **Update the index, not just the file.** A status change goes in `repos/README.md` too. A
   cross-repo decision goes in `decisions/`.
6. **Public means public.** Anything a private repo would not publish does not go here. When in
   doubt, it goes in the private brain with a role-only mention here.

## Wiring into Claude Code

Check out the family repos as siblings under one directory, this repo among them, and copy
`workspace/CLAUDE.md` into that directory. Claude Code loads a `CLAUDE.md` from every directory
above the one it starts in, so the brain's always-on files reach every session in every family
repo. `workspace/README.md` has the steps and the one approval prompt to expect.

Each family repo's own `CLAUDE.md` may also add a line pointing here, for sessions started
without the workspace file:

```
The family-level context (repo map, cross-repo decisions, shared conventions) lives in the
sibling checkout ../ankka-brain. Read ../ankka-brain/family.md and ../ankka-brain/repos/<this-repo>.md
before cross-repo work.
```

Those with access to the private repos add `ankka-brain-private` beside this checkout and its
`workspace/CLAUDE.local.md` beside the workspace `CLAUDE.md`.

## Maintenance ritual

- **When a release is cut** in any public repo: update the version table in `family.md` and the
  repo's row in `repos/README.md`.
- **When a feature spec is opened or closed:** update `roadmap.md`.
- **When a decision touches two repos:** log it in `decisions/`, then link it from each repo's
  in-repo log or design doc. If a private repo is affected, log it in the private brain instead.
- **When the same thing is built twice:** add a row to `reuse-map.md`.
- **When a repo changes visibility:** move its file, its rows and its decisions to the other brain
  in one commit on each side.
- **Monthly:** re-read `family.md` and `roadmap.md` for anything that has quietly gone stale.
