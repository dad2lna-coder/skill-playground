---
name: moonfin-haos
description: >
  Put this household's Home Assistant OS into Moonfin. Use for any HAOS entity
  or feed they name (a thermostat or a Frigate camera is only an example):
  state, service calls, stills, and live streams. HAOS shares the Jellyfin
  server stack; TVs are on the home network; Tailscale covers everything else.
  An integration on HAOS is acceptable.
---

# Moonfin ↔ HAOS

Read [assets/haos-card.md](assets/haos-card.md) first. `unknown` stays `unknown`. Open [references/ha-api.md](references/ha-api.md) only while writing the call.

Thermostat and Frigate were examples of two shapes (an entity, and a live feed). They are not the product boundary. The next entity they name is in scope. Something not on the card is not called until they add it.

## This house

- HAOS and Jellyfin are on the same server stack. Talk between them over localhost or the stack network. Do not plan a port forward, a cloud relay, or an HAOS ingress URL.
- The TVs are on that same home network. A playable URL the TV opens directly is fine.
- Tailscale is how devices off the house still reach the stack. A Tailscale client is reachable. Do not stop because it is not on house Wi-Fi, and do not build a video relay for it.
- A custom integration on HAOS is acceptable. Prefer it. Do not avoid HAOS and stuff entity logic into the Flutter app.

The address a TV already uses for Jellyfin is the host. Only the port changes. Do not invent a hostname. HA's usual port is 8123; confirm it, do not write it into the card as a fact until it is checked.

## Shape

Two pieces, not three:

1. **Custom integration on HAOS.** It owns every HA call. It exposes only the entities and services listed on the card. It lives in `config/custom_components/`, not as a separate add-on, unless they ask for a separate process.
2. **Moonfin client** in [Moonfin-Core](https://github.com/Moonfin-Client/Moonfin-Core). New screen under `lib/ui/screens/`, registered in `lib/ui/navigation/`. Not under `lib/ui/screens/seerr/`.

The TV already has a Jellyfin session. Keep that as the login. A thin route on [Moonbase](https://github.com/Moonfin-Client/Plugin) (`Jellyfin/backend/Api/`, beside `SeerrProxyController`, route `Moonfin/Ha`, `[Authorize]`) calls the HA integration on the stack and returns the result. The APK never holds an HA admin token and never calls HA itself.

Do not extend `SeerrProxyController`. Do not proxy arbitrary `/api` paths. The card is the allowlist.

Live bytes stay off that route. The integration returns a URL on this stack (home network or Tailscale) and the existing player opens it: `lib/playback/media3_player_backend.dart`, same path as `lib/ui/screens/livetv/live_tv_player_screen.dart`. No second player and no `flutter_webrtc` unless that URL cannot be played and they ask.

## One slice

Ship one entity or one feed before the next. Match the shape:

| They asked for | HA integration | What Moonfin shows |
| --- | --- | --- |
| An entity (thermostat, lock, sensor, …) | `GET /api/states/<entity_id>`, then only the service they named | State, and the one control |
| A feed (Frigate or any other camera) | A still, plus one playable URL | Still on the row. Enter plays it |

Return the fields the screen renders. Do not return raw `attributes`.

TV (`androidTv`, `MOONFIN_FORCE_TV`): D-pad. One focused control. A still opens playback on enter. Phone layout alone is not done.

## Build

Do not claim a build ran unless it did.

```bash
# Moonbase, from Plugin/ — only if this slice adds the route
cd Jellyfin && ./build.sh <version> <targetABI>
dotnet test Jellyfin/tests/Moonfin.Server.Tests/Moonfin.Server.Tests.csproj

# client, from Moonfin-Core/
flutter analyze && flutter test
flutter build apk --release --flavor mobile --dart-define=DISTRIBUTION_CHANNEL=apk
flutter build apk --release --flavor androidTv \
  --dart-define=MOONFIN_FORCE_TV=true \
  --dart-define=DISTRIBUTION_CHANNEL=android_tv_apk
```

Toolchain pins: `skills/moonfin-android` and `skills/moonbase`. This skill is the integration.

Moonfin-Core is GPL-2.0. A distributed modified APK needs its source.

## Done when

- The new entity or feed is on the card, and a call for anything not on the card is rejected.
- No HA admin token in the APK, the response, git, or the card.
- The TV uses its existing Jellyfin login. Playback uses the existing Media3 backend.
- Tests passed, or the missing toolchain was named instead of a fake pass.
