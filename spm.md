# Swift Package Manager (SPM) - Detailed Guide

> 📁 **Try it:** See `spm-examples/` for working examples

Swift Package Manager is the official build system and dependency manager for Swift. It handles compilation, testing, and dependency resolution automatically.

## Getting Started

### Package initialization
```bash
# Create a new library package
swift package init --type library

# Create a new executable package
swift package init --type executable

# Create in a specific directory
mkdir MyPackage && cd MyPackage
swift package init --name MyPackage --type library

# Create with custom name
swift package init --name MyCustomName --type executable
```

### Package structure
```
MyPackage/
├── Package.swift          # Package manifest
├── Sources/
│   └── MyPackage/
│       └── MyPackage.swift
├── Tests/
│   └── MyPackageTests/
│       └── MyPackageTests.swift
└── README.md
```

### Package.swift basics
```swift
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "MyPackage",
    platforms: [
        .macOS(.v13),
        .iOS(.v16),
        .watchOS(.v9),
        .tvOS(.v16)
    ],
    products: [
        .library(name: "MyPackage", targets: ["MyPackage"]),
        .executable(name: "MyTool", targets: ["MyTool"])
    ],
    dependencies: [
        .package(url: "https://github.com/apple/swift-argument-parser", from: "1.0.0")
    ],
    targets: [
        .target(
            name: "MyPackage",
            dependencies: []
        ),
        .executableTarget(
            name: "MyTool",
            dependencies: [
                .product(name: "ArgumentParser", package: "swift-argument-parser")
            ]
        ),
        .testTarget(
            name: "MyPackageTests",
            dependencies: ["MyPackage"]
        )
    ]
)
```

## Building

### Basic building
```bash
# Build debug (default)
swift build

# Build release
swift build -c release
swift build --configuration release

# Build specific target
swift build --target MyTarget

# Build with verbose output
swift build --verbose

# Build for specific platform
swift build --triple x86_64-apple-macosx14.0
```

### Build paths and artifacts
```bash
# Show build path
swift build --show-bin-path

# Custom build path
swift build --build-path .build-custom

# Find built products
ls $(swift build --show-bin-path)
```

### Advanced building
```bash
# Parallel builds (default: number of cores)
swift build --jobs 4

# Skip building dependencies
swift build --skip-dependencies

# Build with sanitizers
swift build --sanitize=address
swift build --sanitize=thread

# Static linking
swift build --static-swift-stdlib
```

## Running Executables

### Basic execution
```bash
# Run default executable
swift run

# Run specific executable
swift run MyTool

# Pass arguments to executable
swift run MyTool -- --help --verbose arg1 arg2

# Run without building (if already built)
swift run --skip-build MyTool
```

### Development workflow
```bash
# Run in debug mode with full output
swift run --configuration debug MyTool -- --debug

# Run release build
swift run -c release MyTool

# Run with environment variables
MYVAR=value swift run MyTool
```

## Testing

### Basic testing
```bash
# Run all tests
swift test

# Run tests with verbose output
swift test --verbose

# Run specific test
swift test --filter MyTestSuite
swift test --filter MyTestSuite/testSpecificMethod

# List all tests
swift test --list-tests
```

### Advanced testing
```bash
# Parallel test execution
swift test --parallel

# Generate code coverage
swift test --enable-code-coverage

# Test specific configuration
swift test --configuration release

# Test with sanitizers
swift test --sanitize=address

# Skip specific tests
swift test --skip-tests MySlowTests
```

### Test filtering patterns
```bash
# Run tests matching pattern
swift test --filter ".*Network.*"

# Run specific test class
swift test --filter "MyPackageTests.NetworkTests"

# Run specific test method
swift test --filter "MyPackageTests.NetworkTests/testHTTPRequest"

# Multiple filters
swift test --filter "Test1|Test2"
```

## Dependencies

### Managing dependencies
```bash
# Resolve dependencies (creates Package.resolved)
swift package resolve

# Update to latest allowed versions
swift package update

# Update specific package
swift package update MyDependency

# Show dependency graph
swift package show-dependencies
swift package show-dependencies --format tree
swift package show-dependencies --format dot
swift package show-dependencies --format json
```

### Dependency editing
```bash
# Edit dependency locally
swift package edit MyDependency
swift package edit MyDependency --branch main
swift package edit MyDependency --path ../local-copy

# Stop editing dependency
swift package unedit MyDependency

# Reset all local edits
swift package unedit --force
```

### Cleaning
```bash
# Clean build artifacts
swift package clean

# Reset dependencies and build artifacts
swift package reset

# Purge resolved dependencies
swift package purge-cache
```

## Package Information

### Inspection
```bash
# Describe package
swift package describe
swift package describe --type json

# Show package configuration
swift package dump-package

# Validate package
swift package validate

# Generate Xcode project (deprecated but still works)
swift package generate-xcodeproj
```

### Tools version
```bash
# Show tools version
swift package tools-version

# Set tools version
swift package tools-version --set 5.9
swift package tools-version --set-current
```

## Cross-Platform Development

### Platform-specific building
```bash
# Build for specific architecture
swift build --arch arm64
swift build --arch x86_64

# Cross-compile for different platforms
swift build --triple arm64-apple-ios16.0
swift build --triple x86_64-unknown-linux-gnu
swift build --triple x86_64-pc-windows-msvc
```

### Destination files
```bash
# Create destination file for cross-compilation
cat > ios-destination.json << 'EOF'
{
  "version": 1,
  "target": {
    "triple": "arm64-apple-ios16.0"
  },
  "sdk": "/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk",
  "toolchain-bin-dir": "/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin"
}
EOF

# Use destination file
swift build --destination ios-destination.json
```

## Compiler and Linker Flags

### Swift compiler flags
```bash
# Pass flags to Swift compiler
swift build -Xswiftc -warn-swift3-objc-inference-complete
swift build -Xswiftc -DDEBUG
swift build -Xswiftc -enable-testing

# Multiple flags
swift build -Xswiftc -DDEBUG -Xswiftc -DFEATURE_FLAG
```

### C/C++/Objective-C flags
```bash
# Pass flags to C compiler
swift build -Xcc -DMACRO_NAME=1
swift build -Xcc -I/usr/local/include

# Pass flags to C++ compiler
swift build -Xcxx -std=c++17

# Pass flags to linker
swift build -Xlinker -dead_strip
swift build -Xlinker -L/usr/local/lib
```

## Package Types and Products

### Library types
```swift
// Static library
.library(name: "MyStaticLib", type: .static, targets: ["MyTarget"])

// Dynamic library
.library(name: "MyDynamicLib", type: .dynamic, targets: ["MyTarget"])

// Automatic (default)
.library(name: "MyLib", targets: ["MyTarget"])
```

### Executable products
```swift
.executable(name: "MyTool", targets: ["MyTool"])
```

### Plugin products
```swift
.plugin(name: "MyPlugin", targets: ["MyPlugin"])
```

## Target Types

### Regular targets
```swift
.target(
    name: "MyLibrary",
    dependencies: [],
    path: "Sources/MyLibrary",
    sources: ["File1.swift", "File2.swift"], // Optional: specify files
    resources: [.process("Resources")],       // Include resources
    publicHeadersPath: "include",             // For C/C++ targets
    cSettings: [.define("MACRO_NAME")],       // C compiler settings
    cxxSettings: [.headerSearchPath(".")],   // C++ compiler settings
    swiftSettings: [.define("FEATURE_FLAG")]  // Swift compiler settings
)
```

### Executable targets
```swift
.executableTarget(
    name: "MyTool",
    dependencies: [
        "MyLibrary",
        .product(name: "ArgumentParser", package: "swift-argument-parser")
    ]
)
```

### Test targets
```swift
.testTarget(
    name: "MyLibraryTests",
    dependencies: ["MyLibrary"],
    resources: [.copy("TestData")]
)
```

### System library targets
```swift
.systemLibrary(
    name: "CLibrary",
    pkgConfig: "libxml-2.0",
    providers: [.brew(["libxml2"]), .apt(["libxml2-dev"])]
)
```

### Plugin targets
```swift
.plugin(
    name: "MyBuildPlugin",
    capability: .buildTool(),
    dependencies: []
)
```

## Resources

### Including resources
```swift
.target(
    name: "MyTarget",
    resources: [
        .process("Images"),           // Process and optimize
        .copy("Data"),               // Copy as-is
        .copy("config.json"),        // Copy specific file
        .process("Localization",     // Process .lproj directories
                 localization: .default)
    ]
)
```

### Accessing resources in code
```swift
import Foundation

// Access bundle resources
guard let resourceURL = Bundle.module.url(forResource: "config", withExtension: "json") else {
    fatalError("Could not find config.json")
}

// Read resource data
let data = try Data(contentsOf: resourceURL)
```

## Advanced Features

### Conditional dependencies
```swift
dependencies: [
    .package(url: "https://github.com/apple/swift-log", from: "1.0.0"),
    // Only include on Apple platforms
    .package(url: "https://github.com/apple/swift-system", from: "1.0.0"),
],
targets: [
    .target(
        name: "MyTarget",
        dependencies: [
            .product(name: "Logging", package: "swift-log"),
            .product(name: "SystemPackage", 
                    package: "swift-system", 
                    condition: .when(platforms: [.macOS, .iOS, .watchOS, .tvOS]))
        ]
    )
]
```

### Custom build settings
```swift
.target(
    name: "MyTarget",
    swiftSettings: [
        .define("DEBUG", .when(configuration: .debug)),
        .define("RELEASE", .when(configuration: .release)),
        .unsafeFlags(["-Xfrontend", "-warn-long-function-bodies=100"])
    ]
)
```

## CI/CD Integration

### GitHub Actions
```yaml
name: Swift

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - uses: swift-actions/setup-swift@v1
      with:
        swift-version: "5.9"
    - name: Build
      run: swift build --verbose
    - name: Run tests
      run: swift test --verbose --enable-code-coverage
```

### Common CI commands
```bash
# Resolve dependencies with timeout
timeout 300 swift package resolve

# Build with maximum warnings
swift build -Xswiftc -warnings-as-errors

# Test with JUnit output (requires additional setup)
swift test --enable-code-coverage 2>&1 | tee test-results.txt

# Generate documentation (with SwiftDocC)
swift package generate-documentation
```

## Troubleshooting

### Common issues
```bash
# Clear all caches
swift package clean
swift package reset
rm -rf .build

# Update all dependencies
swift package update

# Resolve dependency conflicts
swift package resolve --verbose

# Check package validity
swift package validate

# Debug build issues
swift build --verbose

# Check tools version compatibility
swift package tools-version
```

### Performance optimization
```bash
# Use build cache
swift build --cache-path .build-cache

# Parallel builds
swift build --jobs $(nproc)

# Skip unnecessary rebuilds
swift build --skip-dependencies
```

### Memory issues
```bash
# Limit parallel jobs for memory-constrained environments
swift build --jobs 1

# Use release build to reduce memory usage
swift build -c release
```

## Plugin Development

### Build tool plugins
```swift
import PackagePlugin

@main
struct MyBuildPlugin: BuildToolPlugin {
    func createBuildCommands(context: PluginContext, target: Target) async throws -> [Command] {
        return [
            .buildCommand(
                displayName: "Generating code",
                executable: try context.tool(named: "my-generator").path,
                arguments: [target.directory.string],
                outputFiles: [context.pluginWorkDirectory.appending("Generated.swift")]
            )
        ]
    }
}
```

### Command plugins
```swift
import PackagePlugin

@main
struct MyCommandPlugin: CommandPlugin {
    func performCommand(context: PluginContext, arguments: [String]) async throws {
        print("Running custom command with args: \(arguments)")
    }
}
```

This comprehensive guide covers most SPM use cases. For the latest features and updates, check the [Swift Package Manager documentation](https://swift.org/package-manager/).
