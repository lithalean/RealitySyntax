# 📝 RealitySyntax

*Native Multi-Language Code Editor for Apple Game Development*

![Platform](https://img.shields.io/badge/platform-macOS%20%7C%20iOS-blue)
![Architecture](https://img.shields.io/badge/arch-Apple%20Silicon-green)
![Swift Version](https://img.shields.io/badge/swift-6.0-orange)
![Status](https://img.shields.io/badge/status-In%20Development-yellow)

**RealitySyntax** is a modern code editor built exclusively for Apple platforms, designed as the primary code editing interface for the Orchard game engine. It provides native SwiftUI experiences on both macOS and iOS with advanced syntax highlighting powered by Tree-sitter.

---

## ✨ Key Features

### 🚀 Native Apple Experience
- Built with **SwiftUI** for seamless macOS and iOS support
- Optimized for **Apple Silicon** (M1/M2/M3/M4)
- Adaptive layouts that feel at home on any device
- Native performance with no Electron or web views

### 🎨 Multi-Language Support
- **Swift** - Primary game logic with Godot-Swift bindings
- **C++** - Performance-critical code and engine extensions  
- **Metal Shading Language** - Custom shaders and visual effects
- Language-specific syntax themes and templates

### 🔧 Modern Architecture
- **Tree-sitter** integration for accurate syntax highlighting
- Dynamic framework loading - grammars are optional
- No Objective-C dependencies - pure Swift implementation
- Modular design for easy extension

---

## 📱 Platform Experiences

### macOS
<img width="800" alt="macOS Editor Layout" src="https://github.com/user-attachments/assets/placeholder-macos">

- Traditional code editor layout with file tree on left
- Resizable panels and customizable workspace
- Full keyboard shortcuts and productivity features
- Integrated debugging and build tools

### iOS
<img width="400" alt="iOS Editor Layout" src="https://github.com/user-attachments/assets/placeholder-ios">

- Full-screen editing with slide-out inspector
- Touch-optimized text selection and navigation
- Floating toolbar for quick actions
- External keyboard support

---

## 🏗️ Technical Architecture

### Tree-sitter Integration
RealitySyntax uses a modern approach to syntax highlighting:

```swift
// Runtime detection of Tree-sitter availability
if dlsym(dlopen(nil, RTLD_LAZY), "tree_sitter_swift") != nil {
    // Enable syntax highlighting
    swiftStatus = .loaded
}
```

- **No compile-time dependencies** - Tree-sitter is completely optional
- **Dynamic loading** - Grammar frameworks loaded at runtime
- **Graceful fallback** - Editor works without syntax highlighting
- **Visual indicators** - Shows which languages are available

### Component Structure
```
├── Editors/          # Language-specific editors
├── Inspector/        # File browser and properties
├── Core/            # Script management
└── Views/           # Platform-adaptive layouts
```

---

## 🎯 Part of Orchard

RealitySyntax is one component of the **Orchard** game engine - a complete Apple-native game development ecosystem:

- **RealitySyntax** - Code editing (this project)
- **RealityViewport** - 3D scene manipulation
- **RealityAssets** - Resource management
- **RealityBuild** - Compilation and deployment

---

## 🚧 Development Status

### Currently Working
- ✅ Multi-file script management
- ✅ Platform-adaptive UI (macOS/iOS)
- ✅ Tree-sitter runtime integration
- ✅ Language-specific editor views
- ✅ Real-time status indicators

### In Progress
- 🔄 Tree-sitter grammar loading
- 🔄 Syntax highlighting implementation
- 🔄 Build system integration
- 🔄 File persistence

### Planned Features
- 📋 Code completion and IntelliSense
- 📋 Integrated debugging
- 📋 Git version control
- 📋 Live error detection

---

## 🛠️ Building from Source

*Note: Source code is currently in a private repository during initial development.*

### Requirements
- macOS 15.0+ or iOS 18.0+
- Xcode 16.0+
- Swift 6.0
- Apple Silicon Mac (for development)

### Tree-sitter Frameworks
RealitySyntax uses custom-built XCFrameworks for Tree-sitter support:
- `TreeSitterRuntime.xcframework` - Core parsing engine
- `TreeSitterSwift.xcframework` - Swift grammar
- `TreeSitterCpp.xcframework` - C++ grammar

These are built using the **TreeXC** build system (separate project).

---

## 📚 Documentation

Detailed documentation is being developed alongside the project:
- **Architecture Guide** - Understanding the codebase
- **Language Integration** - Adding new language support
- **Platform Adaptation** - iOS vs macOS differences
- **Tree-sitter Guide** - Grammar integration

---

## 🤝 Contributing

While the repository is currently private, we welcome feedback and suggestions! Feel free to open issues for:
- Feature requests
- Bug reports (once public beta begins)
- Documentation improvements
- Language support requests

---

## 📄 License

RealitySyntax will be released under an open-source license (TBD) once it reaches public beta status.

---

## 🙏 Acknowledgments

- [Tree-sitter](https://tree-sitter.github.io/tree-sitter/) for parsing technology
- [Swift Forums](https://forums.swift.org) community for SwiftUI guidance
- Apple Developer Documentation for platform APIs

---

<p align="center">
  <i>Built with ❤️ for the Apple ecosystem as part of the Orchard game engine</i>
</p>

<p align="center">
  <a href="#-realitysyntax">Back to top ↑</a>
</p>