# CarX2ModLoader (C2ML)

C2ML is a mod loader for **CarX Drift Racing Online 2** (Steam App ID `1826420`), built from scratch to host
mods for a native IL2CPP Unity game without the game ever shipping a managed assembly to patch.

➡️ **Website:** [c2ml.l3g3nd.dev](https://c2ml.l3g3nd.dev) - download the loader, browse the Mod DB, read the
changelog.
📖 **Docs for mod developers:** [c2ml.l3g3nd.dev/docs](https://c2ml.l3g3nd.dev/docs)
🧰 **SDK to build mods:** [C2ML-SDK](https://github.com/l3g3nd2410/C2ML-SDK) (compiled API, Harmony, guides, an
example mod)

This repository contains the **native loader** - the `winhttp.dll` proxy that CarX Drift Racing Online 2 loads
on startup. It hosts the .NET runtime and the mod manager described below.

## What C2ML does

- Loads as a `winhttp.dll` proxy next to the game executable (no external injector, no launcher to run) and
  forwards every real `winhttp.dll` export to the system library, so the game behaves exactly as normal.
- Hosts the **CoreCLR** runtime directly (`coreclr.dll`, loaded and driven through the low-level
  [CoreCLR hosting API](https://github.com/dotnet/runtime) - no `hostfxr`/`dotnet.exe` involved) and runs the
  managed side of the loader (mod manager, UI, security checks) inside it.
- Generates its own **IL2CPP interop assemblies** on first launch (`C2ML\cache\unity-libs\`, `C2ML\interop\`):
  C# types shaped exactly like the game's native IL2CPP classes, so mods (and [HarmonyX](#built-with) patches)
  can call into and patch the game like a normal managed assembly.
- Draws an in-game menu (mod list, keybinds, per-mod config, about page) with [Dear ImGui](#built-with), hooked
  into the game's DirectX 11/12 swap chain.
- Reconstructs the metadata it needs from the game's own IL2CPP data at runtime using pattern/shape matching
  instead of hardcoded offsets, so it keeps working across most routine game updates without a loader update.

## Security model

C2ML's mod format exists to stop players from being tricked into running a malicious DLL disguised as a mod,
while still letting developers test locally:

- Mods are distributed as **encrypted `.c2m` packages**, published and encrypted through
  [c2ml.l3g3nd.dev](https://c2ml.l3g3nd.dev). The server never sees a developer's DLL in clear form except
  through an explicit, logged admin-inspection path for moderation.
- By default the loader **refuses to load a plain, unencrypted `.dll`** dropped in the mods folder - only
  `.c2m` packages that came from the official server are accepted. A developer testing their own work-in-progress
  mod uploads a **test version** from their [dev space](https://c2ml.l3g3nd.dev/dev) instead of bypassing
  encryption.
- A narrow, admin-curated **whitelist** allows specific, well-known BepInEx-style plugins (e.g. ReShade) to load
  as plain DLLs - everything outside that whitelist goes through the encrypted `.c2m` path.

## Project layout

C2ML is split into several components; only the native loader (this repo) and the [SDK](#for-mod-developers)
are public. The managed runtime, the public API and the bootstrapper that wires them together are closed-source
and developed privately.

| Component | Role |
|---|---|
| **CarX2ModLoader** (this repo) | Native `winhttp.dll` proxy: CoreCLR hosting, IL2CPP interop generation, ImGui overlay, update/security checks |
| C2ML.Bootstrapper | Thin native↔managed entry point invoked by the loader once CoreCLR is up |
| C2ML.Runtime | Mod manager, in-game UI, keybinds, config, Mod DB client, security gating |
| C2ML.API | The public API mods are written against - shipped as a compiled DLL in the [SDK](https://github.com/l3g3nd2410/C2ML-SDK), source kept private |

## Installing (players)

1. Download the loader from [c2ml.l3g3nd.dev](https://c2ml.l3g3nd.dev).
2. Drop `winhttp.dll` next to `CarX Drift Racing Online 2.exe`.
3. Launch the game - the in-game menu opens with a configurable keybind. Install mods from the
   [Mod DB](https://c2ml.l3g3nd.dev/#mods) or a direct link from a developer.

## For mod developers

Mods are written in C# against `C2ML.API` and published through the [dev space](https://c2ml.l3g3nd.dev/dev).
Start with the [C2ML-SDK](https://github.com/l3g3nd2410/C2ML-SDK) repository - it has the compiled API, HarmonyX,
a full bilingual guide set and a reference mod that uses the whole API. See
[c2ml.l3g3nd.dev/docs](https://c2ml.l3g3nd.dev/docs) for the same guides rendered online.

## Built with

- [.NET / CoreCLR](https://github.com/dotnet/runtime) - the managed runtime hosted by the loader
- [Dear ImGui](https://github.com/ocornut/imgui) - the in-game menu
- [MinHook](https://github.com/TsudaKageyu/minhook) - function hooking (render hook for the ImGui overlay)
- [HarmonyX](https://github.com/BepInEx/HarmonyX) - bundled in the SDK for mods that patch game code (not used
  by the loader itself)

## License

All rights reserved. This source is published for transparency; no license is granted to copy, modify,
redistribute or reuse it without the author's explicit permission.
