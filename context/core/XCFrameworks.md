# RealitySyntax XCFrameworks Documentation

**Purpose**: Document custom XCFramework build process and integration  
**Version**: 1.0  
**Status**: SwiftSyntax.xcframework built, LibClang.xcframework needed  
**Last Updated**: January 2025

## XCFramework Overview

RealitySyntax uses custom-built XCFrameworks to integrate Apple's SwiftSyntax and LLVM's LibClang while maintaining a pure Swift codebase and optimizing binary size.

### Current Framework Status

| Framework | Status | Build Script | Integration | Size |
|-----------|--------|--------------|-------------|------|
| **SwiftSyntax.xcframework** | ✅ Built | Available | Working | ~15MB |
| **LibClang.xcframework** | 🔄 Needed | To Create | Dynamic Loading | TBD |

## SwiftSyntax XCFramework

### Build Process

Based on the existing build script pattern, SwiftSyntax.xcframework is built from Apple's swift-syntax repository:

```bash
#!/bin/bash
# build-swiftsyntax-xcframework.sh

# Clone swift-syntax
git clone https://github.com/apple/swift-syntax.git
cd swift-syntax

# Build for each platform
swift build -c release --arch arm64 \
    --platform macos \
    --minimum-deployment-target 14.0

swift build -c release --arch arm64 \
    --platform ios \
    --minimum-deployment-target 17.0

swift build -c release --arch arm64 \
    --platform tvos \
    --minimum-deployment-target 17.0

# Create XCFramework
xcodebuild -create-xcframework \
    -framework build/release/SwiftSyntax.framework \
    -framework build/ios-release/SwiftSyntax.framework \
    -framework build/tvos-release/SwiftSyntax.framework \
    -output SwiftSyntax.xcframework
```

### Integration in Xcode

1. **Add XCFramework**
   - Drag SwiftSyntax.xcframework to project
   - Select "Embed & Sign"
   - Ensure all target platforms selected

2. **Import in Bridge**
   ```swift
   import SwiftSyntax
   import SwiftParser
   
   public class SwiftSyntaxBridge {
       // Bridge implementation
   }
   ```

3. **Framework Contents**
   - Core parsing functionality
   - AST node types
   - Visitor patterns
   - Syntax builders

### Current Usage

The SwiftSyntax.xcframework is currently used for:
- Token type definitions
- Future AST parsing preparation
- Syntax structure validation
- Language-specific features

**Note**: Currently using regex tokenization for performance, but framework provides full AST capability for future enhancement.

## LibClang XCFramework (To Build)

### Proposed Build Process

```bash
#!/bin/bash
# build-libclang-xcframework.sh

# Download LLVM/Clang source
git clone https://github.com/llvm/llvm-project.git
cd llvm-project

# Configure CMake for each platform
# macOS build
cmake -S llvm -B build-macos \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_OSX_ARCHITECTURES=arm64 \
    -DCMAKE_OSX_DEPLOYMENT_TARGET=14.0 \
    -DLLVM_ENABLE_PROJECTS=clang \
    -DLLVM_TARGETS_TO_BUILD=AArch64 \
    -DBUILD_SHARED_LIBS=OFF \
    -DLLVM_BUILD_TOOLS=OFF \
    -DLLVM_BUILD_TESTS=OFF

# iOS build  
cmake -S llvm -B build-ios \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_SYSTEM_NAME=iOS \
    -DCMAKE_OSX_ARCHITECTURES=arm64 \
    -DCMAKE_OSX_DEPLOYMENT_TARGET=17.0 \
    -DLLVM_ENABLE_PROJECTS=clang \
    -DLLVM_TARGETS_TO_BUILD=AArch64

# Build
cmake --build build-macos --target libclang
cmake --build build-ios --target libclang

# Create XCFramework
xcodebuild -create-xcframework \
    -library build-macos/lib/libclang.dylib \
    -headers build-macos/include \
    -library build-ios/lib/libclang.dylib \
    -headers build-ios/include \
    -output LibClang.xcframework
```

### Dynamic Loading Strategy

```swift
// LibClangBridge dynamic loading
private func loadLibClang() -> Bool {
    // Check for symbols
    guard let createIndex = dlsym(dlopen(nil, RTLD_LAZY), "clang_createIndex"),
          let parseTranslationUnit = dlsym(dlopen(nil, RTLD_LAZY), "clang_parseTranslationUnit") else {
        return false
    }
    
    // Store function pointers
    self.clang_createIndex = unsafeBitCast(createIndex, to: CreateIndexFunc.self)
    self.clang_parseTranslationUnit = unsafeBitCast(parseTranslationUnit, to: ParseTranslationUnitFunc.self)
    
    return true
}
```

### Required Symbols

| Symbol | Purpose | Status |
|--------|---------|--------|
| clang_createIndex | Initialize parser | ✅ Found |
| clang_parseTranslationUnit | Parse C++ files | ✅ Found |
| clang_tokenize | Token extraction | 🔄 Planned |
| clang_getDiagnostic | Error reporting | 🔄 Planned |
| clang_disposeIndex | Cleanup | ✅ Found |

## XCFramework Architecture Benefits

### Binary Size Optimization
- Strip unnecessary architectures
- Remove unused LLVM components  
- Optimize for Apple Silicon
- Selective symbol exports

### Platform-Specific Builds
```yaml
macOS:
  - Full feature set
  - Desktop optimizations
  - Larger binary acceptable

iOS/iPadOS:
  - Mobile optimizations
  - Reduced feature set
  - Size constraints

tvOS:
  - Minimal features
  - Read-only operations
  - Smallest binary
```

### Security Considerations
- Code signed frameworks
- Notarization ready
- No arbitrary code execution
- Sandboxed operation

## Build Configuration

### Optimization Flags
```
SWIFT_OPTIMIZATION_LEVEL = -O
ENABLE_BITCODE = NO
STRIP_INSTALLED_PRODUCT = YES
DEAD_CODE_STRIPPING = YES
```

### Framework Settings
```
MACH_O_TYPE = staticlib (for size)
DYLIB_INSTALL_NAME_BASE = @rpath
FRAMEWORK_VERSION = A
```

## Integration Patterns

### Direct Import (SwiftSyntax)
```swift
import SwiftSyntax

// Direct usage of types
let sourceFile = SourceFileSyntax(...)
```

### Dynamic Loading (LibClang)
```swift
// Function pointer types
typealias CreateIndexFunc = @convention(c) (CInt, CInt) -> OpaquePointer?

// Runtime binding
if let symbol = dlsym(handle, "clang_createIndex") {
    let function = unsafeBitCast(symbol, to: CreateIndexFunc.self)
}
```

## Troubleshooting

### Common Build Issues

1. **Architecture Mismatch**
   ```
   Error: Building for iOS, but linking in dylib built for macOS
   Solution: Ensure correct platform flags in build script
   ```

2. **Missing Dependencies**
   ```
   Error: Cannot find module 'SwiftSyntax'
   Solution: Check framework search paths, ensure embedded
   ```

3. **Symbol Visibility**
   ```
   Error: Symbol not found at runtime
   Solution: Check export lists, use nm to verify symbols
   ```

### Debugging Commands

```bash
# Check framework architectures
lipo -info SwiftSyntax.xcframework/macos-arm64/SwiftSyntax.framework/SwiftSyntax

# List exported symbols
nm -gU LibClang.xcframework/macos-arm64/LibClang.framework/LibClang

# Verify code signing
codesign -dv SwiftSyntax.xcframework
```

## Maintenance

### Version Updates
- Track swift-syntax releases
- Monitor LLVM/Clang versions
- Test on beta OS versions
- Update minimum deployments

### Build Automation
```yaml
CI Pipeline:
  - Trigger on dependency update
  - Build all platforms
  - Run integration tests
  - Archive frameworks
  - Update documentation
```

## Future Enhancements

### Planned Improvements
1. **Incremental Builds** - Faster framework updates
2. **Symbol Stripping** - Smaller binaries
3. **Module Maps** - Better Swift integration
4. **Bitcode Support** - Future compatibility

### Under Consideration
- WebAssembly builds for web editor
- Catalyst support for Mac apps
- Framework plugins for extensibility
- Custom parser additions

## Framework Size Analysis

### Current Sizes (Approximate)
```
SwiftSyntax.xcframework:
  - macOS: 15MB
  - iOS: 12MB  
  - tvOS: 10MB
  - Total: ~40MB

LibClang.xcframework (estimated):
  - macOS: 25MB
  - iOS: 20MB
  - tvOS: 15MB
  - Total: ~65MB
```

### Size Optimization Strategies
1. Strip debug symbols in release
2. Remove unused language support
3. Compress resources
4. Use static linking where possible

---

*This document tracks the custom XCFramework build process critical to RealitySyntax's pure Apple architecture. The frameworks enable native performance while maintaining clean Swift integration.* 