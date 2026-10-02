# Opto-Sync integration audit execution update — 2026-09-09

Tracking: DEN-3498. This is the execution companion to `docs/integration-audit-2026-09-09.md`.

## Canonical policy applied

The implementation pass was reconciled against `ORESoftware/my-ai/AGENTS.md` and `SHARED.md` before merge decisions. Shared history was preserved with merge commits. Cross-language contract changes use `ORESoftware/typespec-json-schema-validator` (TJSV) as a fail-closed gate over independently authored TypeSpec and JSON Schema Draft 2020-12 authorities. Generated schemas/code and receipts are evidence only.

Credential values supplied through chat were not copied into repositories, workflow files, comments, logs, or fallback configuration. No token was revoked or rotated.

## Merged evidence

### `ORESoftware/api-docs`

- PR #73, merge `be7038ad292a9a2106df4cc592acef7d7e3f59d4`: hardened the one-way `api-docs -> Opto-Sync` RPC seam.
  - direct calls bypass queue/readback;
  - queued deletes use tombstones;
  - queue/readback failures stay distinct and fail closed;
  - no local projection is never presented as authoritative success;
  - Rust and TypeScript share FNV-1a/64 record-ID fixtures, including Unicode;
  - TJSV RPC admission and cross-runtime gates remain exact-head requirements.
- PR #74, merge `41fb8561b1195c01a59cc92f2fb608fe796cef52`: hardened malformed input boundaries.
  - strict percent escape and UTF-8 decoding;
  - malformed/non-object request JSON rejected before durable queue admission;
  - arrays/null/scalars/malformed JSON cannot reach queue/readback/direct fallback;
  - shared boundary fixtures are included in TJSV/runtime workflow path filters.

Dependency direction remains `api-docs -> Opto-Sync`; Opto-Sync did not acquire an RPC dependency.

### `opto-sync/opto-sync-web-server.rs`

- PR #10, merge `9d6297850751173dcee0cfec7fbfbc58781a6f53`: introduced one renderer-neutral `WebSyncService` boundary for MASH/Maud+HTMX, Leptos, and Dioxus.
  - callers pass an already admitted mutation; form/domain validation remains outside the sync engine;
  - durable upsert/delete and optimistic local readback share one application service;
  - missing local projection fails closed;
  - telemetry event shape excludes body/payload, table, record id, auth data, headers, cookies, and form values;
  - the repository-local TypeSpec/JSON Schema canary is pinned to immutable TJSV commit `4740f1367a7906813dcd420a77d0c9ede26943fb`.

Exact merge head evidence: Container CI (locked clippy/tests/container image), source-policy lint, and TJSV peer-authority parity all passed.

### `opto-sync/opto-sync-interfaces`

- PR #17, merge `71d856aa90d8a29cddabddc6557e051ab0336d71`: promoted TJSV from canary-only evidence into production public-contract admission.
  - independently authored `validation/typespec/validation.tsp` and `validation/public-contracts.v1.json` are admitted directly;
  - immutable TJSV commit `4740f1367a7906813dcd420a77d0c9ede26943fb` emits deterministic report/SARIF/Contract IR evidence;
  - explicit valid/invalid instances cover `RequestMeta`, `PageQuery`, and `ProblemDetails`;
  - the existing 19 consumer-admission refusal regressions remain required;
  - legacy validation helpers now recognize Draft 2020-12 boolean-false and `{ "not": {} }` false-schema closure, including `unevaluatedProperties: false`;
  - validation parity consumes merged `api-docs` semantics rather than the stale pre-hardening pin;
  - stale generated TypeScript/Rust/Go/Gleam evidence was regenerated only after peer-authority agreement;
  - generator hardening emits reserved fields safely (`r#type` in Rust, `type_` in Gleam) and refreshes the digest-bound parity receipt.

Exact merge head evidence: all six checks passed — production TJSV Contract IR admission, TJSV refusal regressions, interface build/conformance, public validation contract, cross-language generated parity, and source-policy lint.

### Existing linked lifecycle evidence

- `ores-otel/ores.otel.log#29` is merged: executable Zed lifecycle convention gates with semantic conflict preservation.
- `opto-sync/opto-sync-clients#94` is merged: authenticated Rust/Flutter session lifecycle, durable drain/forceFlush ordering, and cross-language lifecycle vectors.
- The original DEN-3498 snapshot saying no Ores integration was found is historical rather than current state.

## Important findings from the test cycle

1. TJSV proved the public authorities behaviorally identical across 196 probes while still stopping promotion for structural closure spelling. The authored Draft 2020-12 representation was reconciled rather than weakening the gate.
2. Provenance CI caught a wording-only edit to the canonical ingest schema because the file is byte-tracked to `opto-sync-clients`. The interface copy was restored exactly; wording must be corrected at the canonical source first and propagated through provenance.
3. The newer `api-docs` parity generator caught stale checked-in language artifacts after generator semantics changed. Evidence was regenerated; stale-evidence checking was not disabled.
4. GitHub runner warnings show several legacy action SHAs still target the deprecated Node 20 action runtime even though runners force Node 24. Those action pins should be upgraded to reviewed immutable revisions.
5. `ores-forms` is still not visible through the current GitHub connection. Do not invent package names or APIs; keep the architecture boundary explicit until the actual repositories are inspectable.

## Remaining backlog

Linear currently rejects creation of additional granular issues because the workspace issue-count limit is exceeded. Until capacity is available, these are explicit DEN-3498 sub-tasks and must remain mirrored here.

- [ ] **P0 — executable MASH runtime:** replace the print-only `server::run` with a real Axum/Tokio listener, `/healthz`, `/readyz`, HTMX full/fragment behavior, graceful shutdown, request/body/time bounds, and runtime HTTP tests. Keep the merged `WebSyncService` as the only sync application boundary.
- [ ] **P0 — production sync-envelope peer authority:** independently author the TypeSpec peer for the complex queued mutation/envelope JSON Schema, including upsert/delete conditional semantics and timestamp unions; run TJSV structural+differential admission with positive/negative instances. Generated schema is evidence only.
- [ ] **P0 — concrete OTel web adapter:** implement the adapter from `WebSyncService::SyncTelemetry` into the established Ores telemetry contract; prove request/trace/sync-cycle propagation, retry/conflict/terminal classification, and payload/secret redaction.
- [ ] **P0 — canonical authority wording:** change “single source of truth” language at the canonical `opto-sync-clients` schema source, then update provenance and downstream byte-identical copies. Do not edit only the copy.
- [ ] **P1 — `ores-forms` admission adapter:** once the org is visible, inspect its actual public contract and prove invalid forms never construct an `AdmittedMutation`; valid admitted mutations remain queueable offline.
- [ ] **P1 — Leptos adapter:** thin server-function adapter over the shared `WebSyncService`; same fixtures and recovery semantics as MASH.
- [ ] **P1 — Dioxus adapter:** thin server-function adapter over the shared `WebSyncService`; same fixtures and recovery semantics as MASH.
- [ ] **P1 — recovery matrix:** crash/restart, duplicate delivery, stale base revision, conflict, tombstone, reconnect, queue drain, and local-to-authoritative convergence across supported stores.
- [ ] **P1 — TJSV fleet audit:** reject unpinned/stale TJSV references and require production contract paths, not canary-only evidence, wherever a serialized cross-language boundary exists.
- [ ] **P1 — GitHub Action runtime refresh:** replace legacy action commits that target deprecated Node 20 with reviewed immutable newer commits; retain least-privilege permissions and exact-head checks.
- [ ] **P1 — codegen reserved identifiers:** retain fixtures for Rust/Gleam/other language keywords so regenerated evidence is always compilable.
- [ ] **P1 — route-envelope naming:** make the `api-docs` route-map publication envelope unmistakably distinct from the Opto-Sync mutation envelope in naming/docs without merging their authorities.
- [ ] **P2 — browser lifecycle:** IndexedDB/service-worker multi-tab ownership, freeze/pagehide/resume, offline flapping, lease handoff, and background sync canaries.
- [ ] **P2 — OTel SLOs:** queue age/depth, reconciliation latency, retry count, conflict rate, terminal failure rate, drain duration, and local-vs-authoritative convergence measures.

## Promotion rule

Do not call pending work green. Merge only the exact head that has repository-appropriate evidence. If a semantic conflict appears, reconcile ownership and unique contributions according to `ORESoftware/my-ai/AGENTS.md`; do not take one side wholesale, rebase away shared history, or suppress a failing contract/evidence gate.