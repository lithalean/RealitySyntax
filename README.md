# 📝 RealitySyntax

*Multi-Language Code Editor for Apple Silicon*

![Platform](https://img.shields.io/badge/platform-macOS%2026%2B-blue)
![Architecture](https://img.shields.io/badge/arch-ARM64%20ONLY-green)
![Swift Version](https://img.shields.io/badge/swift-6.0%2B-orange)
![Xcode](https://img.shields.io/badge/Xcode-26%2B-red)

**RealitySyntax** is the code editor component of Orchard—a powerful multi-language development environment built exclusively for Apple Silicon and the latest Apple technologies.

---

## ✨ Features

- **Multi-Language Support**: Swift, C++, and Metal Shading Language (MSL)
- **Apple Silicon Optimized**: Built exclusively for M1/M2/M3/M4 processors
- **Cross-Platform**: Native experiences on macOS and iOS
- **TreeSitter Integration**: Advanced syntax highlighting and parsing
- **Live Code Analysis**: Real-time error detection and suggestions
- **Adaptive UI**: Responsive layouts for different screen sizes

## 🎯 Language Support

### Swift
- Full Swift 6 syntax highlighting
- Godot-Swift integration
- Live parsing and validation
- Export property detection

### C++
- Modern C++ syntax support
- Godot-CPP bindings
- Build system integration
- Header/implementation awareness

### Metal Shading Language (MSL)
- Metal 3 shader support
- Live preview capabilities
- Vertex/fragment shader templates
- Apple GPU optimizations

---

## 🏗️ Architecture

```
RealitySyntax/
├── Models/                    # Core data models
│   ├── ScriptFile.swift      # Script file representation
│   └── ScriptManager.swift   # Script management & operations
├── Editors/                  # Language-specific editors
│   ├── SwiftScriptEditor.swift
│   ├── CppScriptEditor.swift
│   └── MSLEditor.swift       # Coming soon
├── Views/                    # Platform-adaptive layouts
│   ├── MacView.swift         # macOS split-view interface
│   └── iPhoneView.swift      # iOS overlay interface
└── Shared/                   # Reusable components
    ├── SharedInspectorView.swift
    ├── SharedOutlinerView.swift
    └── SharedPropertiesView.swift
```

**Key Components:**
- **Script Manager**: File operations, templates, and project management
- **Adaptive Editors**: Platform-optimized editing experiences
- **Inspector System**: Live properties and statistics
- **Outliner**: Hierarchical script organization

---

## 🚧 Development Status

### ✅ Phase 1: Foundation (Complete)
- Multi-language editor framework
- Cross-platform adaptive layouts
- Script file management system
- TreeSitter integration foundation

### 🚀 Phase 2: Core Features (In Progress)
- Swift and C++ syntax highlighting
- Live code analysis and validation
- Template system for common patterns
- Build and run integration

### 🔮 Phase 3: Advanced Features (Planned)
- MSL editor with live preview
- Advanced debugging integration
- Code completion and IntelliSense
- Git integration and version control

---

## 💻 Getting Started

### Basic Usage
- **Create Scripts**: Use the + button to create new scripts
- **Switch Languages**: Choose from Swift, C++, or MSL templates
- **Live Editing**: Real-time syntax highlighting and validation
- **Organize**: Use the outliner to manage multiple scripts
- **Properties**: View script statistics and metadata

---

## 🎮 Controls

### macOS
- **Left Panel**: Script outliner and properties inspector
- **Right Panel**: Main code editing area
- **Toolbar**: Script creation and view controls
- **Shortcuts**: Standard Mac text editing conventions

### iOS
- **Sliding Inspector**: Swipe from left edge to access outliner
- **Touch Editing**: Optimized text selection and editing
- **Toolbar**: Bottom navigation with script controls
- **Gestures**: Intuitive touch-based interactions

---

## 🔧 Part of Orchard

RealitySyntax is the code editing component of **Orchard**—a complete Apple-native game development ecosystem.

**Related Components:**
- **RealityViewport**: 3D scene editor and manipulation

---

## 🎨 Language-Specific Features

### Swift Development
- **Export Properties**: Automatic detection of `@Export` variables
- **Live Validation**: Real-time syntax and type checking
- **Smart Templates**: Common game programming patterns

### C++ Development
- **Header Management**: Automatic include detection
- **Godot-CPP Bindings**: Full GDNative integration
- **Build Integration**: Seamless compilation workflow
- **Memory Management**: Smart pointer and RAII patterns

### Metal Shaders (Coming Soon)
- **Vertex/Fragment**: Complete shader pipeline support
- **Live Preview**: Real-time rendering feedback
- **Apple GPU**: M-series optimization hints
- **Material Integration**: Direct connection to rendering system

---

## 📋 Requirements

| Component | Version |
|-----------|---------|
| macOS     |   26+   |
| iOS       |   26+   |
| Xcode     |   26+   |
| Swift     |   6.0+  |


---

## ✅ Runtime Architecture Updates (2025-07)

- Tree-sitter runtime and grammar modules are now loaded via `dlsym()` at runtime
- Removed all use of Objective-C bridging headers (`RealitySyntax-Bridging-Header.h`) and `.m` files
- `TreeSitterRuntime.xcframework`, `TreeSitterCpp.xcframework`, and `TreeSitterSwift.xcframework` are now optional
- Graceful fallback behavior if runtime or grammar modules are not present
- `TreeSitterStatusView` shows real-time runtime and language availability

### Example Usage

```swift
if dlsym(dlopen(nil, RTLD_LAZY), "tree_sitter_cpp") != nil {
    cppStatus = .loaded
} else {
    cppStatus = .notLoaded
}
```

These changes make the editor modular and sandbox-safe for dynamic Tree-sitter grammar loading.

---

*Built with ❤️ for the Apple ecosystem as part of the Orchard game engine*

---

