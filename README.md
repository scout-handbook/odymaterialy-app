# odymaterialy-app

Android app for [odymaterialy.skauting.cz](https://odymaterialy.skauting.cz) — a Czech Scout organization materials platform.

Built as a [Trusted Web Activity (TWA)](https://developer.chrome.com/docs/android/trusted-web-activity) using [Android Browser Helper](https://github.com/GoogleChrome/android-browser-helper). There is no Kotlin/Java business logic; all functionality lives in the web app.

## Build

```bash
./gradlew assembleDebug
```

Release builds require signing properties in `gradle.properties` — see `gradle_properties_keystore.sample`.

## Publishing a new version

1. Bump `versionCode` (integer, must increase) and `versionName` (human-readable) in `app/build.gradle`.
3. Build the release bundle:
   ```bash
   ./gradlew bundleRelease
   ```
   The AAB will be at `app/build/outputs/bundle/release/app-release.aab`.
4. Upload the AAB to Google Play Console and publish the release.

## CI

GitHub Actions runs `./gradlew build --no-configuration-cache` on all pushes and pull requests.
