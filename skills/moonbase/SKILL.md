---
name: moonbase
description: >
  Modify or build Moonbase, the Moonfin server plugin (Moonfin-Client/Plugin)
  for Jellyfin 10.10+ and Emby 4.8+. Use for settings sync, the hosted Moonfin
  web app, Seerr proxy, themes, plugin catalog, or a Moonfin.Server zip. Not
  the Flutter client.
---

# Moonbase

Upstream is [Moonfin-Client/Plugin](https://github.com/Moonfin-Client/Plugin). One repo, two plugins: `Jellyfin/` (.NET 8) and `Emby/` (.NET Standard 2.1, built with the .NET 8 SDK). Ignore `RadicalMuffinMan/moonfin-server`. That tree is an abandoned WIP.

Read [references/build.md](references/build.md) before compiling. Read `jellyfin-server` before telling the user where to copy a zip.

## What it is

Moonbase is the shared backend for Moonfin clients: settings sync, server-wide defaults, extra ratings, Seerr proxy, themes, and the hosted web app at `/Moonfin/Web/`. The web bundle is not in the plugin git tree. A build without `Jellyfin/frontend/` still produces a plugin, and `/Moonfin/Web/` then 404s.

Catalog install (preferred for a stock server):

- Name: `Moonbase`
- URL: `https://raw.githubusercontent.com/Moonfin-Client/Plugin/refs/heads/master/manifest.json`

Dashboard, Plugins, Repositories, then Catalog, then restart Jellyfin. Minimum server: Jellyfin 10.10 or Emby 4.8.

## Before editing

- Jellyfin work stays under `Jellyfin/`. Emby work stays under `Emby/`. Do not "fix" one by editing the other.
- A version bump touches the four places listed in the build note. CI checks that they match.
- Run the Jellyfin tests before calling a plugin change done:

```bash
dotnet test Jellyfin/tests/Moonfin.Server.Tests/Moonfin.Server.Tests.csproj
```

- After a manual install, Jellyfin must restart. The card's `plugins_dir` is the only path to quote. If it is `unknown`, say so and give the install-type table from the build note as candidates, not as this server's path.
