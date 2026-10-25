# Architecture

C2ML is closed source - this page is a conceptual, high-level overview for players and mod developers, not an
implementation reference. Internal mechanisms (hooking, runtime hosting details, update/anti-tamper checks) are
intentionally not documented publicly.

## In short

- The loader attaches to the game at startup without modifying any game file, and hosts a .NET runtime that runs
  the mod manager, the in-game menu and the mods themselves.
- CarX Drift Racing Online 2 is an IL2CPP (native-code) Unity game; the loader generates the interop layer that
  lets mods (and HarmonyX patches) call into and patch the game like a normal managed assembly.
- Mods are authenticated and loaded through [c2ml.l3g3nd.dev](https://c2ml.l3g3nd.dev) - see
  [SECURITY.md](SECURITY.md) for the encryption and distribution model, which is the part of the design that's
  actually meant to be public.
- The loader is built to tolerate most routine game updates without needing an update of its own.

## Repository scope

C2ML is closed source end to end - no component's source is published. This repository holds documentation
only.

| Component | Role | Public |
|---|---|---|
| CarX2ModLoader | Native loader attached to the game: mod manager, in-game UI, server auth, and everything else that makes mods run | ❌ closed (compiled loader downloaded from the website) |
| C2ML.Bootstrapper | Native↔managed entry point | ❌ closed |
| C2ML.API | Public modding API | ❌ closed (compiled DLL shipped in the [SDK](https://github.com/l3g3nd2410/C2ML-SDK)) |
| Mod distribution, auth, encryption, Mod DB, dev panel, admin panel | [c2ml.l3g3nd.dev](https://c2ml.l3g3nd.dev) | ❌ closed |
