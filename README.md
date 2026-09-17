name: Build Android APK

on:
  push:
    branches: [ main, master ]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Setup Java 17
        uses: actions/setup-java@v4
        with:
          distribution: 'zulu'
          java-version: '17'

      - name: Install & Build
        run: |
          npm install
          npm install @capacitor/core @capacitor/cli @capacitor/android
          npm run build

      - name: Init Capacitor & Android
        run: |
          npx cap init "CBSE Math 10" "com.cbsemath.class10quiz" --web-dir dist
          npx cap add android

      - name: Build Debug APK with Gradle
        working-directory: ./android
        run: |
          chmod +x gradlew
          ./gradlew assembleDebug --no-daemon

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: CBSE-Math-Class10-Quiz-APK
          path: android/app/build/outputs/apk/debug/app-debug.apk
