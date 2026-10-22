# Moonbase build

Source: Moonfin-Client/Plugin wiki, Building from Source, checked 2026-10-22. If `Jellyfin/build.sh` disagrees with this file, the script wins.

## SDK

.NET 8 SDK (`dotnet --version` prints `8.x`).

- Windows: `winget install Microsoft.DotNet.SDK.8`
- macOS: `brew install dotnet@8`
- Linux: the official dotnet-install script, channel `8.0`

`build.sh` also wants `zip`, `jq`, and `md5sum` (macOS: `md5`). Windows uses `build.ps1` and does not need those.

## Web bundle

Not committed. Either skip it, extract `moonfin-web-v<version>.tar.gz` from Moonfin-Core releases into `Jellyfin/frontend/` and `Emby/web/`, or from a Moonfin-Core checkout run `./build-web-plugin.sh`.

Missing frontend: the build warns, the zip has no web app, `/Moonfin/Web/` returns 404.

## Jellyfin

```bash
cd Jellyfin
./build.sh [version] [targetABI]    # example: ./build.sh 2.4.0.0 10.10.0
```

Windows:

```powershell
pwsh ./build.ps1 -Version "2.4.0.0" -TargetAbi "10.10.0"
```

`targetABI` must match the Jellyfin.Controller package version, for example `10.10.0`.

Output: `Jellyfin/Moonfin.Server-<version>.zip` containing `Moonfin.Server.dll`, `SharpCompress.dll`, `meta.json`, and `frontend/` when present. The script updates the `manifest.json` checksum.

Project: `Jellyfin/backend/Moonfin.Server.csproj`.

## Emby

```bash
cd Emby
./build.sh
```

Windows: `pwsh ./build.ps1`. Output: `Emby/Moonfin.Emby-<version>.zip`. This script does not edit `manifest.json`.

## Tests

```bash
dotnet test Jellyfin/tests/Moonfin.Server.Tests/Moonfin.Server.Tests.csproj
dotnet test Emby/tests/Emby.Plugins.Moonfin.Tests/Emby.Plugins.Moonfin.Tests.csproj
dotnet run --project Jellyfin/tools/salvage-tests -c Release
```

## Version bump

CI requires all four:

1. `Jellyfin/backend/Moonfin.Server.csproj` `MoonfinVersion`
2. `Jellyfin/build.sh` default version
3. `Emby/Emby.Plugins.Moonfin.csproj` `AssemblyVersion`
4. a new top entry in `manifest.json` (the Jellyfin build script writes the checksum)

## Where a manual zip goes

Use the server card. These are candidates, not facts:

| Install | Plugins folder |
| --- | --- |
| Linux package | `/var/lib/jellyfin/plugins/Moonfin/` |
| Docker | `/config/plugins/Moonfin/` inside the config volume |
| Windows | `%ProgramData%\Jellyfin\Server\plugins\Moonfin\` |

Extract the zip into that folder. Restart Jellyfin.
