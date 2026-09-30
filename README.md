# ankka-brain

Source of truth for everything an agent (or a new collaborator) needs to know about the ankka
family of repositories that is not already written down inside one of them: what the family is,
how the repos relate, what each one owns, the decisions that cut across more than one, and the
conventions they all share.

The family, as of 2026-09-30:

| Repo | Role |
|---|---|
| [ankka](repos/ankka.md) | the platform: runtime, operator, control plane, CLI, SDKs |
| [ankka-cloud](repos/ankka-cloud.md) | the hosted product: sign up, subscribe, get an organization |
| [ankka-deployments](repos/ankka-deployments.md) | the infrastructure an installation runs on (private) |
| [ankka-flow](repos/ankka-flow.md) | streaming pipelines beside ankka |
| [cloudflow](repos/cloudflow.md) | the maintained Cloudflow fork ankka-flow descends from |
| [satisfactory](repos/satisfactory.md) | constraint solving as a service, built on ankka |

## What lives here and what does not

Each repo already documents itself well: `CLAUDE.md`, `README.md`, `docs/`, design documents,
`ROADMAP.md`, `docs/decisions.md`. Those stay authoritative for anything inside one repo. This
brain never copies them; it points at them.

This brain holds only what has no single-repo home:

- the family map: thesis, who consumes what from whom, who pins which version of whom
- decisions that touch two or more repos
- conventions every repo follows, written once
- the vocabulary that crosses repo boundaries
- where things run and publish, as pointers
- status: what is in flight across the family and what is waiting on what

## Layout

```
ankka-brain/
├── family.md            # the map: thesis, repo roles, dependency and pinning chain
├── principles.md        # beliefs that show up in real decisions across the family
├── conventions.md       # how every repo is built, tested, released; agent rules
├── glossary.md          # cross-repo vocabulary
├── infrastructure.md    # where things run and publish; pointers, never secrets
├── audiences.md         # who the family is for
├── voice.md             # how ankka talks in docs, READMEs, errors, CLI output
├── roadmap.md           # what is in flight and what waits on what, date-stamped
├── reuse-map.md         # what is shared, what should be, what deliberately is not
├── repos/               # one file per repo, plus a status index
└── decisions/           # ADR-style log of cross-repo calls; links to in-repo logs
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

## Wiring into Claude Code

`CLAUDE.md` in this repo imports the always-on files. Each family repo's own `CLAUDE.md` should
add a line pointing here, for example:

```
The family-level context (repo map, cross-repo decisions, shared conventions) lives in the
sibling checkout ../ankka-brain. Read ../ankka-brain/family.md and ../ankka-brain/repos/<this-repo>.md
before cross-repo work.
```

Keep the checkouts as siblings under one directory so relative paths hold.

## Maintenance ritual

- **When a release is cut** in any repo: update the version table in `family.md` and the repo's
  row in `repos/README.md`.
- **When a feature spec is opened or closed:** update `roadmap.md`.
- **When a decision touches two repos:** log it in `decisions/`, then link it from each repo's
  in-repo log or design doc.
- **When the same thing is built twice:** add a row to `reuse-map.md`.
- **Monthly:** re-read `family.md` and `roadmap.md` for anything that has quietly gone stale.
