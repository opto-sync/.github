# Opto-Sync integration audit — 2026-09-09

Tracking: DEN-3498. Related work: DEN-817, DEN-818, DEN-825, DEN-3498, DEN-3593, DEN-3828, DEN-3963.

## Goal

Keep Opto-Sync the transport- and UI-framework-neutral offline-first synchronization runtime while making it easy for Rust `*-web-server.rs` applications to expose MASH (Maud + Axum + HTMX), Leptos, and Dioxus surfaces; compose with `ores-forms`; emit privacy-bounded `ores-otel` telemetry; and carry custom RPC declared by `ORESoftware/api-docs` without reversing dependency ownership.

## Non-negotiable dependency directions

1. `opto-sync-interfaces` owns Opto-Sync wire/data contracts. It must not depend on a sync engine or application UI.
2. `opto-sync-*core` owns synchronization behavior, stores, queues, retries, reconciliation, local projections, and framework-neutral application services.
3. MASH / Leptos / Dioxus adapters are thin application boundaries. They call the same sync application service; they do not implement their own queue, merge, conflict, or retry engine.
4. `ores-forms` owns form controls, field/form validation, validation presentation, and UI state. Opto-Sync receives an admitted domain mutation and owns durability/reconciliation. Form validation is not a sync concern.
5. `ores-otel` owns telemetry semantics/export. Opto-Sync emits the established privacy-bounded sync lifecycle contract and accepts/propagates trace/correlation context; it does not create a competing telemetry vocabulary.
6. `ORESoftware/api-docs` owns RPC/route-map contracts. RPC may adapt its queued mutation transport to Opto-Sync. Opto-Sync must not depend on `api-docs`. Reads remain direct when the route map says direct; queueable mutations use the durable Opto-Sync mutation boundary.
7. TypeSpec and human-authored JSON Schema Draft 2020-12 remain independent peer authorities. `ORESoftware/typespec-json-schema-validator` (TJSV) is the fail-closed admission gate. TypeSpec-emitted JSON Schema is comparison evidence only, never a third authority.

## Findings

### `opto-sync-interfaces`

- TJSV is present and pinned, but the main TJSV workflow primarily proves a repository-local canary. The workflow itself explicitly calls out that production sync contracts and runtime conformance are out of scope.
- `validation/typespec/validation.tsp` and `validation/public-contracts.v1.json` are independent public contract peers and should receive direct TJSV admission, not only custom parity scripts.
- `schemas/opto-sync-envelope.schema.json` describes itself as the “single cross-language source of truth”. That conflicts with the fleet invariant that human-authored TypeSpec and JSON Schema are independent peer authorities.
- `validation/parity/manifest.v2.json` correctly points route authority at `ORESoftware/api-docs`; retain that boundary.

### `opto-sync-web-server.rs`

- The repository is labelled a MASH web server, but `server::run` only prints the bind address and home markup. There is currently no live Axum listener/router.
- `AppState` contains only `WebConfig`; it does not expose a framework-neutral sync service boundary.
- Transport selection exists, but current HTTP/NATS/TCP transport structs are skeletal and must not become parallel implementations of the core queue/reconciliation engine.
- Container CI is useful and already runs locked clippy/tests plus image checks; runtime HTTP tests should be added before calling the MASH surface production-ready.
- Its TJSV workflow is pinned to an older validator revision than the interface repository. Fleet TJSV pins should converge on a reviewed immutable revision.

### `ORESoftware/api-docs`

- The RPC repository already contains the correct one-way seam: `runtime/rust/opto_sync.rs` defines `MutationQueue` and `LocalReadback` traits and explicitly states that `api-docs` may call Opto-Sync while Opto-Sync must not call `api-docs`.
- The RPC transport queues only route-map-declared mutations; reads remain direct. This is the correct semantic boundary for offline-first RPC.
- `api-docs/json-schema/opto-sync-envelope.schema.json` is a route-map publication envelope, not the same document as Opto-Sync’s mutation envelope. Do not merge those schemas just because their filenames overlap; rename/document them where practical to prevent accidental authority conflation.
- Add cross-repository conformance that exercises `api-docs`’s generic `MutationQueue`/`LocalReadback` seam against the public Opto-Sync client/runtime behavior.

### `ores-otel`

- `opto-sync-clients` already has cross-language ORE telemetry bridge work (Rust, Dart, TypeScript). Reuse it.
- Web-server adapters should propagate request/trace/sync-cycle identifiers into that established contract and keep payload/body contents out of default telemetry.
- Exporter setup, sampling, and process lifecycle belong at the application/server boundary using `ores-otel`; core sync logic should depend on a small telemetry sink/bridge interface.

### `ores-forms`

- The current GitHub connector cannot see the `ores-forms` organization, so this audit does not invent concrete package paths or APIs that could not be inspected.
- The integration contract is still clear: the form layer produces a validated/admitted mutation; Opto-Sync persists and reconciles it. `api-docs/form-validation` already demonstrates the same independent TypeSpec/JSON-Schema admission model and is a useful conformance reference until `ores-forms` is visible to this connection.

## Required architecture for Rust web surfaces

All three Rust web UI choices should share one application service boundary:

```text
MASH (Maud/Axum/HTMX) ─┐
Leptos server funcs     ├─> WebSyncService ─> Opto-Sync queue/store/reconcile
Dioxus server funcs     ┘          │
                                  ├─> ORE telemetry sink
                                  └─> admitted domain mutation

api-docs RPC transport ─> MutationQueue + LocalReadback ─> same Opto-Sync core
```

The framework adapter may choose response rendering and request extraction. It must not choose merge algorithms, replay order, idempotency rules, conflict semantics, retry policy, or local-view projection semantics.

## Test matrix

| Boundary | Required evidence |
| --- | --- |
| TypeSpec ↔ JSON Schema | TJSV structural + differential admission, immutable SHA, Contract IR receipt, positive and negative instance corpus |
| Rust ↔ TypeScript ↔ Dart | same mutation/envelope fixtures and runtime evidence tied to the admitted contract receipt |
| MASH | real Axum router, HTMX full/fragment behavior, health/readiness, mutation admission, offline queue response |
| Leptos | server-function adapter calls the same `WebSyncService`; no separate sync state machine |
| Dioxus | server-function adapter calls the same `WebSyncService`; no separate sync state machine |
| Forms | invalid form never reaches mutation enqueue; valid admitted mutation is queueable offline; validation errors remain form-domain errors |
| RPC | direct reads bypass queue; declared queued upsert/delete use Opto-Sync; queued write returns local projection; no false authoritative response |
| OTel | correlation/trace propagation; retry/conflict/terminal events; redaction test proves bodies/secrets are absent |
| Stores | SQLite/Postgres/Supabase/Neon/IndexedDB deterministic replay and idempotency fixtures |
| Recovery | restart/offline/online transition, duplicate delivery, conflict, tombstone/delete, stale base revision |

## Backlog discovered by this audit

These are concrete follow-up tasks. Linear issue creation was attempted on 2026-09-09 but the workspace rejected new issues because its issue-count limit was exceeded. Until capacity is available, keep these items under DEN-3498 and this file; do not lose them or create shadow trackers elsewhere.

- [ ] **P0 — production TJSV admission:** run the current immutable TJSV action against `validation/typespec/validation.tsp` and `validation/public-contracts.v1.json`, emit a Contract IR, retain deterministic receipts, and include explicit positive/negative instances.
- [ ] **P0 — sync mutation peer authorities:** author an independent TypeSpec peer for the production queued mutation/envelope boundary currently represented in JSON Schema, then admit both directions with TJSV. Generated schema remains evidence only.
- [ ] **P0 — fix authority wording:** remove “single source of truth” claims from authored JSON Schema and document peer authority ownership.
- [ ] **P0 — executable MASH server:** replace the print-only web-server stub with a real Axum/Tokio listener, `/healthz`, `/readyz`, full-page/HTMX-fragment rendering, graceful shutdown, and request-size/time bounds.
- [ ] **P0 — framework-neutral `WebSyncService`:** introduce one queue/readback/status boundary consumed by MASH, Leptos, and Dioxus adapters; prohibit framework-specific sync engines.
- [ ] **P0 — RPC conformance:** in `api-docs`, test its `MutationQueue` and `LocalReadback` adapters against Opto-Sync fixtures for direct reads, queued upserts, queued deletes, local optimistic readback, and confirmed fallback.
- [ ] **P0 — telemetry propagation:** thread request/correlation/trace/sync-cycle identifiers through web/RPC adapters into the existing Opto-Sync ORE telemetry bridge; add redaction assertions.
- [ ] **P1 — forms integration contract:** once `ores-forms` is visible to the GitHub connection, inspect its actual public contracts and add an adapter/conformance fixture proving that form admission precedes queueing and that Opto-Sync never owns field validation.
- [ ] **P1 — route-envelope naming:** distinguish `api-docs` route-map publication envelope from Opto-Sync mutation envelope in code/docs so same-basename schemas cannot be mistaken for the same authority.
- [ ] **P1 — Leptos adapter:** add a thin server-function adapter over `WebSyncService`; test parity with MASH behavior using the same fixture corpus.
- [ ] **P1 — Dioxus adapter:** add a thin server-function adapter over `WebSyncService`; test parity with MASH behavior using the same fixture corpus.
- [ ] **P1 — recovery matrix:** add crash/restart, duplicate, conflict, stale revision, tombstone, reconnect, and queue-drain tests across supported stores/transports.
- [ ] **P1 — TJSV fleet pin:** standardize Opto-Sync repositories on a reviewed immutable TJSV SHA and add an automated audit that rejects unpinned or stale workflow references.
- [ ] **P2 — browser lifecycle:** verify IndexedDB + service-worker/background behavior with multi-tab ownership, pagehide/freeze/resume, offline flapping, and lease handoff.
- [ ] **P2 — observability SLOs:** define queue age/depth, reconcile latency, retry count, conflict rate, terminal failures, and local-vs-authoritative convergence measures using `ores-otel` attributes.

## Promotion rule

A change is merge-ready only after the repository’s required checks are green and the changed contract/runtime surface has direct evidence. Documentation-only claims do not count as implementation. If a semantic conflict occurs, reconcile ownership and invariants first; never resolve by taking one side wholesale, rebasing away history, or silently dropping unique work.
