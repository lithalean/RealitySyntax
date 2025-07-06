# RealitySyntax — AI Context Document

**Date**: July 6, 2025  
**Status**: Active Development - Phase 2  
**Purpose**: Context engineering for AI systems working with RealitySyntax codebase

---

## Project Identity

**RealitySyntax** is the code editor component of Orchard. It is NOT:
- A standalone IDE application
- Part of "Reality Game Engine" (old placeholder name)
- Cross-platform beyond Apple ecosystem
- A generic text editor

**Correct Context**: RealitySyntax is a SwiftUI + TreeSitter multi-language code editor that runs natively on Apple Silicon, designed as the primary code editing interface for Orchard game development projects.

---

## Technical Architecture Deep Dive

### Core Philosophy
- **Apple Silicon Exclusive**: ARM64 only, no Intel support, no Rosetta
- **Latest APIs Only**: Uses macOS 26+, iOS 26+, Swift 6, TreeSitter parsing
- **Multi-Language Native**: Swift, C++, MSL with language-specific optimizations
- **Cross-Platform**: Single codebase for macOS and iOS with platform adaptations

### File Structure & Key Components

```
RealitySyntax/
├── Models/
│   ├── ScriptFile.swift          # Script file model with language support
│   └── ScriptManager.swift       # Central script management & operations
├── Editors/
│   ├── SwiftScriptEditor.swift   # Swift syntax highlighting & editing
│   ├── CppScriptEditor.swift     # C++ syntax highlighting & editing
│   └── MSLEditor.swift           # Metal Shading Language (planned)
├── Views/
│   ├── MacView.swift             # macOS layout (inspector LEFT, editor RIGHT)
│   └── iPhoneView.swift          # iOS layout (sliding inspector from LEFT)
├── Shared/                       # Modular shared components
│   ├── SharedInspectorView.swift # Generic inspector containers
│   ├── SharedOutlinerView.swift  # Generic outliner/hierarchy views
│   └── SharedPropertiesView.swift # Generic properties editing
└── Syntax/
    └── SyntaxView.swift          # Main entry point & platform router
```

### Key State Management Patterns

**ScriptManager** (`@StateObject`):
- Central source of truth for all script files
- Manages file operations (create, delete, rename, save)
- Template system for common script patterns
- Search and filtering capabilities
- Statistics tracking and reporting

**ScriptFile** (`@ObservableObject`):
- Individual script representation with language awareness
- Content tracking with modification state
- File statistics (lines, characters, size)
- Language-specific properties (Swift/C++/MSL)
- Template-based creation system

**Language Support Pattern**:
```swift
enum ScriptLanguage: String, CaseIterable {
    case swift = "Swift"
    case cpp = "C++"
    case msl = "MSL"
    
    var fileExtension: String { ... }
    var accentColor: Color { ... }
    var iconName: String { ... }
    var defaultContent: String { ... }
}
```

### Platform Layout Differences

**CRITICAL**: RealitySyntax has OPPOSITE layout from RealityViewport:
- **RealityViewport**: `[3D Scene] | [Inspector]` (content left, panels right)
- **RealitySyntax**: `[Inspector] | [Code Editor]` (panels left, content right)

**macOS Layout**:
- HSplitView with inspector/outliner on LEFT (280pt width)
- Code editor takes main area on RIGHT
- Resizable properties panel with drag gesture
- Toolbar with inspector toggle and script creation

**iOS Layout**:
- Full-screen code editor as primary view
- Inspector slides from LEFT edge (opposite of RealityViewport)
- Bottom toolbar with material background
- Touch-optimized compact headers and controls

### Editor Integration Patterns

**Language-Specific Editors**:
- `SwiftScriptEditor`: Orange theming, Swift-specific features
- `CppScriptEditor`: Blue theming, C++ build integration
- `MSLEditor`: Purple theming, Metal shader features (planned)

**TreeSitter Integration**:
- Runtime status tracking with visual indicators
- Language grammar loading for syntax highlighting
- Parse testing and debugging capabilities
- Placeholder bindings for actual TreeSitter implementation

**Adaptive Layouts**:
- Environment detection via `@Environment(\.horizontalSizeClass)`
- Compact vs regular size class adaptations
- Platform-specific font sizes and spacing
- Touch vs cursor interaction optimizations

### Code Editor Architecture

**Language Switching Pattern**:
```swift
switch script.language {
case .swift:
    SwiftScriptEditor()
case .cpp:
    CppScriptEditor()
case .msl:
    MSLEditorPlaceholder()
}
```

**Editor Features**:
- Line numbers with alternating backgrounds
- Adaptive header (compact vs full)
- TreeSitter status indicators
- Debug panels for parser testing
- Platform-appropriate action buttons

**Content Synchronization**:
- Scripts store content as `@Published var content: String`
- Real-time modification tracking with `isModified` state
- Automatic statistics calculation (lines, characters, file size)
- Template-based content initialization

---

## Current Development Status

### Working Features
- ✅ Multi-language script management (Swift, C++, MSL templates)
- ✅ Cross-platform adaptive layouts (Mac split view, iOS overlay)
- ✅ Script file operations (create, delete, rename, duplicate)
- ✅ Properties inspection with live statistics
- ✅ TreeSitter integration foundation
- ✅ Template system for common patterns
- ✅ Modular shared component architecture

### Known Issues
- TreeSitter language grammars not yet fully integrated
- MSL editor is placeholder only
- Save/load operations need file system integration
- Build system integration not implemented
- Code completion and IntelliSense not available

### Phase 2 Goals (Current)
- Complete TreeSitter language grammar integration
- Implement file system persistence
- Add build and run capabilities for Swift/C++
- Enhanced syntax highlighting and validation
- Code completion system foundation

---

## Development Guidelines for AI

### Code Patterns to Follow
- Use `ScriptLanguage` enum for all language-specific logic
- Leverage shared components from `Shared/` directory
- Platform-specific code with `#if os(macOS)` / `#if os(iOS)`
- Template-based script creation via `ScriptManager`
- Consistent naming: no "Unified" or "Syntax" prefixes

### Code Patterns to Avoid
- Hard-coded file paths or content
- Platform-specific UI frameworks (use SwiftUI equivalents)
- Direct file system access without ScriptManager
- Inline script content (use template system)
- Language-specific hardcoding outside ScriptLanguage enum

### Common Tasks
**Adding New Languages**: Extend `ScriptLanguage` enum, create new editor view
**Adding Script Templates**: Use `ScriptFile.create*()` static methods
**Platform Adaptations**: Use size class detection and platform guards
**TreeSitter Integration**: Extend parser status tracking in editors
**Shared Components**: Use generic protocols (OutlinerItem, PropertyEditable)

### Architecture Decisions Context
- **Why Multi-Language?**: Game development requires Swift (logic), C++ (performance), MSL (shaders)
- **Why TreeSitter?**: Modern syntax highlighting with grammar-based parsing
- **Why Opposite Layout?**: Code editors typically show file tree on left, content on right
- **Why Template System?**: Reduces boilerplate for common game programming patterns
- **Why Modular Shared Components?**: Consistency with RealityViewport and reusability

---

## Integration Context

RealitySyntax integrates with Orchard as:
- **Primary Code Editor**: All game logic, performance code, and shader development
- **Script Management**: Organization and file operations for project scripts
- **Build Integration**: Compilation and testing workflows
- **Template System**: Rapid prototyping with game development patterns

**Related Orchard Systems** (external to RealitySyntax):
- **RealityViewport**: 3D scene editor (uses opposite layout direction)
- **Build System**: Compilation and deployment pipeline
- **Asset Pipeline**: Resource import and processing
- **Project Management**: Workspace and project organization
- **Version Control**: Git integration and collaboration

---

## Multi-Language Development Context

### Swift in Orchard
- **Purpose**: Primary game logic, UI systems, scripting
- **Integration**: Godot-Swift bindings for native performance
- **Features**: Export properties, node inheritance, type safety
- **Templates**: Player controllers, game managers, UI components

### C++ in Orchard
- **Purpose**: Performance-critical code, engine extensions
- **Integration**: Godot-CPP for native module development
- **Features**: Direct memory management, system-level access
- **Templates**: Physics controllers, audio processors, platform bridges

### MSL in Orchard
- **Purpose**: Custom shaders, visual effects, compute kernels
- **Integration**: Metal 3 rendering pipeline integration
- **Features**: Apple GPU optimization, live preview
- **Templates**: Vertex shaders, fragment shaders, compute shaders

---

## AI Assistant Guidelines

When working with this codebase:
1. **Always consider language differences** (Swift vs C++ vs MSL patterns)
2. **Respect the layout direction** (inspector left, editor right)
3. **Use the template system** for new script creation
4. **Follow the modular architecture** with shared components
5. **Maintain cross-platform compatibility** (macOS and iOS)
6. **Use proper TreeSitter integration** patterns for syntax features
7. **Keep the Apple Silicon exclusive** philosophy
8. **Reference ScriptLanguage enum** for all language-specific logic

The goal is a professional, multi-language development environment that seamlessly integrates with the Orchard game engine ecosystem while providing native Apple platform experiences.

---

*Updated July 6, 2025 — RealitySyntax Context v1.0*