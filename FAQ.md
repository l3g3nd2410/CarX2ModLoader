# FAQ

**Is C2ML affiliated with CarX Technologies?**
No. C2ML is an independent, unofficial mod loader for CarX Drift Racing Online 2. "CarX Drift Racing Online 2"
is a trademark of CarX Technologies LLC.

**Is it safe to use?**
C2ML only loads mods distributed as encrypted `.c2m` packages published through
[c2ml.l3g3nd.dev](https://c2ml.l3g3nd.dev) - it refuses plain, unencrypted DLLs by default (see
[SECURITY.md](SECURITY.md)). Mods still run with the same privileges as the game process, like any mod loader
for any game; only install mods from developers and sources you trust.

**Why does it replace `winhttp.dll`?**
It's the standard DLL-proxying technique used to load into a game at startup without an external injector or
launcher: the loader forwards every real `winhttp.dll` call straight through to the system library, so nothing
about the game's own networking changes.

**Why can't I just drop a `.dll` in the mods folder?**
By design - see [SECURITY.md](SECURITY.md). Developers testing their own mod use a **test version** from their
dev space instead, which stays encrypted but isn't distributed to other players.

**The game updated and something broke - what do I do?**
C2ML is built to keep working across most routine game updates without needing a loader update. A larger engine
change can still require one - check [c2ml.l3g3nd.dev](https://c2ml.l3g3nd.dev#changelog) for the latest
release.

**Where do I get mods?**
The [Mod DB](https://c2ml.l3g3nd.dev/#mods) on the site, or a direct link from a developer for an unlisted or
private mod.

**I want to make a mod - where do I start?**
[C2ML-SDK](https://github.com/l3g3nd2410/C2ML-SDK) has everything you need (the API, HarmonyX, guides, an
example mod), and [c2ml.l3g3nd.dev/docs](https://c2ml.l3g3nd.dev/docs) has the same guides online.

**I found a bug or a security issue - how do I report it?**
A regular bug: open an [issue](https://github.com/l3g3nd2410/CarX2ModLoader/issues) on this repository. A
security issue: see [SECURITY.md](SECURITY.md) for a private contact instead.
