---
name: moonfin-haos
description: >
  Add this household's Home Assistant OS to Moonfin. Use when the ask is a
  thermostat, climate entity, Frigate feed, camera still, or any other HAOS
  device inside the Moonfin APK. Covers the Moonbase proxy and the Flutter
  screen. Do not use for ordinary Jellyfin playback.
---

# Moonfin ↔ HAOS

One feature, two repos. The APK never talks to Home Assistant.

- Plugin: [Moonfin-Client/Plugin](https://github.com/Moonfin-Client/Plugin), Jellyfin side, next to `Jellyfin/backend/Api/SeerrProxyController.cs`.
- Client: [Moonfin-Client/Moonfin-Core](https://github.com/Moonfin-Client/Moonfin-Core).

Read [assets/haos-card.md](assets/haos-card.md) before naming a URL, entity, or camera. `unknown` stays `unknown`. Read [references/ha-api.md](references/ha-api.md) only while writing the proxy or the screen.

## Shape

Moonbase already proxies Seerr at `Moonfin/Seerr` with `[Authorize]`, and stores the upstream secret on the server. Do the same for HA. Do not extend `SeerrProxyController`. Do not give the client a general `/api` pass-through: a Jellyfin login must not be able to open locks, run scripts, or call `shell_command`.

The proxy allowlist is plugin config, not a client argument:

- `climate.<id>` values from the card, and no other entity domain
- Frigate camera names from the card, and no other names

Call HA and Frigate from the Jellyfin host. HAOS ingress URLs work in the HA UI only. If the card has no LAN URL the Jellyfin host can reach, stop and ask.

The long-lived HA token lives in plugin config. It does not go in Dart, git, logs, or the card.

## One slice at a time

Finish one of these before starting the next: thermostat read, thermostat set, camera stills, camera live.

### Plugin

`Jellyfin/` only, unless the card says they also run Emby. Route `Moonfin/Ha`, `[Authorize]`, same idea as `SeerrProxyController`.

| Slice | Upstream | Client receives |
| --- | --- | --- |
| Thermostat read | `GET /api/states/climate.<id>` | temperature, setpoint, `hvac_mode`, `hvac_action`. Nothing else. |
| Thermostat set | `climate/set_temperature`, `climate/set_hvac_mode` | the updated state |
| Stills | Frigate `GET /api/<camera>/latest.jpg` | the JPEG |
| Live | no byte proxy | a password-free `rtsp://<frigate>:8554/<camera>` only if the card says this device is on that LAN |

Reject every other entity and every other path.

If `frigate_url` is `unknown` and `ha_url` is set, stills may fall back to HA `GET /api/camera_proxy/<entity_id>` for a card-listed camera entity. Say that the image is the low-res proxy. Do not invent a Frigate host.

Live video from off the LAN: stop. Do not build a relay, and do not switch to go2rtc HLS (`/api/stream.m3u8` is a poor, non-standard fallback).

### Client

New screen under `lib/ui/screens/`, registered in `lib/ui/navigation/`. Not under `lib/ui/screens/seerr/`.

The screen calls the signed-in Jellyfin origin only.

Stills are images. Refresh on the camera screen, not from the home row on a timer that runs all day.

Live playback goes through the existing Android backend `lib/playback/media3_player_backend.dart`, the same path `lib/ui/screens/livetv/live_tv_player_screen.dart` uses (`Media3 (ExoPlayer)`). Do not add `flutter_webrtc` or a second player.

TV (`androidTv` flavor, `MOONFIN_FORCE_TV`): D-pad. Setpoint is up/down on the focused control. HVAC mode is a focused list. A still opens live on enter. A phone-only layout is not done.

## Build

Do not claim either build ran unless it did.

```bash
# plugin, from Plugin/
cd Jellyfin && ./build.sh <version> <targetABI>
dotnet test Jellyfin/tests/Moonfin.Server.Tests/Moonfin.Server.Tests.csproj

# client, from Moonfin-Core/
flutter analyze && flutter test
flutter build apk --release --flavor mobile --dart-define=DISTRIBUTION_CHANNEL=apk
flutter build apk --release --flavor androidTv \
  --dart-define=MOONFIN_FORCE_TV=true \
  --dart-define=DISTRIBUTION_CHANNEL=android_tv_apk
```

Flutter pin and signing failures: `skills/moonfin-android`. Plugin SDK and zip path: `skills/moonbase`. This skill is the integration. Those two are only the toolchain.

Moonfin-Core is GPL-2.0. A distributed modified APK needs its source.

## Done when

- An allowlisted climate read returns the state, and a `lock.*` or any id not on the card is rejected.
- The token is not in the client binary, the response, or the card.
- The Flutter screen never uses the HA host.
- `dotnet test` and `flutter analyze` / `flutter test` passed, or the missing toolchain was stated instead of a fake pass.
