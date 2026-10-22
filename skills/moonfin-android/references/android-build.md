# Android build

Source: Moonfin-Core wiki, Building from Source, checked 2026-10-22. Re-read `.github/workflows/build.yml` if this file and the workflow disagree. The workflow wins.

## Toolchain

| Tool | Pin |
| --- | --- |
| Flutter | 3.47.2 stable (3.47.0 minimum) |
| Dart | ships with Flutter (3.13 at that Flutter) |
| JDK | Temurin 17 |
| Android NDK | 28.2.13676358 |
| CMake | 3.22.1 |

CI runner for Android is `ubuntu-latest` with Temurin JDK 17.

## Commands

From a Moonfin-Core checkout, after `flutter pub get`:

```bash
# Phones and tablets
flutter build apk --release --flavor mobile --dart-define=DISTRIBUTION_CHANNEL=apk

# Android TV, Google TV, Fire TV
flutter build apk --release --flavor androidTv \
  --dart-define=MOONFIN_FORCE_TV=true \
  --dart-define=DISTRIBUTION_CHANNEL=android_tv_apk
```

Outputs (repo `build/`, not `android/app/build/`):

- `build/app/outputs/flutter-apk/app-mobile-release.apk`
- `build/app/outputs/flutter-apk/app-androidtv-release.apk`

Each APK contains `arm64-v8a`, `armeabi-v7a`, and `x86_64`. Add `--split-per-abi` for one APK per ABI.

`MOONFIN_FORCE_TV=true` turns on the TV interface at compile time. Do not drop it on a TV build.

## Troubleshooting

| Symptom | Fix |
| --- | --- |
| You must specify a `--flavor` option, or the build lists product flavors | Add `--flavor mobile` or `--flavor androidTv` |
| Java heap space, Gradle daemon disappears | Lower `-Xmx8G` in `android/gradle.properties` |
| `sdkmanager` not found, licenses not accepted | Install Android SDK Command-line Tools, then `flutter doctor --android-licenses` |
| `INSTALL_FAILED_UPDATE_INCOMPATIBLE` | Installed app was signed with a different key. Uninstall it first. See identity.md |
| NDK or CMake version errors | Install NDK 28.2.13676358 and CMake 3.22.1 |
| Stale errors after a Flutter or machine change | `flutter clean && flutter pub get` |
