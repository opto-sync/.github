# Opto Sync marketing site

Complete Astro source staged for the future public repository `opto-sync/opto-sync.github.io` and URL `https://opto-sync.github.io/`.

## Canonical planning

- Linear project: [github.com/opto-sync](https://linear.app/denman/project/githubcomopto-sync-de6ba65bd559)
- GitHub Project: [opto-sync-project #1](https://github.com/orgs/opto-sync/projects/1)
- Official clients: [opto-sync-clients](https://github.com/opto-sync/opto-sync-clients)

## Client truth

The page uses the public, tested client surfaces:

- TypeScript `@opto-sync/client` with Dexie/IndexedDB and native/WASM merge bindings
- Dart `opto_sync_client` with Drift/SQLite or browser persistence
- Rust `opto-sync-client` with the first-party SQLite protocol store
- Gleam `opto_sync_client` with a typed BEAM queue and Rustler/NIF reconciliation

All clients pin and call the same `syncer.c` core. The key UI rule is preserved verbatim in the product narrative: render `localView`, not `reconcileIncoming`. HTTP push/pull remains commit-ordered truth; WebSocket, Supabase, BroadcastChannel, and TCP are wake hints.

## Publish

1. Create public repository `opto-sync.github.io` in the `opto-sync` organization.
2. Copy this directory to the new repository root.
3. Run `npm install && npm run build`.
4. Add the standard Astro GitHub Pages workflow and enable GitHub Actions as the Pages source.
5. Verify `https://opto-sync.github.io/` and update the linked GitHub and Linear tickets.
