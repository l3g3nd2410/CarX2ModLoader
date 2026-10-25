# Changelog

Per-version release notes for the loader are maintained live on the website, not in this file -
see [c2ml.l3g3nd.dev](https://c2ml.l3g3nd.dev/#changelog).

## Highlights

- **Custom loader.** C2ML no longer depends on BepInEx - it's a loader built from scratch, with its own runtime,
  mod manager and API (see [ARCHITECTURE.md](ARCHITECTURE.md)).
- **DirectX 12 overlay support**, in addition to DirectX 11.
- **Update resilience.** The loader is built to keep working across most routine game updates on its own.
- **Encrypted mod distribution** (`.c2m`) through [c2ml.l3g3nd.dev](https://c2ml.l3g3nd.dev), replacing ad hoc
  plain-DLL sharing (see [SECURITY.md](SECURITY.md)).
