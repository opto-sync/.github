# opto-sync organization handbook

> Shared operating defaults for repositories maintained under **opto-sync**. Repository-local policy may strengthen these rules but should not silently weaken them.

## Mission

opto-sync maintains offline-first synchronization, replication, and integration tooling across clients and services. This `.github` repository is the canonical home for shared policy, reusable templates, community health files, and planning links.

## Repository contract

Each active repository must document purpose, ownership, maturity, supported stores and platforms, development and test commands, authoritative data and protocol formats, release and rollback procedures, compatibility policy, and GitHub Project/Linear links. Sync components should also document identity, ordering, clocks, conflict resolution, tombstones, retries, idempotency, batching, backpressure, offline limits, convergence guarantees, retention, and recovery.

## Change workflow

1. Anchor work in an issue, Linear item, or documented maintenance objective.
2. Keep branches and pull requests focused.
3. Explain motivation, scope, durability and compatibility risk, validation, migration, and rollback.
4. Test offline, reconnect, concurrent edits, reorder, duplicate, deletion, partial failure, replay, and multi-version paths as relevant.
5. Resolve conflicts semantically by reconstructing both sides' intent.
6. Prefer squash merges for focused work unless commit structure materially improves auditability.

## Evidence, security, and documentation

Pull requests should include reproducible commands, deterministic fixtures, expected and observed state, convergence evidence, negative-path coverage, documentation updates, and CI or local-equivalent evidence. Never commit credentials, production datasets, encryption keys, or sensitive logs. Follow `SECURITY.md` for private reporting. Keep protocol examples executable, compatibility matrices current, invariants explicit, and important durability, conflict, privacy, and operational decisions recorded.

## Planning ownership

GitHub owns code, reviews, checks, releases, and delivery evidence. Linear owns priority, dependencies, sequencing, and cross-project planning. The organization GitHub Project is the cross-repository execution view; see `PROJECTS.md` for routing details.

## Organization health

- [ ] Profiles, descriptions, topics, and READMEs are current.
- [ ] Community health files and reusable issue/PR guidance are present.
- [ ] Ordering, conflicts, deletion, convergence, retries, retention, and recovery are documented.
- [ ] Required checks cover offline, concurrent, partial-failure, multi-version, and supply-chain risk.
- [ ] Stale repositories are archived or clearly marked.
- [ ] GitHub Project and Linear links resolve and reflect completed work.
