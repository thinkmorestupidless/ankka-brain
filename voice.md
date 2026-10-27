# Voice

How ankka talks in READMEs, docs, CLI output, error messages and commit messages. Inferred from
what is written today; correct it where it is wrong.

## In one line
Plain, declarative, specific. Closer to a good commit message than to marketing copy.

## What it sounds like
- Sentences state the resulting fact. "You write components; ankka supplies the runtime."
- Lists of concrete nouns, not adjectives. "Sharding, persistence, replay, projections, durable
  orchestration, timers, HTTP and the agent loop."
- Reasons given inline, briefly. "Apache 2.0 throughout, so the programming model carries no
  licence constraint on who runs it."
- Honest about limits. A limitations page in every repo. "Nothing is built yet" at the top of a
  README when that is true.
- Traps and lessons written in the second person or as flat statements of what happened.

## Mechanics
- Sentence case for headings and titles. Lower-case product names: ankka, ankka-flow, satisfactory.
- "ankka" is never capitalised, even at the start of a sentence.
- Backticks for every identifier, path, command, flag and version.
- Exact versions and dates, not "latest" or "recently".
- No exclamation marks. No emoji. No "simply", "just", "easily".
- Docs pages tell no history: no feature numbers, no "we decided", no "previously".
- British spelling in prose (licence, serialised, behaviour); American where an API or upstream
  name uses it (`license` in metadata, `serialization` config keys).

## Words used
ship, pin, seam, overlay, installation, component, effect, wire name, trap, the honest list.

## Words avoided
leverage, robust, seamless, powerful, solution (as a noun for a product), journey, unlock,
"best practice", "enterprise-grade".

## Tone shifts
- Public docs: teach from scratch; assume Kubernetes and no other platform.
- CLAUDE.md files: terse, imperative, rules and traps; "do not optimise this back".
- Design treatments and decision logs: reasons and rejected alternatives, first person allowed.
- Hosted product customer pages: same register, fewer identifiers, no Kubernetes vocabulary.
- Errors and refusals: say what was refused and what to do instead, in one line.
