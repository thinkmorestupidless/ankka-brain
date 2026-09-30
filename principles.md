# Principles

Beliefs that show up in real decisions across more than one repo. Each has a place it bites.

1. **No BSL on the path.** Pekko, not Akka. The reason ankka exists and the reason cloudflow was
   ported. A dependency that reintroduces a BSL licence is a design change, not a version bump.
2. **Adopt, then diverge with reasons.** ankka reimplements Akka's component model and lists every
   departure in `akka-divergences.md`. satisfactory reimplements the Timefold Platform API and
   lists every divergence in `API.md`. The method is the same: a familiar surface, an honest list.
3. **Effects are inert data; the runtime interprets them.** Handlers describe; nothing does I/O
   when built. The same idea shapes the operators: pure `render`, one executor that touches the
   world.
4. **Explicit registration, never classpath scanning.** ankka components, ankka-flow streamlets,
   satisfactory models. Scanning was one of the things ankka-flow left behind in Cloudflow.
5. **Split by lifecycle and trust.** Infrastructure, the hosted product, and the platform live in
   separate repos because they change at different speeds, are read by different people, and hold
   different credentials. Nothing installation-specific goes in a public repo.
6. **Be your own customer.** ankka-cloud, satisfactory and the control plane are ankka
   applications. What they cannot do becomes an ankka feature request (`notes/ankka-requests.md`
   in satisfactory is the model).
7. **Only a tag publishes.** Versions come from git tags; no version file, no publishing by hand
   after the first time, no re-tagging. Downstream pins releases, not branches.
8. **The honest not-implemented list.** Every repo keeps a limitations page and does not promise
   past it. Docs pages stand alone and tell no history.
9. **Traps are written down the day they cost time.** ankka's CLAUDE.md carries about 120 dated
   ones; ankka-deployments has its traps sections. Read the section for the area before touching it.
10. **Same conventions across repos so people move between them.** Justfile, spec-kit, scalafmt
    hook, serialised tests, docs tool, marketplace plugin, `FLOW_*` mirroring `ANKKA_*`.
