# Package id and signing

Look up the current ids in `android/app/build.gradle` or `build.gradle.kts` under `productFlavors`. Do not invent them.

The Play listing for the TV app is `org.moonfin.androidtv`. Confirm the mobile flavor before writing it down. The two flavors can differ.

## Side by side with the store app

A fork that keeps the store `applicationId` replaces the store install. It cannot sit next to it.

To keep both:

- Change `applicationId` (and the namespace only if resources require it).
- Use a label the user can tell apart on the home screen.
- Sign with the fork's own keystore. A later install must use that same key.

## Same id, new key

`INSTALL_FAILED_UPDATE_INCOMPATIBLE` means the installed package was signed by a different key. Uninstall the old app, then install. User data and logins on that package go with it.

## Release signing

Do not commit keystores, `key.properties`, or passwords. Debug builds are enough until the user asks for a key they can keep updating.

Moonfin-Core is GPL-2.0. Publishing a modified APK (sideload page, Drive link, chat attachment to other people) requires offering the matching source. A private install on the owner's own device is still their binary; do not strip license files.
