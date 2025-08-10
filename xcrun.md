# xcrun (Xcode Tool Runner) - Detailed Guide

> 📁 **Try it:** See `xcrun-examples/` for working examples

`xcrun` is a command-line tool that locates and runs tools within Xcode toolchains and SDKs. It's essential for cross-platform compilation, simulator management, and accessing Xcode tools.

## Overview

`xcrun` serves as a bridge between command-line tools and Xcode's toolchain, allowing you to:
- Find and run Xcode tools
- Access specific SDKs
- Control iOS simulators
- Manage physical devices
- Compile for different platforms

## Basic Usage

### Finding tools
```bash
# Find Swift compiler
xcrun --find swift

# Find Clang compiler
xcrun --find clang

# Find specific tool
xcrun --find xcresulttool
```

### Running tools with specific SDK
```bash
# Run swiftc with iOS Simulator SDK
xcrun --sdk iphonesimulator swiftc main.swift -o app

# Run clang with iOS Device SDK
xcrun --sdk iphoneos clang -c source.c -o source.o
```

## SDK Management

### Available SDKs
```bash
# List all available SDKs
xcodebuild -showsdks

# Show specific SDK information
xcrun --show-sdk-path --sdk macosx
xcrun --show-sdk-path --sdk iphonesimulator
xcrun --show-sdk-path --sdk iphoneos
xcrun --show-sdk-path --sdk watchos
xcrun --show-sdk-path --sdk appletvos
```

### SDK paths for different platforms
```bash
# macOS SDK
xcrun --show-sdk-path --sdk macosx
# Output: /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk

# iOS Simulator SDK
xcrun --show-sdk-path --sdk iphonesimulator
# Output: /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator.sdk

# iOS Device SDK
xcrun --show-sdk-path --sdk iphoneos
# Output: /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk
```

### Using SDKs with compilation
```bash
# Compile Swift for iOS Simulator
xcrun --sdk iphonesimulator swiftc main.swift -target arm64-apple-ios18.5-simulator -o app

# Compile Swift for iOS Device
xcrun --sdk iphoneos swiftc main.swift -target arm64-apple-ios18.5 -o app

# Compile C for macOS
xcrun --sdk macosx clang -c source.c -o source.o
```

## Simulator Control (simctl)

### Listing devices
```bash
# List all devices (simulators and physical devices)
xcrun simctl list devices

# List only iOS simulators
xcrun simctl list devices iOS

# List only watchOS simulators
xcrun simctl list devices watchOS

# List only tvOS simulators
xcrun simctl list devices tvOS

# List device types
xcrun simctl list devicetypes

# List runtimes (iOS versions)
xcrun simctl list runtimes
```

### Creating simulators
```bash
# Create iPhone 15 simulator
xcrun simctl create "My iPhone 15" "iPhone 15" "iOS18.5"

# Create iPad simulator
xcrun simctl create "My iPad" "iPad Pro (12.9-inch) (6th generation)" "iOS18.5"

# Create Apple Watch simulator
xcrun simctl create "My Watch" "Apple Watch Series 9 (45mm)" "watchOS10.5"
```

### Managing simulators
```bash
# Boot a simulator
xcrun simctl boot "My iPhone 15"

# Shutdown a simulator
xcrun simctl shutdown "My iPhone 15"

# Shutdown all simulators
xcrun simctl shutdown all

# Erase a simulator (factory reset)
xcrun simctl erase "My iPhone 15"

# Erase all simulators
xcrun simctl erase all
```

### Installing and running apps
```bash
# Install app on booted simulator
xcrun simctl install booted /path/to/MyApp.app

# Install app on specific simulator
xcrun simctl install "My iPhone 15" /path/to/MyApp.app

# Launch app on booted simulator
xcrun simctl launch booted com.example.MyApp

# Launch app with arguments
xcrun simctl launch booted com.example.MyApp -- --debug --verbose

# Uninstall app
xcrun simctl uninstall booted com.example.MyApp
```

### Simulator interaction
```bash
# Take screenshot
xcrun simctl io booted screenshot screenshot.png

# Record video
xcrun simctl io booted recordVideo video.mov

# Send push notification
xcrun simctl push booted com.example.MyApp notification.json

# Get app container path
xcrun simctl get_app_container booted com.example.MyApp

# Open URL in simulator
xcrun simctl openurl booted "https://example.com"
```

## Device Control (devicectl)

### Listing devices
```bash
# List connected devices
xcrun devicectl list devices

# List devices with details
xcrun devicectl list devices --verbose
```

### Installing apps on devices
```bash
# Install IPA on device
xcrun devicectl device install app --device DEVICE-UDID /path/to/MyApp.ipa

# Install app bundle on device
xcrun devicectl device install app --device DEVICE-UDID /path/to/MyApp.app

# Install on first available device
xcrun devicectl device install app /path/to/MyApp.ipa
```

### Device management
```bash
# Get device info
xcrun devicectl device info --device DEVICE-UDID

# List installed apps
xcrun devicectl device list apps --device DEVICE-UDID

# Uninstall app
xcrun devicectl device uninstall app --device DEVICE-UDID com.example.MyApp
```

## Result Bundle Tools (xcresulttool)

### Working with test results
```bash
# Get test summary from .xcresult
xcrun xcresulttool get --format json --path TestResults.xcresult

# Get specific test info
xcrun xcresulttool get --format json --path TestResults.xcresult --id test-id

# Export test attachments
xcrun xcresulttool export --type attachments --path TestResults.xcresult --output-path ./attachments

# Export screenshots
xcrun xcresulttool export --type screenshots --path TestResults.xcresult --output-path ./screenshots

# Export logs
xcrun xcresulttool export --type logs --path TestResults.xcresult --output-path ./logs
```

### Result bundle analysis
```bash
# Show result bundle structure
xcrun xcresulttool get --format json --path TestResults.xcresult | jq '.'

# Get code coverage
xcrun xcresulttool get --format json --path TestResults.xcresult | jq '.actions[0].actionResult.coverage'

# Get test statistics
xcrun xcresulttool get --format json --path TestResults.xcresult | jq '.actions[0].actionResult.testSummary'
```

## Tool Location and Execution

### Finding tools
```bash
# Find Swift compiler
xcrun --find swift
# Output: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swift

# Find Clang compiler
xcrun --find clang
# Output: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/clang

# Find specific tool
xcrun --find xcresulttool
xcrun --find simctl
xcrun --find devicectl
```

### Running tools with specific toolchain
```bash
# Run Swift with specific toolchain
xcrun --toolchain Swift swift --version

# Run Clang with specific toolchain
xcrun --toolchain Swift clang --version
```

## Cross-Platform Compilation

### iOS Simulator compilation
```bash
# Compile Swift for iOS Simulator
xcrun --sdk iphonesimulator swiftc main.swift \
  -target arm64-apple-ios18.5-simulator \
  -o app

# Compile C for iOS Simulator
xcrun --sdk iphonesimulator clang -c source.c \
  -target arm64-apple-ios18.5-simulator \
  -o source.o
```

### iOS Device compilation
```bash
# Compile Swift for iOS Device
xcrun --sdk iphoneos swiftc main.swift \
  -target arm64-apple-ios18.5 \
  -o app

# Compile C for iOS Device
xcrun --sdk iphoneos clang -c source.c \
  -target arm64-apple-ios18.5 \
  -o source.o
```

### macOS compilation
```bash
# Compile Swift for macOS
xcrun --sdk macosx swiftc main.swift \
  -target arm64-apple-macosx14.0 \
  -o app

# Compile C for macOS
xcrun --sdk macosx clang -c source.c \
  -target arm64-apple-macosx14.0 \
  -o source.o
```

## Advanced Usage

### Multiple SDKs
```bash
# Compile for multiple platforms
xcrun --sdk iphonesimulator swiftc main.swift -target arm64-apple-ios18.5-simulator -o app-sim
xcrun --sdk iphoneos swiftc main.swift -target arm64-apple-ios18.5 -o app-dev
```

### Custom toolchains
```bash
# Use specific toolchain
xcrun --toolchain Swift-5.9 swift --version

# List available toolchains
xcrun --find-toolchain Swift
```

### Environment variables
```bash
# Set developer directory
DEVELOPER_DIR=/Applications/Xcode.app xcrun --find swift

# Set SDK path
SDKROOT=$(xcrun --show-sdk-path --sdk iphonesimulator) xcrun swiftc main.swift -o app
```

## Troubleshooting

### Common issues
```bash
# Check Xcode installation
xcrun --version

# Verify SDK availability
xcodebuild -showsdks

# Check tool availability
xcrun --find swift

# Verify simulator availability
xcrun simctl list devices
```

### Debugging
```bash
# Verbose output
xcrun --verbose --find swift

# Show toolchain info
xcrun --show-toolchain-info

# List all available tools
xcrun --find-all-tools
```

## Integration with Build Systems

### Makefile integration
```makefile
SWIFT := xcrun --sdk iphonesimulator swiftc
TARGET := arm64-apple-ios18.5-simulator

app: main.swift
	$(SWIFT) main.swift -target $(TARGET) -o app

clean:
	rm -f app
```

### CI/CD integration
```bash
# Set up environment
export DEVELOPER_DIR=/Applications/Xcode.app

# Compile for simulator
xcrun --sdk iphonesimulator swiftc main.swift -target arm64-apple-ios18.5-simulator -o app

# Run tests on simulator
xcrun simctl boot "iPhone 15"
xcrun simctl install booted app
xcrun simctl launch booted com.example.MyApp
```

## Best Practices

### SDK selection
- Always specify the correct SDK for your target platform
- Use `--sdk` flag to ensure consistent compilation
- Verify SDK availability before compilation

### Simulator management
- Use descriptive names for custom simulators
- Clean up unused simulators regularly
- Use `booted` for convenience when only one simulator is running

### Tool location
- Use `xcrun --find` to locate tools dynamically
- Don't hardcode tool paths in scripts
- Handle missing tools gracefully

### Error handling
```bash
# Check if tool exists before using
if xcrun --find swift > /dev/null 2>&1; then
    xcrun swift --version
else
    echo "Swift not found"
    exit 1
fi
```

This comprehensive guide covers most `xcrun` use cases. For the latest features and updates, check the Xcode documentation and `xcrun --help`.
