# RealitySyntax Context Engineering Manifest

**Purpose**: Master reference for AI systems to understand the context module structure  
**Last Updated**: January 29, 2025  
**Manifest Version**: 2.0

## Project Quick Facts

**Product**: SwiftUI-based multi-language code editor for the Orchard game engine  
**Status**: ~85% Complete - Core features working, needs persistence and enhanced diagnostics  
**Architecture**: SwiftSyntax + LibClang with Bridge Pattern (NOT TreeSitter!)  
**Platforms**: macOS 14.0+, iOS 17.0+, tvOS 17.0+ (Apple Silicon optimized)  
**Languages**: Swift (gameplay), C++ (performance), Metal Shading Language (shaders)  
**Part of**: Orchard Game Engine ecosystem (with RealityViewport and RealityAssets)

## Module Version Registry

| Module | Version | Last Modified | Status | Health |
|--------|---------|--------------|--------|---------|
| ARCHITECTURE.md | 2.0 | 2025-01-29 | Updated | ✅ |
| IMPLEMENTATION.md | 2.0 | 2025-01-29 | Updated | ✅ |
| VISUAL.md | 1.0 | 2025-01-29 | Needs Update | ⚠️ |
| NAVIGATION.md | 1.0 | 2025-01-29 | Needs Update | ⚠️ |
| INTEGRATION.yaml | 1.0 | 2025-01-29 | Needs Update | ⚠️ |
| EVOLUTION.md | 1.0 | 2025-01-29 | Needs Update | ⚠️ |
| session.json | - | DYNAMIC | Needs Update | ⚠️ |
| XCFRAMEWORKS.md | - | NOT CREATED | Required | ❌ |
| GAMEINTEGRATION.md | - | NOT CREATED | Required | ❌ |
| BRIDGEINTEGRATION.md | - | NOT CREATED | Required | ❌ |

## Quick Reference Matrix

| Information Needed | Primary Module | Secondary Module |
|-------------------|----------------|------------------|
| How bridge pattern works | ARCHITECTURE.md | BRIDGEINTEGRATION.md* |
| Current implementation status | IMPLEMENTATION.md | session.json |
| UI layout and design specs | VISUAL.md | NAVIGATION.md |
| User flow through the app | NAVIGATION.md | VISUAL.md |
| Parser framework details | BRIDGEINTEGRATION.md* | INTEGRATION.yaml |
| Why SwiftSyntax over TreeSitter | EVOLUTION.md | ARCHITECTURE.md |
| XCFramework build process | XCFRAMEWORKS.md* | IMPLEMENTATION.md |
| Game scripting workflows | GAMEINTEGRATION.md* | ARCHITECTURE.md |
| File structure and status | IMPLEMENTATION.md | - |
| Platform-specific behavior | VISUAL.md | NAVIGATION.md |

*Modules marked with asterisk need to be created

## Context Loading Strategy

### For Parser/Bridge Issues
1. BRIDGEINTEGRATION.md (when created)
2. IMPLEMENTATION.md (current status)
3. ARCHITECTURE.md (bridge pattern)
4. session.json (debugging notes)

### For XCFramework Work
1. XCFRAMEWORKS.md (when created)
2. INTEGRATION.yaml (dependencies)
3. IMPLEMENTATION.md (framework status)

### For Game Engine Integration
1. GAMEINTEGRATION.md (when created)
2. ARCHITECTURE.md (Orchard context)
3. NAVIGATION.md (scripting workflows)

### For UI/UX Work
1. VISUAL.md (design specifications)
2. NAVIGATION.md (user flows)
3. ARCHITECTURE.md (component patterns)
4. IMPLEMENTATION.md (what's implemented)

### For New Features
1. ARCHITECTURE.md (design patterns)
2. NAVIGATION.md (where it fits)
3. VISUAL.md (UI requirements)
4. IMPLEMENTATION.md (integration points)

### For Bug Fixes
1. IMPLEMENTATION.md (current state)
2. session.json (recent changes)
3. ARCHITECTURE.md (design constraints)

## Critical Architecture Clarifications

### ❗ NOT TreeSitter - Using SwiftSyntax + LibClang
The project uses:
- **SwiftSyntax**: Apple's official Swift parser (via custom XCFramework)
- **LibClang**: LLVM's C/C++ parser (dynamically loaded)
- **Bridge Pattern**: Unified interface for multiple parsers
- **Regex Tokenization**: Currently using simplified tokenization, not full AST

### 🎮 Game Engine Context
RealitySyntax is part of the Orchard ecosystem:
- Designed for game scripting (Swift gameplay, C++ performance, MSL shaders)
- Integrates with RealityViewport (3D editing) and RealityAssets (asset pipeline)
- 100% Apple-native alternative to Godot/Unreal

## Module Relationships

```
MANIFEST.md (you are here)
    ├── Core Architecture
    │   ├── ARCHITECTURE.md ← Bridge pattern, game engine design
    │   ├── BRIDGEINTEGRATION.md* ← Parser technical details
    │   └── XCFRAMEWORKS.md* ← Custom framework builds
    │
    ├── Game Engine Context  
    │   ├── GAMEINTEGRATION.md* ← Scripting workflows
    │   └── EVOLUTION.md ← Why pure Apple approach
    │
    ├── Implementation Status
    │   ├── IMPLEMENTATION.md ← Current feature matrix
    │   ├── INTEGRATION.yaml ← Dependencies
    │   └── session.json ← Active work
    │
    └── User Experience
        ├── VISUAL.md ← UI specifications
        └── NAVIGATION.md ← User flows
```

## AI Agent Instructions

When working with RealitySyntax:

1. **First Time**: Read this MANIFEST, then ARCHITECTURE.md for bridge pattern
2. **Parser Work**: Check BRIDGEINTEGRATION.md (when created), then IMPLEMENTATION.md
3. **XCFramework Issues**: Start with XCFRAMEWORKS.md (when created)
4. **Game Features**: Load GAMEINTEGRATION.md (when created) for context
5. **UI Changes**: VISUAL.md first, then NAVIGATION.md
6. **Current Work**: Always check session.json

## Module Creation Priority

### 🚨 Highest Priority (Create First)
1. **XCFRAMEWORKS.md** - Document custom framework build process
2. **BRIDGEINTEGRATION.md** - Explain SwiftSyntax/LibClang bridges
3. **GAMEINTEGRATION.md** - Game engine scripting context

### ⚠️ High Priority (Update Next)
4. **EVOLUTION.md** - Add correct architectural decisions
5. **INTEGRATION.yaml** - Update with real dependencies
6. **VISUAL.md** - Correct UI documentation
7. **NAVIGATION.md** - Update flows

### 📝 Ongoing
8. **session.json** - Keep current with work status

## Health Indicators

✅ **Updated Modules** (2/10)
- ARCHITECTURE.md - Correctly documents bridge pattern
- IMPLEMENTATION.md - Accurate feature status

⚠️ **Outdated Modules** (5/10)
- VISUAL.md - Still references TreeSitter
- NAVIGATION.md - Needs game context
- INTEGRATION.yaml - Wrong dependencies
- EVOLUTION.md - Missing real decisions
- session.json - Old investigation

❌ **Missing Critical Modules** (3/10)
- XCFRAMEWORKS.md - Build process undocumented
- BRIDGEINTEGRATION.md - Parser details missing
- GAMEINTEGRATION.md - Game context missing

## Next Steps for Documentation

1. **Create XCFRAMEWORKS.md** - Document SwiftSyntax.xcframework build
2. **Create BRIDGEINTEGRATION.md** - Explain parser bridges
3. **Create GAMEINTEGRATION.md** - Game scripting workflows
4. **Update remaining modules** - Correct TreeSitter references
5. **Validate cross-references** - Ensure consistency

## Key Corrections Made

- ✅ TreeSitter → SwiftSyntax + LibClang
- ✅ Unknown status → ~85% complete
- ✅ Added Orchard game engine context
- ✅ Clarified bridge pattern architecture
- ✅ Updated implementation status
- ❌ Still need to update other modules

---

*This manifest reflects the corrected understanding of RealitySyntax as a SwiftSyntax/LibClang-based editor for the Orchard game engine. Critical modules still need creation.*