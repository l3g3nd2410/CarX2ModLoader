# Changelog

Per-version release notes for the loader are maintained live on the website, not in this file -
see [c2ml.l3g3nd.dev](https://c2ml.l3g3nd.dev/#changelog).

## Highlights

- **Custom CoreCLR host.** The loader no longer depends on BepInEx - it hosts CoreCLR directly and loads its own
  bootstrapper, mod manager and API (see [ARCHITECTURE.md](ARCHITECTURE.md)).
- **DirectX 12 overlay support**, in addition to DirectX 11.
- **Pattern-based IL2CPP metadata reconstruction** instead of hardcoded offsets, so most game updates don't
  require a loader update.
- **Encrypted mod distribution** (`.c2m`) through [c2ml.l3g3nd.dev](https://c2ml.l3g3nd.dev), replacing ad hoc
  plain-DLL sharing (see [SECURITY.md](SECURITY.md)).
