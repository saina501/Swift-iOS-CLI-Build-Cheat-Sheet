# xcrun Examples

This folder contains practical examples of using `xcrun` for various Xcode toolchain operations.

## Examples

### 1. SDK Path Examples
- Find SDK paths for different platforms
- Use SDKs with swiftc compilation

### 2. Simulator Control Examples
- List available simulators
- Boot, install, and launch apps on simulators

### 3. Tool Location Examples
- Find Xcode tools
- Run tools with specific SDKs

### 4. Device Control Examples
- Install apps on physical devices
- Manage device connections

## Quick Start

```bash
# Find iOS Simulator SDK path
xcrun --show-sdk-path --sdk iphonesimulator

# List available simulators
xcrun simctl list devices

# Compile Swift for iOS Simulator
xcrun --sdk iphonesimulator swiftc main.swift -target arm64-apple-ios18.5-simulator -o app
```

## Prerequisites

- Xcode installed
- iOS Simulator available
- Physical device (optional, for device examples)
