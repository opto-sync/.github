<!-- ore-org-baseline:begin -->
# Repository relationships for `opto-sync`

This file is rendered from `repository-relationships.json`. The JSON registry is authoritative.

- Audience: `public`
- Repositories represented: **5**
- Relationships represented: **4**
- Inventory digest: `sha256:6309bb66e50638b4ce93f8839245f3a0bb7c71cddf259ec07f884be5b9ebf9f7`

## Immutable routing identity

| Field | Value |
|---|---|
| Mapping ID | `context:opto-sync` |
| GitHub owner ID | `308481609` |
| Linear project ID | `88c60373-3e46-4a12-8f10-907d239d48f8` |
| Linear team ID | `eb8ab169-5afe-4b6f-9cab-3f2aa3e887dc` |

## Repositories

| Repository | Visibility | Roles | Archived |
|---|---|---|---|
| `opto-sync/.github` | `public` | `community-health`, `governance`, `relationship-registry` | no |
| `opto-sync/opto-sync-clients` | `public` | `clients` | no |
| `opto-sync/opto-sync-e2e` | `public` | `end-to-end-tests` | no |
| `opto-sync/syncer.c` | `public` | `repository` | no |
| `opto-sync/syncer.rs` | `public` | `repository` | no |

## Relationships

| From | Type | To | Status | Required |
|---|---|---|---|---|
| `opto-sync/.github` | `governs` | `opto-sync/opto-sync-clients` | `declared` | yes |
| `opto-sync/.github` | `governs` | `opto-sync/opto-sync-e2e` | `declared` | yes |
| `opto-sync/.github` | `governs` | `opto-sync/syncer.c` | `declared` | yes |
| `opto-sync/.github` | `governs` | `opto-sync/syncer.rs` | `declared` | yes |

## Editing relationships

Put reviewed public declarations in `repository-relationships.manual.json`; do not edit the generated registry directly.
Private repository names and private-only relationships belong in the private `ORESoftware/project-registry` mirror.
Inferred edges are advisory and must remain visibly labeled until reviewed.
<!-- ore-org-baseline:end -->
