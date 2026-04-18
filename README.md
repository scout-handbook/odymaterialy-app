# odymaterialy-app

Android app for [odymaterialy.skauting.cz](https://odymaterialy.skauting.cz) — a Czech Scout organization materials platform.

Built as a [Trusted Web Activity (TWA)](https://developer.chrome.com/docs/android/trusted-web-activity) using [Android Browser Helper](https://github.com/GoogleChrome/android-browser-helper). There is no Kotlin/Java business logic; all functionality lives in the web app.

## Build

```bash
./gradlew assembleDebug
```

Release builds require signing properties in `gradle.properties` — see `gradle_properties_keystore.sample`.

## CI

GitHub Actions runs `./gradlew build --no-configuration-cache` on all pushes and pull requests.
