# RealitySyntax Architecture Context

**Purpose**: Technical blueprint and system design rationale  
**Version**: 2.0  
**Status**: Active Development - ~85% Complete  
**Last Updated**: January 2025

## Key Concepts (AI Quick Reference)

### Core Architecture Pattern
```
SwiftSyntax Bridge + LibClang Bridge + Platform Adaptive UI = Game Engine Script Editor
```

### Critical Design Elements
1. **Bridge Pattern** - Separate bridges for SwiftSyntax and LibClang
2. **Dynamic Loading** - LibClang loaded via dlsym for graceful degradation
3. **Game Engine Context** - Part of Orchard modular game engine
4. **Regex Tokenization** - Fast syntax highlighting without full AST parsing

## System Architecture

### Core Design Pattern
RealitySyntax follows a **bridge pattern architecture** where language-specific parsing is abstracted behind unified bridge interfaces. This enables consistent editor behavior across Swift, C++, and Metal Shading Language while leveraging specialized parsers.

### Orchard Game Engine Context
RealitySyntax is the **scripting and code editing module** of the Orchard game engine ecosystem:
- **RealityViewport**: 3D scene editing and manipulation
- **RealitySyntax**: Script editing with syntax highlighting (this project)
- **RealityAssets**: Asset management and pipeline
- **GameCore**: Core engine runtime without editor
- **Orchard**: Final integrated game engine with full editor

### Component Architecture
```
┌─────────────────────────────────────────┐
│           RealitySyntaxApp              │
│              (App Entry)                │
└─────────────────┬───────────────────────┘
                  │
┌─────────────────┴───────────────────────┐
│            SyntaxManager                │
│         (@StateObject)                  │
│   • Runtime status management           │
│   • Bridge orchestration                │
│   • Language availability checking      │
└─────────┬───────────────┬───────────────┘
          │               │
┌─────────┴─────┐ ┌───────┴───────────────┐
│ SwiftSyntax   │ │    LibClang           │
│   Bridge      │ │     Bridge            │
│ • Tokenizer   │ │ • C++ parsing         │
│ • Regex-based │ │ • MSL support         │
└───────────────┘ └───────────────────────┘
          │               │
┌─────────┴───────────────┴───────────────┐
│          Editor Components              │
│   • SwiftScriptEditor                   │
│   • CppScriptEditor                     │
│   • Platform-adaptive layouts           │
└─────────────────────────────────────────┘
```

## Technical Architecture

### Bridge Pattern Implementation
**Purpose**: Abstract language-specific parsing behind consistent interfaces

```swift
// SwiftSyntaxBridge: Regex-based tokenization
public func tokenizeSwiftCode(_ code: String) -> [(String, String)] {
    // Pattern matching for Swift syntax elements
}

// LibClangBridge: C++/MSL parsing with diagnostics
public func tokenizeCppCode(_ code: String) -> [(String, String)] {
    // LibClang-based tokenization
}
```

**Rationale**:
- Unified editor interface across languages
- Independent parser evolution
- Graceful degradation if parser unavailable
- Simplified testing and maintenance

### Dynamic Loading Strategy
**Goal**: Optional LibClang support without hard dependencies

```swift
// Runtime symbol checking
guard let symbol = dlsym(dlopen(nil, RTLD_LAZY), "clang_createIndex") else {
    // Graceful degradation - basic syntax highlighting only
    return
}
```

**Benefits**:
- App runs without LibClang
- Smaller binary size
- Optional C++/MSL support
- Better error handling

### State Management
**Pattern**: ObservableObject with Published properties
- `SyntaxManager` as central coordinator
- `SyntaxFileManager` for file operations
- Reactive UI updates via Combine
- Platform-agnostic state

### Multi-Language Architecture
```
Swift Scripts → SwiftSyntax Bridge → Tokenization → Editor
C++ Scripts   → LibClang Bridge    → Tokenization → Editor  
MSL Scripts   → LibClang Bridge    → Tokenization → Editor
```

## Design Patterns

### Pattern 1: Bridge Pattern
```
PURPOSE: Decouple parsing implementation from editor UI
IMPLEMENTATION: Language-specific bridges with common protocol
BENEFITS: 
  - Easy to add new languages
  - Parser updates don't affect UI
  - Testable in isolation
  - Graceful feature degradation
```

### Pattern 2: Regex Tokenization
```
PURPOSE: Fast syntax highlighting without full AST parsing
IMPLEMENTATION: Pattern-based token identification
BENEFITS:
  - Immediate visual feedback
  - Low memory footprint
  - Simple implementation
  - Adequate for highlighting
TRADEOFFS:
  - Limited semantic understanding
  - No refactoring support
  - Basic error detection only
```

### Pattern 3: Platform-Adaptive Design
```
PURPOSE: Native experience on all Apple platforms
IMPLEMENTATION: Size class based layout switching
BENEFITS:
  - Single codebase
  - Platform-specific optimizations
  - Native feel everywhere
  - Consistent behavior
```

### Pattern 4: Game-Centric Language Support
```
PURPOSE: Optimize for game development workflows
LANGUAGES:
  - Swift: Gameplay logic, AI, game systems
  - C++: Performance-critical code, engine integration
  - MSL: Shader programming for Metal
BENEFITS:
  - Right tool for each task
  - Native performance
  - Unified editing experience
```

## Anti-Patterns to Avoid

### ❌ NEVER: Full AST Parsing for Highlighting
```swift
// WRONG - Too heavy for real-time highlighting
let syntaxTree = try SyntaxParser.parse(source: code)
syntaxTree.walk { node in
    // Process every AST node
}

// CORRECT - Lightweight tokenization
let tokens = tokenizeSwiftCode(code)
// Apply highlighting to tokens
```

### ❌ NEVER: Hard LibClang Dependencies
```swift
// WRONG - App crashes if LibClang missing
import ClangImporter // Direct import

// CORRECT - Dynamic loading with fallback
if let clang = loadLibClangDynamically() {
    // Use LibClang features
} else {
    // Fallback to basic highlighting
}
```

### ❌ NEVER: Platform-Specific Code Paths
```swift
// WRONG - Duplicate implementations
#if os(macOS)
    // macOS-only editor
#else
    // iOS-only editor
#endif

// CORRECT - Adaptive components
struct AdaptiveEditor: View {
    @Environment(\.horizontalSizeClass) var sizeClass
    // Single implementation, adaptive behavior
}
```

## Architectural Decisions Log

### Decision: SwiftSyntax + LibClang over TreeSitter
**Rationale**: Native Apple toolchain, better language support
**Implementation**: Custom XCFrameworks with bridge pattern
**Result**: Type-safe Swift integration, official parser support

### Decision: Regex Tokenization over Full AST
**Rationale**: Performance and simplicity for highlighting
**Implementation**: Pattern-based tokenizers in bridges
**Result**: Fast highlighting, limited semantic features

### Decision: Bridge Pattern Architecture
**Rationale**: Flexibility for multiple parsers and languages
**Implementation**: Protocol-based bridges with common interface
**Result**: Clean separation, easy to extend

### Decision: Game Engine Modular Design
**Rationale**: Independent development of engine components
**Implementation**: RealitySyntax as standalone module
**Result**: Reusable editor, focused development

### Decision: Custom XCFramework Builds
**Rationale**: Control over build process and optimizations
**Implementation**: Build scripts for SwiftSyntax.xcframework
**Result**: Optimized frameworks, reduced binary size

## Performance Considerations

### Tokenization Performance
- Regex compilation cached
- Incremental parsing planned
- Async tokenization for large files
- UI remains responsive

### Memory Management
- Token arrays instead of full AST
- Weak references where appropriate
- File content streaming for large files

## Security Considerations

### Code Execution Safety
- No code evaluation in editor
- Syntax checking only
- Sandboxed file access
- No arbitrary library loading

## Future Architecture Extensions

### Planned: Full AST Features
- Refactoring support
- Semantic highlighting
- Code completion
- Go to definition

### Planned: Language Server Protocol
- External language servers
- Richer language features
- Standardized integration

### Planned: Incremental Parsing
- Real-time syntax updates
- Better performance
- Reduced battery usage

### Planned: Plugin Architecture
- Custom language support
- User-defined tokenizers
- Theme extensions