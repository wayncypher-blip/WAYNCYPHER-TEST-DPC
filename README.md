name: Build and release APK

on:
  push:
    tags:
      - "v*"

permissions:
  contents: write

jobs:
  release:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: "17"
          cache: gradle

      - name: Grant Gradle execute permission
        run: chmod +x ./gradlew

      - name: Build release APK
        run: ./gradlew assembleRelease

      - name: Rename APK
        run: |
          mkdir -p dist
          find app/build/outputs/apk/release \
            -name "*.apk" \
            -exec cp {} dist/wayncypher-testdpc.apk \;

      - name: Create GitHub release
        uses: softprops/action-gh-release@v2
        with:
          files: dist/wayncypher-testdpc.apk
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
