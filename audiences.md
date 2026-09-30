# Audiences

Who the family is for. Product-level detail lives in each repo's docs; this is the shared
picture an agent needs when writing anything public.

## Developers building services on ankka
The primary audience for ankka, its SDKs, docs, CLI, template and Claude Code skills. They know
Kubernetes well enough to run kind, may or may not know Akka, and write Scala, Python, TypeScript
or Rust. They want to ship a component and have the platform own the rest. Everything public
speaks to them first.

## People who used Akka and hit the licence
A sharper subset of the above: they know the component model already and want the same shape
without BSL. `akka-divergences.md` is written for them. Do not assume this audience for anyone
else; explain the model from scratch in public docs.

## Operators of an installation
Whoever runs an ankka installation: today Trevor on arrakis, tomorrow a self-hosting team.
`docs/platform/` and ankka-deployments speak to them. They care about the lifecycle scripts,
PKI, backups (not yet solved), and what a release moves.

## Customers of the hosted product
A person who signs up on www.ankka.cloud, pays, and deploys with the CLI. ankka-cloud's pages,
pricing and emails speak to them. They see organizations, plans and capacity, never Kubernetes.

## Teams building on the sibling products
satisfactory's audience is anyone with a planning problem and a Timefold-shaped expectation;
ankka-flow's is anyone with Kafka topics and stage logic in a non-JVM language. Both are reached
through their own docs sites and skills.

## Existing cloudflow consumers
The eitheror platform on the frozen 0.1.0 Akka line. They must not be broken and are not being
courted onto Pekko.
