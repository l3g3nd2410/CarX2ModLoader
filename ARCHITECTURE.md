# Architecture

How C2ML gets from "the game starts" to "a mod is running" - a conceptual overview, not a code walkthrough: C2ML
is closed source (see [Repository scope](#repository-scope) below).

## Boot sequence

1. **Load.** Windows loads `winhttp.dll` from the game's folder instead of the system one (standard DLL-proxying:
   nothing in the game is modified). `DllMain` immediately resolves and forwards every real `winhttp.dll` export
   to the genuine system library, so Steam/game networking keeps working exactly as if C2ML weren't there, then
   starts loader initialization on its own thread.
2. **Host the runtime.** The loader locates `C2ML\core\coreclr.dll`, loads it, and drives it through the raw
   [CoreCLR hosting API](https://github.com/dotnet/runtime) (`coreclr_initialize`, `coreclr_create_delegate`) -
   no `hostfxr`/`dotnet.exe` involved. It resolves a fixed set of named entry points
   (`Initialize`, `GameReady`, `DrawUI`, `Poll`, `LoadMod`, `Shutdown`, ...) on the managed bootstrapper and calls
   into them from native code for the rest of the game's lifetime.
3. **Rebuild IL2CPP metadata.** CarX Drift Racing Online 2 ships as IL2CPP (native code, not a .NET assembly).
   On first run after install or after a game update, the loader reconstructs the metadata it needs from the
   game's own IL2CPP data using **pattern/shape matching** rather than hardcoded offsets, then generates interop
   assemblies (`C2ML\cache\unity-libs\`, `C2ML\interop\`). This is what lets mods (and HarmonyX patches) call
   into and patch the game like a normal managed assembly, and is also why most routine game updates don't
   require a loader update.
4. **Render the overlay.** A [MinHook](https://github.com/TsudaKageyu/minhook) hook on the game's DirectX 11/12
   `Present` call renders the [Dear ImGui](https://github.com/ocornut/imgui) menu (mod list, keybinds, per-mod
   config, about page, Mod DB browser) every frame. The managed side drives the UI through a thin native bridge;
   the native side only owns the render hook and the ImGui draw calls.
5. **Load mods.** From here on the managed runtime (closed-source `C2ML.Runtime`) takes over: it authenticates
   the player against [c2ml.l3g3nd.dev](https://c2ml.l3g3nd.dev) using their Steam session, fetches the catalog
   of mods they're authorized to run, downloads and decrypts the corresponding `.c2m` packages, and loads each
   one through `C2ML.API`.

## Security self-checks

Besides the mod-encryption model described in [SECURITY.md](SECURITY.md), the native loader runs structural
integrity checks at startup - for example detecting a second `coreclr.dll` already loaded in the process, or the
presence of known loader modules (Doorstop, MelonLoader, BepInEx) that would indicate something other than C2ML
is trying to attach to the game.

## Repository scope

C2ML is closed source end to end - no component's source is published, including the native loader described
above. This repository holds documentation only.

| Layer | Component | Public |
|---|---|---|
| `winhttp.dll` proxy, CoreCLR host, IL2CPP metadata reconstruction, ImGui render hook, integrity checks | CarX2ModLoader | ❌ closed (compiled loader downloaded from the website) |
| Native↔managed entry points | `C2ML.Bootstrapper` | ❌ closed |
| Mod manager, UI logic, keybinds, config, Mod DB client, server auth | `C2ML.Runtime` | ❌ closed |
| Public modding API | `C2ML.API` | ❌ closed (compiled DLL shipped in the [SDK](https://github.com/l3g3nd2410/C2ML-SDK)) |
| Mod distribution, auth, encryption, Mod DB, dev panel, admin panel | [c2ml.l3g3nd.dev](https://c2ml.l3g3nd.dev) (Cloudflare Workers + D1 + R2) | ❌ closed |
