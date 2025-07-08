# RealitySyntax — AI Context Document

**Date**: January 2025  
**Status**: Active Development - TreeSitter Integration Phase  
**Purpose**: Accurate context for AI systems working with RealitySyntax codebase

---

## 🎯 Project Identity

**RealitySyntax** is a SwiftUI-based multi-language code editor for the Orchard game engine.

**Key Facts:**
- Part of Orchard (not "Reality Game Engine")
- Apple Silicon exclusive (ARM64 only)
- Supports Swift, C++, and Metal Shading Language (MSL)
- Uses TreeSitter for syntax highlighting
- Cross-platform (macOS and iOS)

---

## 🏗️ Current Architecture

### Directory Structure
```
RealitySyntax/
├── App/                      # App entry point
├── Core/
│   └── Managers/
│       └── ScriptManager.swift    # Central script management
├── Editors/
│   ├── SwiftScriptEditor.swift   # Swift editor with TreeSitter integration
│   └── CppScriptEditor.swift     # C++ editor with TreeSitter integration
├── Inspector/
│   ├── InspectorView.swift      # Container for inspector panels
│   ├── OutlinerView.swift       # Script hierarchy/list
│   └── PropertiesView.swift     # Script properties display
├── Sources/
│   └── Shared/
│       ├── Components/
│       │   └── TreeSitterStatusView.swift  # Runtime/language status UI
│       ├── Extensions/
│       ├── PlatformColor.swift
│       └── Types/
│           └── TreeSitterStatusTypes.swift  # Status enums
├── Syntax/
│   ├── ScriptFile.swift         # Script model
│   └── SyntaxView.swift         # Main entry point
└── Views/
    ├── MacView.swift            # macOS layout
    └── iPhoneView.swift         # iOS layout
```

### XCFrameworks (Built with TreeXC)
```
├── TreeSitterRuntime.xcframework    # Core TreeSitter runtime
├── TreeSitterCpp.xcframework       # C++ grammar
└── TreeSitterSwift.xcframework     # Swift grammar
```

---

## 🔧 TreeSitter Integration Status

### Migration Context
**Before**: Used Objective-C bridging headers to integrate TreeSitter
**Now**: Attempting pure Swift integration via `dlsym()` dynamic loading
**Goal**: Modular, optional TreeSitter support without compile-time dependencies

### Current Implementation

#### Runtime Check (WORKING ✅)
```swift
// In SwiftScriptEditor.swift
guard let symbol = dlsym(dlopen(nil, RTLD_LAZY), "ts_parser_new") else {
    runtimeStatus = .notLoaded
    return
}
// This works - runtime symbols are found
```

#### Language Check (NOT WORKING ❌)
```swift
// In SwiftScriptEditor.swift
if dlsym(dlopen(nil, RTLD_LAZY), "tree_sitter_swift") != nil {
    swiftStatus = .loaded  // Never reached - symbol not found
}
```

### The Problem
Language symbols (`tree_sitter_swift`, `tree_sitter_cpp`) are not visible to `dlsym()` even though:
1. XCFrameworks are linked in project
2. Build scripts create proper headers
3. Runtime symbols work fine

### Symptoms
- TreeSitterStatusView shows: Runtime ✅, Swift ❌, C++ ❌
- Parse test creates parser successfully but can't load languages
- No crashes, just nil symbol lookups

---

## 🎨 UI/UX Architecture

### Layout Philosophy
**RealitySyntax uses traditional code editor layout:**
- Inspector/File tree on LEFT
- Code content on RIGHT
- (This is opposite of RealityViewport which has 3D content left, panels right)

### Platform Adaptations

#### macOS
- HSplitView with 280pt left panel
- Full editor header with TreeSitter status
- Debug panel overlay option
- Line numbers with alternating backgrounds

#### iOS
- Full-screen editor
- Sliding inspector from LEFT edge
- Compact header
- Bottom toolbar with material background

### Editor Features
- Language-specific theming (Swift=orange, C++=blue, MSL=purple)
- Line numbers toggle
- TreeSitter status indicators
- Debug panel for parser testing
- Template-based file creation

---

## 💻 Key Components

### ScriptManager
- Central state object (`@StateObject`)
- Manages all script files
- Template system for new files
- Search/filter functionality

### ScriptFile
- Individual script model (`@ObservableObject`)
- Language property (Swift/C++/MSL)
- Content and modification tracking
- Statistics (lines, size, etc.)

### Language Editors
Each editor (SwiftScriptEditor, CppScriptEditor) includes:
- Language-specific syntax highlighting (when TreeSitter works)
- Debug panel for TreeSitter status
- Platform-adaptive layout
- Parse testing functionality

### TreeSitterStatusView
- Shows runtime load status
- Shows language grammar status
- Visual indicators (checkmark/xmark)
- Help tooltips

---

## 🚧 Current Development Focus

### Immediate Goal
Fix TreeSitter language symbol visibility so `dlsym()` can find:
- `tree_sitter_swift` from TreeSitterSwift.xcframework
- `tree_sitter_cpp` from TreeSitterCpp.xcframework

### Possible Solutions Being Explored
1. Symbol visibility in XCFramework builds
2. Different linking flags in Xcode
3. Manual symbol registration
4. Alternative to `dlopen(nil, RTLD_LAZY)`

### What Success Looks Like
- TreeSitterStatusView shows all green checkmarks
- Syntax highlighting works for Swift and C++ code
- Parse tests can use actual language grammars
- No Objective-C bridging headers needed

---

## 🛠️ Development Guidelines

### Code Patterns
- Use `dlsym()` for all TreeSitter integration
- Check symbol availability before use
- Graceful fallback when TreeSitter unavailable
- Status tracking through dedicated types

### Adding New Languages
1. Build XCFramework with TreeXC
2. Link in Xcode project
3. Add symbol check in editor
4. Update status types

### Testing TreeSitter
1. Check TreeSitterStatusView indicators
2. Use debug panel in editors
3. Run parse test with play button
4. Check console for symbol lookup results

---

## 📝 Known Issues

1. **Language symbols not found** - Main blocker
2. **MSL editor** - Placeholder only
3. **Save/Load** - Not implemented
4. **Build integration** - Not connected
5. **Syntax highlighting** - Waiting on TreeSitter

---

## 🔍 Debugging Information

To verify the current state:
1. Run app and check TreeSitterStatusView
2. Open debug panel in any editor
3. Click parse test button
4. Check Xcode console for dlsym results

The runtime should show as loaded, but languages will show as not loaded.

---

*This document reflects the actual current state as of January 2025*