# 🚀 Swift & iOS CLI Build Cheat Sheet

[![Swift](https://img.shields.io/badge/Swift-6.1.2-orange.svg)](https://swift.org)
[![Xcode](https://img.shields.io/badge/Xcode-16.4-blue.svg)](https://developer.apple.com/xcode/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Stars](https://img.shields.io/github/stars/banghuazhao/Swift-iOS-CLI-Build-Cheat-Sheet?style=social)](https://github.com/banghuazhao/Swift-iOS-CLI-Build-Cheat-Sheet)

> **Master Swift development from the command line** 🎯

A comprehensive, hands-on guide to building Swift applications using command-line tools. Perfect for developers who want to automate builds, set up CI/CD pipelines, or understand Swift's build system beyond Xcode's GUI.

## ✨ What You'll Learn

- **swiftc**: Direct Swift compilation with fine-grained control
- **Swift Package Manager**: Modern dependency management and building
- **xcodebuild**: Professional iOS app building and distribution
- **xcrun**: Cross-platform compilation and simulator management

## 🎯 Quick Start

### Prerequisites

```bash
# Install Xcode Command Line Tools
xcode-select --install

# Verify installation
swift --version
xcodebuild -version
```

### Your First Swift Command

```bash
# Create a simple Swift file
echo 'print("Hello, Swift CLI!")' > hello.swift

# Compile and run
swiftc hello.swift -o hello && ./hello
```

**🎉 You just built your first Swift app from the command line!**

## 📚 Detailed Guides

| Tool | Purpose | Guide | Examples |
|------|---------|-------|----------|
| **swiftc** | Direct Swift compilation | [📖 Full Guide](swiftc.md) | [📁 Examples](swiftc-examples/) |
| **Swift Package Manager** | Modern build system | [📖 Full Guide](spm.md) | [📁 Examples](spm-examples/) |
| **xcodebuild** | iOS app building | [📖 Full Guide](xcodebuild.md) | [📁 Examples](xcodebuild-examples/) |
| **xcrun** | Cross-platform tools | [📖 Full Guide](xcrun.md) | [📁 Examples](xcrun-examples/) |

## 🛠️ Essential Commands

### Swift Compiler (swiftc)

```bash
# Single file compilation
swiftc hello.swift -o hello && ./hello

# Multi-file compilation
swiftc *.swift -o MyApp

# Library compilation
swiftc LibExample.swift \
  -emit-library \
  -emit-module \
  -module-name MyLib \
  -o libMyLib.dylib
```

### Swift Package Manager

```bash
# Create new project
swift package init --type executable --name MyCLITool

# Build and run
swift build                    # Debug build
swift build -c release         # Release build
swift run MyCLITool            # Run with arguments
swift test                     # Run tests
```

### Xcode Build System

```bash
# List project schemes
xcodebuild -list -project MyApp.xcodeproj

# Build for iOS Simulator
xcodebuild -project MyApp.xcodeproj -scheme MyApp \
  -destination 'platform=iOS Simulator,name=iPhone 16' \
  build

# Archive for distribution
xcodebuild -project MyApp.xcodeproj -scheme MyApp \
  -destination 'generic/platform=iOS' \
  -archivePath MyApp.xcarchive \
  archive
```

### Cross-Platform Tools

```bash
# Find SDK paths
xcodebuild -showsdks

# Compile for iOS Simulator
xcrun --sdk iphonesimulator swiftc main.swift \
  -target arm64-apple-ios18.5-simulator -o app

# Simulator management
xcrun simctl list devices
xcrun simctl boot "iPhone 16"
```

## 🎓 Learning Path

### Beginner → Advanced

1. **Start with swiftc** - Learn basic compilation
2. **Move to SPM** - Understand modern Swift development
3. **Master xcodebuild** - Build professional iOS apps
4. **Explore xcrun** - Cross-platform and automation

### Use Cases

- **🔄 CI/CD Pipelines**: Automate your build process
- **📱 iOS Development**: Build and distribute apps
- **🔧 CLI Tools**: Create command-line applications
- **📦 Libraries**: Build and distribute Swift packages
- **🤖 Automation**: Script your development workflow

## 🚀 Advanced Features

### Multi-Platform Building

```bash
# Build for multiple platforms
swift build --triple x86_64-apple-macosx14.0
swift build --triple arm64-apple-macosx14.0
```

### iOS App Distribution

```bash
# Archive and export
xcodebuild -exportArchive \
  -archivePath MyApp.xcarchive \
  -exportPath ./ipa \
  -exportOptionsPlist ExportOptions.plist
```

### Simulator Automation

```bash
# Install and launch app
xcrun simctl install booted ./MyApp.ipa
xcrun simctl launch booted com.example.MyApp
```

## 🤝 Contributing

Found an error or want to add more examples? Contributions are welcome!

1. Fork the repository
2. Create a feature branch
3. Add your improvements
4. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Apple for the amazing Swift toolchain
- The Swift community for continuous improvements
- All contributors who help make this guide better

---

**⭐ Star this repository if it helped you master Swift CLI development!**

[GitHub Repository](https://github.com/banghuazhao/Swift-iOS-CLI-Build-Cheat-Sheet)