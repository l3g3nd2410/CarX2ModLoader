# Security

## Mod distribution model

C2ML's mod format exists to stop a player from being tricked into running a malicious DLL disguised as a mod,
while still letting developers test locally and iterate quickly.

- Mods are compiled by their developer, then **encrypted in the developer's browser** into a `.c2m` package when
  published through [c2ml.l3g3nd.dev/dev](https://c2ml.l3g3nd.dev/dev). The server only ever receives and stores
  the already-encrypted package - never the plain DLL - except through the explicit, logged admin-inspection
  path below.
- The loader only hands a decryption key to the mod manager for packages that came from the official server and
  that the current player is authorized to install (public, or private with that player on the access list). A
  plain, unencrypted `.dll` dropped in the mods folder is **refused by default**.
- A developer testing a work-in-progress build uploads it as a **test version** from their dev space instead of
  bypassing encryption - it behaves exactly like a real version (encrypted, key-gated) but isn't distributed to
  other players or the Mod DB until promoted.
- A narrow, admin-curated **whitelist** allows a small number of well-known BepInEx-style plugins (e.g. ReShade)
  to load as plain DLLs for compatibility. Everything outside that whitelist goes through the encrypted `.c2m`
  path - there is no general escape hatch for unencrypted third-party code.

## Admin inspection

Server administrators can, for any published mod (private ones included), download the plain DLL for moderation
purposes - checking that a reported mod doesn't do anything malicious - as well as the final `.c2m` package to
test it directly in game. **Every inspection is logged.** This is the only exception to the end-to-end encryption
described above.

## Native loader integrity checks

The native loader runs structural self-checks at startup (see [ARCHITECTURE.md](ARCHITECTURE.md)): detecting a
second instance of the .NET runtime already loaded in the process, or the presence of other known loader modules
attaching to the same game process.

## Reporting a vulnerability

If you find a security issue in the loader, the SDK, or [c2ml.l3g3nd.dev](https://c2ml.l3g3nd.dev) itself
(including the mod encryption/auth model above), please report it privately rather than opening a public issue:
**2410l3g3nd@gmail.com**. Include enough detail to reproduce it; you'll get a response before any public
disclosure.
