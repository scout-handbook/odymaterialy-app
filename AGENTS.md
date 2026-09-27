# AGENTS.md

This file provides guidance to coding agents when working with code in this repository.

## Project Overview

This is a **Trusted Web Activity (TWA)** Android wrapper for `https://odymaterialy.skauting.cz` — a Czech Scout organization materials app. There is **no Kotlin/Java source code**: all business logic lives in the web application. The Android project is a configuration-only shell (single `app` module) providing native app packaging, deep linking, a splash screen, and file sharing.

## Build Commands

```bash
# Standard build (used in CI) — compiles all variants and runs Android lint
./gradlew build --no-configuration-cache

# Debug APK
./gradlew assembleDebug

# Release bundle for Google Play (output: app/build/outputs/bundle/release/app-release.aab)
./gradlew bundleRelease
```

There are no tests.

## Signing Properties

`app/build.gradle` references `RELEASE_STORE_FILE`, `RELEASE_STORE_PASSWORD`, `RELEASE_KEY_ALIAS` and `RELEASE_KEY_PASSWORD` unconditionally at configuration time, so **every** Gradle invocation (including debug builds) fails if they are undefined. Define them in `~/.gradle/gradle.properties` (not the tracked project `gradle.properties`) — see `gradle_properties_keystore.sample`.

To build locally without the real key, do what CI does:

```bash
keytool -genkey -keystore upload-keystore.jks -keyalg RSA -keysize 2048 -validity 10000 -alias upload \
  -dname "cn=Unknown, ou=Unknown, o=Unknown, c=Unknown" -storepass password -keypass password
cp gradle_properties_keystore.sample ~/.gradle/gradle.properties
```

## Architecture

Uses the **single-activity TWA pattern** via [Android Browser Helper](https://github.com/GoogleChrome/android-browser-helper): `AndroidManifest.xml` declares the library's `LauncherActivity` and configures it entirely through `<meta-data>` (default URL, splash drawable/color, FileProvider authority). There is no custom activity.

- `res/values/strings.xml` — `url`, `host`, and the Digital Asset Links `asset_statements`. The manifest's deep-link intent filter reads `@string/host`, so **changing the target site only requires editing `strings.xml`** (all three values).
- The FileProvider authority `cz.skaut.odyssea.odymaterialy.fileprovider` is hard-coded twice in `AndroidManifest.xml` (the `<provider>` and the `FILE_PROVIDER_AUTHORITY` meta-data); both must match each other and the `applicationId`.
- `res/xml/filepaths.xml` — cache paths exposed via the FileProvider for sharing.

## Releasing

1. Bump `versionCode` (must increase) and `versionName` in `app/build.gradle`.
2. `./gradlew bundleRelease` with the real signing properties.
3. Upload the AAB to Google Play Console.

## CI/CD

GitHub Actions (`.github/workflows/CI.yml`) runs on all pushes and PRs: JDK 17 (Temurin), generates a dummy keystore, and runs `./gradlew build --no-configuration-cache`. Note that the configuration cache is enabled in `gradle.properties` but disabled in CI. Dependabot updates Gradle and GitHub Actions dependencies weekly.
