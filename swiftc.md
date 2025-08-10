# swiftc (Swift Compiler) - Detailed Guide

> 📁 **Try it:** See `swiftc-examples/` for working examples

The Swift compiler (`swiftc`) compiles Swift source files directly into executables, libraries, or other artifacts. It's lower-level than Swift Package Manager but gives you fine-grained control.

## Basics

### Simple compilation
```bash
# Compile one file to an executable
swiftc hello.swift -o hello
./hello

# Run directly without creating binary
swift hello.swift

# Compile multiple files with a module name (see swiftc-examples/Source/)
swiftc *.swift -o MyApp
```

### Debug vs Release builds
```bash
# Debug build (default): debug info, no optimizations
swiftc Sources/*.swift -g -Onone -o MyAppDebug

# Release build: optimized, smaller binary
swiftc Sources/*.swift -O -whole-module-optimization -o MyAppRel

# Size-optimized build
swiftc Sources/*.swift -Osize -o MyAppSmall
```

### Warnings and language mode
```bash
# Treat warnings as errors
swiftc Sources/*.swift -warnings-as-errors -swift-version 5

# Suppress warnings
swiftc Sources/*.swift -suppress-warnings

# Enable additional warnings
swiftc Sources/*.swift -warn-swift3-objc-inference-complete
```

## Linking and Dependencies

### Search paths
```bash
# Add include, framework, and library search paths
swiftc main.swift \
  -I /path/to/headers \
  -F /path/to/frameworks \
  -L /path/to/libraries \
  -lMyCLib \
  -framework MyFramework \
  -o app
```
Note: The `-l` flag doesn't require a space between the flag and the library name - this is a standard convention in Unix/Linux linkers. This is Equivalent to `-l MyCLib`

Example:
```bash
# Library compilation
swiftc LibExample.swift \
  -emit-library \
  -emit-module \
  -module-name MyLib \
  -o libMyLib.dylib

# Compile main.swift and link with your library
swiftc main.swift -I lib -L lib -l MyAppLibName -o app
  -module-name MyLib \
  -o libMyLib.dylib
```


### System frameworks
```bash
# Link with Foundation and other system frameworks
swiftc main.swift -framework Foundation -framework CoreFoundation -o app
# Link with UIKit (iOS)
swiftc App/*.swift \
  -target arm64-apple-ios17.0 \
  -sdk "$(xcrun --show-sdk-path --sdk iphoneos)" \
  -framework UIKit \
  -o MyiOSApp
```
Note: Swift on macOS automatically links Foundation by default, but iOS frameworks like UIKit require explicit linking.

## Output Artifacts

### Executables
```bash
# Default: create executable
swiftc Sources/*.swift -module-name MyApp -o MyApp
```

### Dynamic libraries
```bash
# Create .dylib (macOS)
swiftc Sources/*.swift -emit-library -module-name MyLib -o libMyLib.dylib

# Create .so (Linux)
swiftc Sources/*.swift -emit-library -module-name MyLib -o libMyLib.so
```

### Swift modules
```bash
# Emit Swift module interface
swiftc Sources/*.swift \
  -emit-module \
  -emit-module-path build/MyLib.swiftmodule \
  -module-name MyLib

# Emit module with documentation
swiftc Sources/*.swift \
  -emit-module \
  -emit-module-path build/MyLib.swiftmodule \
  -emit-module-doc-path build/MyLib.swiftdoc \
  -module-name MyLib
```

### Objective-C interop
```bash
# Generate Objective-C header for Swift code
swiftc Sources/*.swift \
  -emit-objc-header \
  -emit-objc-header-path build/MyLib-Swift.h \
  -module-name MyLib

# Import Objective-C code
swiftc main.swift \
  -import-objc-header MyObjCBridge.h \
  -o app
```

## Platform Targeting

### macOS
```bash
# Intel Mac
swiftc main.swift \
  -target x86_64-apple-macosx13.0 \
  -sdk "$(xcrun --show-sdk-path --sdk macosx)" \
  -o app

# Apple Silicon Mac
swiftc main.swift \
  -target arm64-apple-macosx13.0 \
  -sdk "$(xcrun --show-sdk-path --sdk macosx)" \
  -o app

# Universal binary (both architectures)
swiftc main.swift \
  -target x86_64-apple-macosx13.0 \
  -sdk "$(xcrun --show-sdk-path --sdk macosx)" \
  -o app-x86_64

swiftc main.swift \
  -target arm64-apple-macosx13.0 \
  -sdk "$(xcrun --show-sdk-path --sdk macosx)" \
  -o app-arm64

lipo -create app-x86_64 app-arm64 -output app-universal
```

### iOS
```bash
# iOS Simulator (Apple Silicon Macs use arm64 simulators)
swiftc App/*.swift \
  -target arm64-apple-ios17.0-simulator \
  -sdk "$(xcrun --show-sdk-path --sdk iphonesimulator)" \
  -o AppSim

# iOS Simulator (Intel Macs)
swiftc App/*.swift \
  -target x86_64-apple-ios17.0-simulator \
  -sdk "$(xcrun --show-sdk-path --sdk iphonesimulator)" \
  -o AppSim

# iOS Device
swiftc App/*.swift \
  -target arm64-apple-ios17.0 \
  -sdk "$(xcrun --show-sdk-path --sdk iphoneos)" \
  -o AppDevice
```

### watchOS and tvOS
```bash
# watchOS Device
swiftc Sources/*.swift \
  -target arm64_32-apple-watchos10.0 \
  -sdk "$(xcrun --show-sdk-path --sdk watchos)" \
  -o WatchApp

# tvOS Device
swiftc Sources/*.swift \
  -target arm64-apple-tvos17.0 \
  -sdk "$(xcrun --show-sdk-path --sdk appletvos)" \
  -o TVApp
```

## Testing Support

### Enable testability
```bash
# Enable @testable imports when compiling sources under test
swiftc Sources/*.swift -enable-testing -o MyTestableBuild

# Compile test files with access to internal APIs
swiftc Tests/*.swift Sources/*.swift -enable-testing -o TestRunner
```

### XCTest integration
```bash
# Compile with XCTest framework
swiftc Tests/*.swift Sources/*.swift \
  -framework XCTest \
  -enable-testing \
  -o TestRunner
```

## Advanced Features

### Conditional compilation
```bash
# Define compilation flags
swiftc Sources/*.swift -DDEBUG -DFEATURE_FLAG -o MyApp

# In Swift code:
# #if DEBUG
#   print("Debug mode")
# #endif
# 
# #if FEATURE_FLAG
#   // Feature code
# #endif
```

### Resource bundles
```bash
# Embed resources (requires resource bundle setup)
swiftc Sources/*.swift \
  -resource-dir Resources \
  -o MyApp
```

### Static libraries
```bash
# Create static library (.a file)
swiftc Sources/*.swift \
  -emit-library -static \
  -module-name MyStaticLib \
  -o libMyStaticLib.a
```

### LLVM IR and Assembly
```bash
# Emit LLVM IR
swiftc main.swift -emit-ir -o main.ll

# Emit assembly
swiftc main.swift -emit-assembly -o main.s

# Emit SIL (Swift Intermediate Language)
swiftc main.swift -emit-sil -o main.sil
```

## Performance and Optimization

### Optimization levels
```bash
# No optimization (fastest compile)
swiftc Sources/*.swift -Onone -o MyApp

# Speed optimization (default -O)
swiftc Sources/*.swift -O -o MyApp

# Size optimization
swiftc Sources/*.swift -Osize -o MyApp

# Unsafe optimizations (use with caution)
swiftc Sources/*.swift -Ounchecked -o MyApp
```

### Whole module optimization
```bash
# Enable whole-module optimization for better performance
swiftc Sources/*.swift -O -whole-module-optimization -o MyApp

# Equivalent short form
swiftc Sources/*.swift -O -wmo -o MyApp
```

### Link-time optimization
```bash
# Enable link-time optimization
swiftc Sources/*.swift -O -lto=llvm-full -o MyApp
```

## Debugging and Profiling

### Debug information
```bash
# Include debug info
swiftc Sources/*.swift -g -o MyApp

# Debug info with DWARF
swiftc Sources/*.swift -g -debug-info-format=dwarf -o MyApp
```

### Sanitizers
```bash
# Address sanitizer
swiftc Sources/*.swift -sanitize=address -o MyApp

# Thread sanitizer
swiftc Sources/*.swift -sanitize=thread -o MyApp

# Undefined behavior sanitizer
swiftc Sources/*.swift -sanitize=undefined -o MyApp
```

### Profiling
```bash
# Profile-guided optimization (requires profile data)
swiftc Sources/*.swift -profile-use=profile.profdata -O -o MyApp

# Generate profile data
swiftc Sources/*.swift -profile-generate -o MyApp
```

## Cross-Platform Compilation

### Linux
```bash
# Compile for Linux (from macOS with Swift for Linux toolchain)
swiftc -target x86_64-unknown-linux-gnu Sources/*.swift -o MyLinuxApp
```

### Windows
```bash
# Compile for Windows (requires Swift for Windows)
swiftc -target x86_64-unknown-windows-msvc Sources/*.swift -o MyWindowsApp.exe
```

## Common Patterns

### Library with public interface
```bash
# Compile library with specific access levels
swiftc Sources/*.swift \
  -emit-library \
  -emit-module \
  -emit-module-path build/MyLib.swiftmodule \
  -module-name MyLib \
  -o build/libMyLib.dylib

# Use the library
swiftc main.swift \
  -I build \
  -L build \
  -lMyLib \
  -o MyApp
```

### Mixed Swift/Objective-C project
```bash
# Compile Objective-C files first
clang -c ObjCFile.m -o ObjCFile.o

# Compile Swift with Objective-C
swiftc SwiftFile.swift ObjCFile.o \
  -import-objc-header BridgingHeader.h \
  -framework Foundation \
  -o MixedApp
```

### Framework development
```bash
# Create framework structure
mkdir -p MyFramework.framework/Headers
mkdir -p MyFramework.framework/Modules

# Compile framework
swiftc Sources/*.swift \
  -emit-library \
  -emit-module \
  -emit-module-path MyFramework.framework/Modules/MyFramework.swiftmodule \
  -module-name MyFramework \
  -o MyFramework.framework/MyFramework

# Create module map
echo 'framework module MyFramework { umbrella header "MyFramework.h" export * module * { export * } }' > MyFramework.framework/Modules/module.modulemap
```

## Troubleshooting

### Common errors and solutions
```bash
# "No such module" error
# Solution: Add -I path/to/modules or check module search paths

# "Undefined symbols" error
# Solution: Add missing -l flags for libraries or -framework for frameworks

# "Architecture mismatch" error
# Solution: Ensure all object files use the same -target

# "SDK not found" error
# Solution: Check SDK path with xcrun --show-sdk-path --sdk <sdk-name>
```

### Verbose output
```bash
# Show all compilation steps
swiftc -v Sources/*.swift -o MyApp

# Show search paths
swiftc -print-search-paths

# Show target info
swiftc -print-target-info
```

## Integration with Build Systems

### Makefiles
```makefile
SWIFTC = swiftc
SWIFT_FLAGS = -O -whole-module-optimization
SOURCES = $(wildcard Sources/*.swift)

MyApp: $(SOURCES)
	$(SWIFTC) $(SWIFT_FLAGS) $(SOURCES) -o $@

clean:
	rm -f MyApp

.PHONY: clean
```

### CMake
```cmake
find_package(Swift REQUIRED)

add_executable(MyApp
  Sources/main.swift
  Sources/other.swift
)

set_target_properties(MyApp PROPERTIES
  Swift_MODULE_NAME MyApp
  Swift_COMPILATION_MODE wholemodule
)
```

This detailed guide covers most `swiftc` use cases. For even more advanced usage, consult `swiftc -help` and the Swift compiler documentation.
