name: OrderFlowRadar APK

on:
  workflow_dispatch:
  push:
    branches:
      - main
    paths:
      - 'OrderFlowRadar-source.zip'
      - '.github/workflows/build-apk.yml'

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Depoyu al
        uses: actions/checkout@v4

      - name: Java 17 kur
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Android SDK kur
        uses: android-actions/setup-android@v3

      - name: Android SDK 35 hazirla
        shell: bash
        run: |
          yes | sdkmanager --licenses >/dev/null 2>&1 || true
          sdkmanager "platforms;android-35" "build-tools;35.0.0"

      - name: Gradle 8.9 kur
        uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: '8.9'

      - name: Kaynak projeyi ac
        shell: bash
        run: |
          set -e
          rm -rf "$RUNNER_TEMP/orderflowradar"
          mkdir -p "$RUNNER_TEMP/orderflowradar"
          unzip -q OrderFlowRadar-source.zip -d "$RUNNER_TEMP/orderflowradar"

          SETTINGS_FILE="$(find "$RUNNER_TEMP/orderflowradar" -maxdepth 5 -type f -name settings.gradle | head -n 1)"
          if [ -z "$SETTINGS_FILE" ]; then
            echo "HATA: settings.gradle bulunamadi"
            exit 1
          fi

          PROJECT_DIR="$(dirname "$SETTINGS_FILE")"
          echo "PROJECT_DIR=$PROJECT_DIR" >> "$GITHUB_ENV"
          echo "Android proje klasoru: $PROJECT_DIR"

      - name: APK derle
        shell: bash
        run: |
          set -e
          echo "sdk.dir=$ANDROID_SDK_ROOT" > "$PROJECT_DIR/local.properties"
          cd "$PROJECT_DIR"
          gradle --no-daemon --stacktrace :app:assembleDebug

          APK="app/build/outputs/apk/debug/app-debug.apk"
          if [ ! -f "$APK" ]; then
            echo "HATA: APK olusmadi"
            exit 1
          fi

          cp "$APK" "$GITHUB_WORKSPACE/OrderFlowRadar-debug.apk"

      - name: APK dosyasini yukle
        uses: actions/upload-artifact@v4
        with:
          name: OrderFlowRadar-debug-apk
          path: OrderFlowRadar-debug.apk
          if-no-files-found: error
          retention-days: 7
