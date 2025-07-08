lithalean@Mac ~ % cd /Users/lithalean/Documents/Developer/3_Build/Xcode/RealitySyntax
lithalean@Mac RealitySyntax % tree
.
├── README.md
├── RealitySyntax
│   ├── App
│   │   ├── ContentView.swift
│   │   └── RealitySyntaxApp.swift
│   ├── Assets.xcassets
│   │   ├── AccentColor.colorset
│   │   │   └── Contents.json
│   │   ├── AppIcon.appiconset
│   │   │   └── Contents.json
│   │   └── Contents.json
│   ├── Core
│   │   └── Managers
│   │       └── ScriptManager.swift
│   ├── CppScriptEditor.swift
│   ├── Inspector
│   │   ├── InspectorView.swift
│   │   ├── OutlinerView.swift
│   │   └── PropertiesView.swift
│   ├── Item.swift
│   ├── Sources
│   │   └── Shared
│   │       ├── Components
│   │       │   └── TreeSitterStatusView.swift
│   │       ├── Extensions
│   │       │   └── HapticFeedback.swift
│   │       ├── PlatformColor.swift
│   │       └── Types
│   │           └── TreeSitterStatusTypes.swift
│   ├── SwiftScriptEditor.swift
│   ├── Syntax
│   │   ├── ScriptFile.swift
│   │   └── SyntaxView.swift
│   └── Views
│       ├── iPhoneView.swift
│       └── MacView.swift
├── RealitySyntax.xcodeproj
│   ├── project.pbxproj
│   ├── project.xcworkspace
│   │   ├── contents.xcworkspacedata
│   │   ├── xcshareddata
│   │   │   └── swiftpm
│   │   │       └── configuration
│   │   └── xcuserdata
│   │       └── lithalean.xcuserdatad
│   │           └── UserInterfaceState.xcuserstate
│   └── xcuserdata
│       └── lithalean.xcuserdatad
│           └── xcschemes
│               └── xcschememanagement.plist
├── TreeSitterCpp.xcframework
│   ├── Info.plist
│   ├── ios-arm64
│   │   ├── Headers
│   │   │   └── tree_sitter_cpp.h
│   │   └── libtree-sitter-cpp.a
│   ├── ios-arm64-simulator
│   │   ├── Headers
│   │   │   └── tree_sitter_cpp.h
│   │   └── libtree-sitter-cpp.a
│   └── macos-arm64
│       ├── Headers
│       │   └── tree_sitter_cpp.h
│       └── libtree-sitter-cpp.a
├── TreeSitterRuntime.xcframework
│   ├── Info.plist
│   ├── ios-arm64
│   │   ├── Headers
│   │   │   └── tree_sitter
│   │   │       ├── api.h
│   │   │       └── parser.h
│   │   └── libtree-sitter.a
│   ├── ios-arm64-simulator
│   │   ├── Headers
│   │   │   └── tree_sitter
│   │   │       ├── api.h
│   │   │       └── parser.h
│   │   └── libtree-sitter.a
│   └── macos-arm64
│       ├── Headers
│       │   └── tree_sitter
│       │       ├── api.h
│       │       └── parser.h
│       └── libtree-sitter.a
└── TreeSitterSwift.xcframework
    ├── Info.plist
    ├── ios-arm64
    │   ├── Headers
    │   │   └── tree_sitter_swift.h
    │   └── libtree-sitter-swift.a
    ├── ios-arm64-simulator
    │   ├── Headers
    │   │   └── tree_sitter_swift.h
    │   └── libtree-sitter-swift.a
    └── macos-arm64
        ├── Headers
        │   └── tree_sitter_swift.h
        └── libtree-sitter-swift.a

50 directories, 49 files
lithalean@Mac RealitySyntax % 