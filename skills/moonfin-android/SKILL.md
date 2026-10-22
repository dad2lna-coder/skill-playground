---
name: moonfin-android
description: >
  Build and modify custom Moonfin Android APKs from Moonfin-Client/Moonfin-Core
  (Flutter). Use for Moonfin forks, sideload builds, phone vs Android TV flavors,
  applicationId, signing keys, Gradle, Media3, or ExoPlayer changes. Not the
  Jellyfin server and not the Moonbase plugin.
---

# Moonfin Android

Upstream is [Moonfin-Client/Moonfin-Core](https://github.com/Moonfin-Client/Moonfin-Core), Flutter/Dart, GPL-2.0. Do not use `RadicalMuffinMan/moonfin-server` for the client. Do not copy upstream source into this skill repo.

Phone and Android TV are flavors of one app, not two projects. Every Android build passes `--flavor`. A missing flavor is the usual first failure.

Read [references/android-build.md](references/android-build.md) for the pinned toolchain, commands, output paths, and the Android troubleshooting table. Read [references/identity.md](references/identity.md) before changing the package id or signing a release.

## Before editing

- Confirm the tree is Moonfin-Core (`pubspec.yaml`, `android/app`, flavors `mobile` and `androidTv`).
- Match Flutter to `.github/workflows/build.yml` (`FLUTTER_VERSION`). As of the 2026-10 wiki, that pin is **3.47.2** stable. `pubspec.yaml` allows 3.47.0 as the floor. Older stable releases often do not build.
- Run `flutter analyze` and `flutter test` before calling a change done.
- Playback bugs: Android uses Media3 / ExoPlayer and reports the device's real decoders to the server. Check `jellyfin-playback` and the server card before rewriting the player.

## Do not

- Claim an APK was produced unless `flutter build apk` actually ran and the APK path exists.
- Ship a fork that keeps the Play Store application id and a different signing key. Android then refuses the update. See the identity note.
- Treat a web preview or a widget test as proof the TV flavor works. TV needs `MOONFIN_FORCE_TV=true` and a D-pad pass on a device or emulator.
