# `opto-sync` repository relationships

Generated from reviewed policy and the current **public** repository inventory.

- Public repositories declared: **5**
- Private repository names withheld: **0**
- Relationship edges: **9**

## Repository roles

| Repository | Role | Lifecycle |
|---|---|---|
| [`.github`](https://github.com/opto-sync/.github) | `organization_governance` | `active` |
| [`opto-sync-clients`](https://github.com/opto-sync/opto-sync-clients) | `sync_service` | `active` |
| [`syncer.c`](https://github.com/opto-sync/syncer.c) | `sync_service` | `active` |
| [`syncer.rs`](https://github.com/opto-sync/syncer.rs) | `sync_service` | `active` |
| [`opto-sync-e2e`](https://github.com/opto-sync/opto-sync-e2e) | `end_to_end_tests` | `active` |

## Declared edges

| From | Relationship | To | Status/basis |
|---|---|---|---|
| `opto-sync/.github` | `governs` | `opto-sync/opto-sync-clients` | `inferred` / `role-convention`: organization defaults, safety, and relationship declarations |
| `opto-sync/.github` | `governs` | `opto-sync/opto-sync-e2e` | `inferred` / `role-convention`: organization defaults, safety, and relationship declarations |
| `opto-sync/.github` | `governs` | `opto-sync/syncer.c` | `inferred` / `role-convention`: organization defaults, safety, and relationship declarations |
| `opto-sync/.github` | `governs` | `opto-sync/syncer.rs` | `inferred` / `role-convention`: organization defaults, safety, and relationship declarations |
| `opto-sync/opto-sync-e2e` | `tests` | `opto-sync/opto-sync-clients` | `inferred` / `role-convention`: black-box compatibility verification |
| `opto-sync/opto-sync-e2e` | `tests` | `opto-sync/syncer.c` | `inferred` / `role-convention`: black-box compatibility verification |
| `opto-sync/opto-sync-e2e` | `tests` | `opto-sync/syncer.rs` | `inferred` / `role-convention`: black-box compatibility verification |
| `organization://opto-sync` | `deployed_via` | `platform://ORESoftware/k8s-cluster` | `platform-default` / `platform-policy`: immutable artifacts are promoted by digest through GitOps |
| `organization://opto-sync` | `packaged_via` | `platform://zed-pkg` | `platform-default` / `platform-policy`: Zed resolves artifacts while submodules compose editable source |

## Composition, service, and observability contract

Git submodules compose editable source; Zed packages resolve packages/artifacts; dual-managed commits must match. Production deploys immutable image digests, not runtime source builds. Cross-service access uses APIs/SDKs/events rather than another service database. MCP uses the product API/SDK. Services emit OpenTelemetry traces, bounded metrics, and correlated structured logs.

## Privacy boundary

This public registry deliberately omits private repository names and edges; the count above makes the boundary explicit.
