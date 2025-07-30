# RealitySyntax Bridge Integration Documentation

**Purpose**: Technical details of SwiftSyntax and LibClang bridge implementations  
**Version**: 1.0  
**Status**: Regex tokenization working, full AST parsing planned  
**Last Updated**: January 2025

## Bridge Architecture Overview

RealitySyntax uses a bridge pattern to abstract language-specific parsing behind a unified interface, enabling consistent editor behavior across Swift, C++, and Metal Shading Language.

```
┌─────────────────────────────────────────────────────┐
│                 SyntaxManager                       │
│          (Orchestrator & Status Manager)            │
└─────────────────┬────────────────┬──────────────────┘
                  │                │
┌─────────────────▼────┐  ┌───────▼──────────────────┐
│   SwiftSyntaxBridge  │  │    LibClangBridge        │
│                      │  │                          │
│ • Regex Tokenization │  │ • Dynamic Loading        │
│ • Swift Keywords     │  │ • C++/MSL Tokenization   │
│ • Future: Full AST   │  │ • Diagnostic Placeholders│
└──────────────────────┘  └──────────────────────────┘
```

## SwiftSyntaxBridge Implementation

### Current Implementation: Regex Tokenization

```swift
public class SwiftSyntaxBridge {
    // Token types for syntax highlighting
    enum TokenType: String {
        case keyword = "keyword"
        case identifier = "identifier"
        case string = "string"
        case number = "number"
        case comment = "comment"
        case type = "type"
        case function = "function"
    }
    
    // Main tokenization function
    public func tokenizeSwiftCode(_ code: String) -> [(String, String)] {
        var tokens: [(String, String)] = []
        
        // Define patterns
        let patterns: [(TokenType, String)] = [
            (.comment, "//.*?$|/\\*.*?\\*/"),
            (.string, "\"([^\"\\\\]|\\\\.)*\"|'([^'\\\\]|\\\\.)'"),
            (.keyword, "\\b(func|var|let|class|struct|enum|protocol|extension|if|else|...)\\b"),
            (.number, "\\b\\d+\\.?\\d*\\b"),
            (.type, "\\b[A-Z][a-zA-Z0-9]*\\b"),
            (.function, "\\b[a-z][a-zA-Z0-9]*(?=\\s*\\()")
        ]
        
        // Apply patterns and collect tokens
        for (tokenType, pattern) in patterns {
            let regex = try! NSRegularExpression(pattern: pattern)
            let matches = regex.matches(in: code, range: NSRange(code.startIndex..., in: code))
            
            for match in matches {
                if let range = Range(match.range, in: code) {
                    let text = String(code[range])
                    tokens.append((text, tokenType.rawValue))
                }
            }
        }
        
        return tokens.sorted { $0.0.startIndex < $1.0.startIndex }
    }
}
```

### Pattern Definitions

| Pattern | Description | Example Matches |
|---------|-------------|-----------------|
| Keywords | Swift reserved words | `func`, `class`, `if` |
| Strings | String literals | `"Hello"`, `'c'` |
| Comments | Single/multi-line | `// comment`, `/* block */` |
| Numbers | Numeric literals | `42`, `3.14` |
| Types | Capitalized identifiers | `String`, `UIView` |
| Functions | Function calls | `print(`, `map(` |

### Future: Full AST Integration

```swift
// Planned implementation using SwiftSyntax
import SwiftSyntax
import SwiftParser

public func parseSwiftAST(_ code: String) -> SourceFileSyntax {
    let sourceFile = Parser.parse(source: code)
    
    // Visit AST nodes for semantic analysis
    let visitor = SyntaxHighlightingVisitor()
    visitor.walk(sourceFile)
    
    return sourceFile
}
```

### SwiftSyntax Capabilities (When Fully Integrated)

1. **Semantic Highlighting**
   - Variable usage vs declaration
   - Type inference visualization
   - Scope-aware coloring

2. **Code Intelligence**
   - Symbol extraction
   - Type information
   - Function signatures

3. **Refactoring Support**
   - Rename symbols
   - Extract method
   - Inline variable

## LibClangBridge Implementation

### Dynamic Loading Strategy

```swift
public class LibClangBridge {
    // Function pointer types
    typealias ClangCreateIndex = @convention(c) (CInt, CInt) -> OpaquePointer?
    typealias ClangParseTranslationUnit = @convention(c) (OpaquePointer?, UnsafePointer<CChar>?, ...) -> OpaquePointer?
    
    // Stored function pointers
    private var clang_createIndex: ClangCreateIndex?
    private var clang_parseTranslationUnit: ClangParseTranslationUnit?
    
    // Check and load LibClang
    public init() {
        loadLibClangSymbols()
    }
    
    private func loadLibClangSymbols() {
        guard let createIndexSymbol = dlsym(dlopen(nil, RTLD_LAZY), "clang_createIndex") else {
            print("LibClang not available")
            return
        }
        
        self.clang_createIndex = unsafeBitCast(createIndexSymbol, to: ClangCreateIndex.self)
        // Load other symbols...
    }
}
```

### C++/MSL Tokenization

```swift
public func tokenizeCppCode(_ code: String, language: Language) -> [(String, String)] {
    var tokens: [(String, String)] = []
    
    // Language-specific keywords
    let keywords = language == .cpp ? cppKeywords : mslKeywords
    
    let patterns: [(String, String)] = [
        ("comment", "//.*?$|/\\*.*?\\*/"),
        ("string", "\"([^\"\\\\]|\\\\.)*\""),
        ("keyword", "\\b(\(keywords.joined(separator: "|")))\\b"),
        ("number", "\\b\\d+\\.?\\d*[fFlL]?\\b"),
        ("preprocessor", "#\\s*\\w+.*?$"),
        ("type", "\\b(int|float|double|void|auto|...)\\b")
    ]
    
    // Apply patterns similar to Swift
    // ...
    
    return tokens
}
```

### Diagnostic Integration (Placeholder)

```swift
public struct Diagnostic {
    let severity: Severity
    let message: String
    let line: Int
    let column: Int
    
    enum Severity {
        case error, warning, note
    }
}

public func getDiagnostics(_ code: String) -> [Diagnostic] {
    var diagnostics: [Diagnostic] = []
    
    // Basic syntax checks (placeholder)
    let lines = code.components(separatedBy: .newlines)
    for (index, line) in lines.enumerated() {
        // Check for missing semicolons (C++)
        if needsSemicolon(line) && !line.hasSuffix(";") {
            diagnostics.append(Diagnostic(
                severity: .error,
                message: "Missing semicolon",
                line: index + 1,
                column: line.count
            ))
        }
    }
    
    return diagnostics
}
```

## SyntaxManager Orchestration

### Status Management

```swift
@MainActor
public class SyntaxManager: ObservableObject {
    @Published var swiftSyntaxStatus: FrameworkStatus = .checking
    @Published var libClangStatus: FrameworkStatus = .checking
    @Published var swiftSupported = false
    @Published var cppSupported = false
    @Published var mslSupported = false
    
    private let swiftBridge = SwiftSyntaxBridge()
    private let clangBridge = LibClangBridge()
    
    public func checkFrameworkAvailability() {
        // Check SwiftSyntax (always available via XCFramework)
        swiftSyntaxStatus = .loaded
        swiftSupported = true
        
        // Check LibClang (dynamic loading)
        if clangBridge.isAvailable {
            libClangStatus = .loaded
            cppSupported = true
            mslSupported = true
        } else {
            libClangStatus = .notAvailable
        }
    }
}
```

### Language Routing

```swift
public func tokenize(_ code: String, language: Language) -> [(String, String)] {
    switch language {
    case .swift:
        return swiftBridge.tokenizeSwiftCode(code)
    case .cpp, .msl:
        return clangBridge.tokenizeCppCode(code, language: language)
    }
}
```

## Performance Considerations

### Current Performance (Regex)
- **Tokenization Speed**: ~10ms for 1000 lines
- **Memory Usage**: Minimal (string operations)
- **Scalability**: Linear with file size
- **Real-time**: Suitable for live editing

### Future Performance (Full AST)
- **Parse Speed**: ~50-100ms for 1000 lines
- **Memory Usage**: Higher (full AST in memory)
- **Scalability**: May need incremental parsing
- **Caching**: Required for large files

## Error Handling Strategy

### Graceful Degradation
```swift
public func highlightCode(_ code: String, language: Language) -> AttributedString {
    do {
        let tokens = tokenize(code, language: language)
        return applyHighlighting(tokens, to: code)
    } catch {
        // Fallback to plain text
        return AttributedString(code)
    }
}
```

### Parser Errors
- Regex tokenization: Virtually error-free
- AST parsing: May fail on invalid syntax
- LibClang: Handle missing framework gracefully

## Integration Points

### Editor Integration
```swift
// In SwiftScriptEditor
@StateObject private var syntaxManager = SyntaxManager()

var highlightedCode: AttributedString {
    syntaxManager.tokenize(script.content, language: .swift)
        .reduce(into: AttributedString(script.content)) { result, token in
            // Apply color based on token type
        }
}
```

### Debug Panel Integration
```swift
// Show tokenization results
ForEach(tokens, id: \.0) { token in
    HStack {
        Text(token.0) // Token text
        Text(token.1) // Token type
            .foregroundColor(.secondary)
    }
}
```

## Language-Specific Features

### Swift-Specific
- String interpolation handling
- Property wrapper detection  
- Async/await keywords
- Result builders

### C++-Specific
- Template syntax
- Namespace handling
- Modern C++ keywords (auto, decltype)
- Preprocessor directives

### MSL-Specific
- Shader stage attributes
- Buffer bindings
- Texture sampling functions
- Metal-specific types

## Testing Strategy

### Unit Tests (Needed)
```swift
func testSwiftTokenization() {
    let code = "func hello() { print(\"world\") }"
    let tokens = bridge.tokenizeSwiftCode(code)
    
    XCTAssertEqual(tokens.count, 7)
    XCTAssertEqual(tokens[0].1, "keyword") // func
    XCTAssertEqual(tokens[4].1, "string")  // "world"
}
```

### Integration Tests
- Multi-language files
- Large file performance
- Error recovery
- Platform differences

## Future Enhancements

### Short Term
1. **Improve Regex Patterns**
   - Better string interpolation
   - Multi-line constructs
   - Edge case handling

2. **Enhanced Diagnostics**
   - Wire up real LibClang diagnostics
   - SwiftSyntax error reporting
   - Inline error display

### Long Term
1. **Full AST Integration**
   - Semantic highlighting
   - Code completion
   - Refactoring support

2. **Language Server Protocol**
   - SourceKit-LSP for Swift
   - clangd for C++
   - Unified interface

3. **Incremental Parsing**
   - Performance optimization
   - Real-time updates
   - Memory efficiency

## Troubleshooting Guide

### Common Issues

1. **Tokens Not Highlighted**
   - Check regex patterns
   - Verify language detection
   - Ensure bridge initialized

2. **Performance Degradation**
   - Profile tokenization time
   - Check for regex backtracking
   - Consider caching results

3. **LibClang Not Loading**
   - Verify framework in bundle
   - Check symbol names
   - Test dlopen directly

### Debug Commands
```swift
// Check bridge status
print("SwiftSyntax: \(syntaxManager.swiftSyntaxStatus)")
print("LibClang: \(syntaxManager.libClangStatus)")

// Profile tokenization
let start = Date()
let tokens = bridge.tokenize(code)
print("Tokenized in \(Date().timeIntervalSince(start))s")
```

---

*This document details the bridge pattern implementation that enables RealitySyntax to support multiple languages while maintaining a clean, extensible architecture.*