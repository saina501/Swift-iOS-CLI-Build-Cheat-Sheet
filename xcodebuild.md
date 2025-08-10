# xcodebuild (Xcode Build System) - Detailed Guide

> 📁 **Try it:** See `xcodebuild-examples/` for working examples

`xcodebuild` is the command-line interface to Xcode's build system. It can build projects, run tests, create archives, and export IPAs for iOS, macOS, watchOS, and tvOS applications.

## Project Discovery

### Listing project information
```bash
# List schemes, targets, and configurations for a project
xcodebuild -list -project MyApp.xcodeproj

# List information for a workspace
xcodebuild -list -workspace MyApp.xcworkspace

# Show all available SDKs
xcodebuild -showsdks

# Show build settings for a scheme
xcodebuild -showBuildSettings -scheme MyApp -workspace MyApp.xcworkspace

# Show build settings in JSON format
xcodebuild -showBuildSettings -json -scheme MyApp
```

### Finding schemes and targets
```bash
# Find all schemes in current directory
find . -name "*.xcworkspace" -exec xcodebuild -list -workspace {} \;

# Show only scheme names
xcodebuild -list -workspace MyApp.xcworkspace 2>/dev/null | grep -A 100 "Schemes:" | grep "    " | sed 's/^    //'
```

## Destinations

### iOS destinations
```bash
# iPhone Simulator (current iOS)
-destination 'platform=iOS Simulator,name=iPhone 15'
-destination 'platform=iOS Simulator,name=iPhone 15 Pro Max'

# iPad Simulator
-destination 'platform=iOS Simulator,name=iPad Pro (12.9-inch) (6th generation)'

# Specific simulator by ID
-destination 'platform=iOS Simulator,id=SIMULATOR-UUID'

# Generic iOS (for archiving)
-destination 'generic/platform=iOS'

# Physical device by name
-destination 'platform=iOS,name=My iPhone'

# Physical device by ID
-destination 'platform=iOS,id=DEVICE-UDID'
```

### macOS destinations
```bash
# Mac (current architecture)
-destination 'platform=macOS'

# Specific Mac architecture
-destination 'platform=macOS,arch=x86_64'
-destination 'platform=macOS,arch=arm64'

# Mac Catalyst
-destination 'platform=macOS,variant=Mac Catalyst'
```

### watchOS and tvOS destinations
```bash
# Apple Watch Simulator
-destination 'platform=watchOS Simulator,name=Apple Watch Series 9 (45mm)'

# Apple TV Simulator  
-destination 'platform=tvOS Simulator,name=Apple TV 4K (3rd generation)'

# Generic destinations for archiving
-destination 'generic/platform=watchOS'
-destination 'generic/platform=tvOS'
```

### Finding available destinations
```bash
# List iOS simulators
xcrun simctl list devices iOS

# List all available destinations for a scheme
xcodebuild -scheme MyApp -showdestinations
```

## Building

### Basic building
```bash
# Build a workspace scheme
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -configuration Debug \
  -destination 'platform=iOS Simulator,name=iPhone 15' \
  build

# Build a project
xcodebuild \
  -project MyApp.xcodeproj \
  -target MyApp \
  -configuration Release \
  build

# Clean before building
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  clean build
```

### Advanced building options
```bash
# Custom derived data path
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -derivedDataPath ./build/DerivedData \
  build

# Parallel building
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -jobs 4 \
  build

# Quiet output (only show errors/warnings)
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -quiet \
  build

# Build with specific settings
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  DEVELOPMENT_TEAM=ABC123DEF4 \
  CODE_SIGN_IDENTITY="iPhone Developer" \
  build
```

### Build for multiple destinations
```bash
# Build universal iOS app
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -configuration Release \
  -destination 'generic/platform=iOS' \
  ONLY_ACTIVE_ARCH=NO \
  build
```

## Testing

### Basic testing
```bash
# Run all tests
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -destination 'platform=iOS Simulator,name=iPhone 15' \
  test

# Test with result bundle
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -destination 'platform=iOS Simulator,name=iPhone 15' \
  -resultBundlePath TestResults.xcresult \
  test
```

### Advanced testing options
```bash
# Enable code coverage
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -destination 'platform=iOS Simulator,name=iPhone 15' \
  -enableCodeCoverage YES \
  test

# Run specific tests
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -destination 'platform=iOS Simulator,name=iPhone 15' \
  -only-testing:MyAppTests/LoginTests \
  test

# Skip specific tests
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -destination 'platform=iOS Simulator,name=iPhone 15' \
  -skip-testing:MyAppUITests \
  test

# Run tests in parallel
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -destination 'platform=iOS Simulator,name=iPhone 15' \
  -parallel-testing-enabled YES \
  test
```

### Test plans
```bash
# Use specific test plan
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -destination 'platform=iOS Simulator,name=iPhone 15' \
  -testPlan UnitTests \
  test

# List available test plans
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -list
```

### Testing on multiple destinations
```bash
# Test on multiple simulators
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -destination 'platform=iOS Simulator,name=iPhone 15' \
  -destination 'platform=iOS Simulator,name=iPad Pro (12.9-inch) (6th generation)' \
  test
```

## Archiving and Distribution

### Creating archives
```bash
# Create archive for iOS
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -configuration Release \
  -destination 'generic/platform=iOS' \
  -archivePath ./build/MyApp.xcarchive \
  archive

# Archive with automatic provisioning
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -configuration Release \
  -destination 'generic/platform=iOS' \
  -archivePath ./build/MyApp.xcarchive \
  -allowProvisioningUpdates \
  archive

# Archive for macOS
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -configuration Release \
  -destination 'platform=macOS' \
  -archivePath ./build/MyApp.xcarchive \
  archive
```

### Exporting IPAs
```bash
# Export for App Store
xcodebuild \
  -exportArchive \
  -archivePath ./build/MyApp.xcarchive \
  -exportPath ./build/AppStore \
  -exportOptionsPlist ExportOptions-AppStore.plist

# Export for Ad Hoc distribution
xcodebuild \
  -exportArchive \
  -archivePath ./build/MyApp.xcarchive \
  -exportPath ./build/AdHoc \
  -exportOptionsPlist ExportOptions-AdHoc.plist

# Export for development
xcodebuild \
  -exportArchive \
  -archivePath ./build/MyApp.xcarchive \
  -exportPath ./build/Development \
  -exportOptionsPlist ExportOptions-Development.plist
```

### Export Options Plists

#### App Store Export
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>method</key>
    <string>app-store</string>
    <key>uploadSymbols</key>
    <true/>
    <key>compileBitcode</key>
    <false/>
    <key>signingStyle</key>
    <string>automatic</string>
    <key>destination</key>
    <string>export</string>
</dict>
</plist>
```

#### Ad Hoc Export
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>method</key>
    <string>ad-hoc</string>
    <key>uploadSymbols</key>
    <true/>
    <key>compileBitcode</key>
    <false/>
    <key>signingStyle</key>
    <string>automatic</string>
    <key>destination</key>
    <string>export</string>
</dict>
</plist>
```

#### Development Export
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>method</key>
    <string>development</string>
    <key>uploadSymbols</key>
    <true/>
    <key>compileBitcode</key>
    <false/>
    <key>signingStyle</key>
    <string>automatic</string>
    <key>destination</key>
    <string>export</string>
</dict>
</plist>
```

#### Manual Signing
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>method</key>
    <string>app-store</string>
    <key>signingStyle</key>
    <string>manual</string>
    <key>provisioningProfiles</key>
    <dict>
        <key>com.example.MyApp</key>
        <string>MyApp App Store Profile</string>
    </dict>
</dict>
</plist>
```

## Swift Package Manager Integration

### SPM in Xcode projects
```bash
# Resolve SPM dependencies
xcodebuild \
  -resolvePackageDependencies \
  -workspace MyApp.xcworkspace \
  -scheme MyApp

# Build with SPM resolution disabled
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -disableAutomaticPackageResolution \
  build

# Clean SPM cache
rm -rf ~/Library/Caches/org.swift.swiftpm
rm -rf ~/Library/org.swift.swiftpm
```

## Code Signing

### Automatic signing
```bash
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -allowProvisioningUpdates \
  -allowProvisioningDeviceRegistration \
  archive
```

### Manual signing
```bash
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  CODE_SIGN_IDENTITY="iPhone Distribution: My Company (ABC123DEF4)" \
  PROVISIONING_PROFILE_SPECIFIER="MyApp Distribution Profile" \
  DEVELOPMENT_TEAM=ABC123DEF4 \
  archive
```

### Signing troubleshooting
```bash
# List available signing identities
security find-identity -v -p codesigning

# List installed provisioning profiles
ls -la ~/Library/MobileDevice/Provisioning\ Profiles/

# Show provisioning profile details
security cms -D -i ~/Library/MobileDevice/Provisioning\ Profiles/profile.mobileprovision
```

## Result Analysis

### Working with .xcresult bundles
```bash
# Get test summary
xcrun xcresulttool get --format json --path TestResults.xcresult

# Extract test attachments
xcrun xcresulttool export --type attachments --path TestResults.xcresult --output-path ./attachments

# Get code coverage report
xcrun xcresulttool get --format json --path TestResults.xcresult | jq '.actions[0].actionResult.coverage'

# Export screenshots
xcrun xcresulttool export --type screenshots --path TestResults.xcresult --output-path ./screenshots
```

### Log analysis
```bash
# Pretty print logs with xcpretty
xcodebuild [...] | xcpretty

# Save logs to file
xcodebuild [...] 2>&1 | tee build.log

# Show only warnings and errors
xcodebuild [...] | xcpretty --report html --output build-report.html
```

## Simulator Management

### Basic simulator operations
```bash
# List available simulators
xcrun simctl list devices

# Boot a simulator
xcrun simctl boot 'iPhone 15'

# Install app on simulator
xcrun simctl install booted ./build/Debug-iphonesimulator/MyApp.app

# Launch app on simulator
xcrun simctl launch booted com.example.MyApp

# Uninstall app from simulator
xcrun simctl uninstall booted com.example.MyApp

# Shutdown simulator
xcrun simctl shutdown 'iPhone 15'
```

### Simulator data management
```bash
# Reset simulator to factory settings
xcrun simctl erase 'iPhone 15'

# Get app container path
xcrun simctl get_app_container booted com.example.MyApp

# Take screenshot
xcrun simctl io booted screenshot screenshot.png

# Record video
xcrun simctl io booted recordVideo video.mov
```

## CI/CD Integration

### GitHub Actions example
```yaml
name: iOS Build and Test

on: [push, pull_request]

jobs:
  build:
    runs-on: macos-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Select Xcode version
      run: sudo xcode-select -s /Applications/Xcode_15.0.app/Contents/Developer
    
    - name: Build
      run: |
        xcodebuild \
          -workspace MyApp.xcworkspace \
          -scheme MyApp \
          -destination 'platform=iOS Simulator,name=iPhone 15' \
          -derivedDataPath build \
          build
    
    - name: Test
      run: |
        xcodebuild \
          -workspace MyApp.xcworkspace \
          -scheme MyApp \
          -destination 'platform=iOS Simulator,name=iPhone 15' \
          -derivedDataPath build \
          -resultBundlePath TestResults.xcresult \
          -enableCodeCoverage YES \
          test
    
    - name: Archive
      if: github.ref == 'refs/heads/main'
      run: |
        xcodebuild \
          -workspace MyApp.xcworkspace \
          -scheme MyApp \
          -configuration Release \
          -destination 'generic/platform=iOS' \
          -archivePath MyApp.xcarchive \
          -allowProvisioningUpdates \
          archive
```

### Common CI patterns
```bash
# Set Xcode version
export DEVELOPER_DIR=/Applications/Xcode_15.0.app/Contents/Developer

# Clean derived data
rm -rf ~/Library/Developer/Xcode/DerivedData

# Build with timeout
timeout 1800 xcodebuild [...]

# Retry on failure
for i in {1..3}; do xcodebuild [...] && break || sleep 30; done
```

## Performance Optimization

### Build performance
```bash
# Use custom derived data path
-derivedDataPath ./build/DerivedData

# Enable parallel building
-jobs $(sysctl -n hw.ncpu)

# Skip unnecessary steps
-skipUnavailableActions

# Use build cache
COMPILER_INDEX_STORE_ENABLE=NO
```

### Reducing build times
```bash
# Only build active architecture for debug
ONLY_ACTIVE_ARCH=YES

# Disable index-while-building
COMPILER_INDEX_STORE_ENABLE=NO

# Use legacy build system (if needed)
-UseModernBuildSystem=0
```

## Troubleshooting

### Common issues and solutions
```bash
# Clean everything
xcodebuild clean
rm -rf ~/Library/Developer/Xcode/DerivedData
rm -rf build

# Reset simulator
xcrun simctl shutdown all
xcrun simctl erase all

# Fix provisioning issues
xcodebuild -allowProvisioningUpdates

# Debug build settings
xcodebuild -showBuildSettings -scheme MyApp | grep SETTING_NAME

# Verbose output
xcodebuild -verbose

# Check for scheme issues
xcodebuild -list -workspace MyApp.xcworkspace
```

### Environment debugging
```bash
# Check Xcode version
xcodebuild -version

# Check available SDKs
xcodebuild -showsdks

# Check command line tools
xcode-select -p

# Switch Xcode versions
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
```

## Advanced Features

### Custom build phases
```bash
# Run script build phase
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  RUN_CLANG_STATIC_ANALYZER=YES \
  build
```

### Build configuration files
```bash
# Use xcconfig file
xcodebuild \
  -workspace MyApp.xcworkspace \
  -scheme MyApp \
  -xcconfig MyConfig.xcconfig \
  build
```

### Multi-platform builds
```bash
# Build for all platforms
for platform in iOS macOS watchOS tvOS; do
  xcodebuild \
    -workspace MyApp.xcworkspace \
    -scheme MyApp-$platform \
    -destination "generic/platform=$platform" \
    archive
done
```

This comprehensive guide covers most xcodebuild scenarios. For the latest options and features, run `xcodebuild -help` or check the Xcode documentation.
