# RealitySyntax Navigation Context

**Purpose**: Screen flow, user journeys, and state management for game script editing  
**Version**: 2.0  
**Navigation Pattern**: Master-Detail with Game-Specific Workflows  
**Last Updated**: January 2025

## Navigation State Machine
```
┌─────────────┐     Select      ┌─────────────┐     Parse      ┌─────────────┐
│   No File   │ ─────────────> │  Editing    │ ─────────────> │  Analyzing  │
│  Selected   │                 │   File      │                 │   Code      │
└─────────────┘                 └─────────────┘                 └─────────────┘
       ▲                               │                               │
       │         Close/Delete          │      Complete                 │
       └───────────────────────────────┴───────────────────────────────┘

States:
- No File Selected: Empty editor, creation prompts
- Editing File: Active file loaded, syntax highlighted
- Analyzing Code: Parser running (background)
- Debug Mode: Overlay panel visible (substate)
```

## Screen Hierarchy
```
RealitySyntaxApp
├── ContentView (Platform Router)
│   ├── MacView (macOS)
│   │   └── HSplitView
│   │       ├── InspectorView
│   │       │   ├── OutlinerView (Script Browser)
│   │       │   └── PropertiesView (Script Info)
│   │       └── SyntaxView
│   │           └── [Language]ScriptEditor
│   │               ├── SyntaxHighlightOverlay
│   │               └── DebugPanel (overlay)
│   │
│   ├── iPadView (iPadOS)
│   │   └── NavigationSplitView
│   │       ├── Sidebar (Inspector)
│   │       └── Detail (Editor)
│   │
│   ├── iPhoneView (iOS)
│   │   ├── SyntaxView (full screen)
│   │   │   └── [Language]ScriptEditor
│   │   └── InspectorView (sheet/overlay)
│   │
│   └── TVView (tvOS)
│       └── NavigationView
│           ├── FileListView
│           └── CodeViewer (read-only)
```

## Component Relationships

### Data Flow Hierarchy
```
SyntaxManager (@StateObject)
    ├── Parser Status (SwiftSyntax, LibClang)
    └── Language Support Flags
         ↓
SyntaxFileManager (@StateObject)
    ├── Script Files Array
    └── Selected Script Binding
         ↓
InspectorView
    ├── OutlinerView (File List)
    └── PropertiesView (File Info)
         ↓
SyntaxView
    ├── Language Detection
    └── Editor Selection
         ↓
[Language]ScriptEditor
    ├── Syntax Highlighting
    ├── Debug Panel
    └── Diagnostics Display
```

### Bridge Integration Flow
```
User Types Code
    ↓
Editor Debounces (100ms)
    ↓
SyntaxManager Routes by Language
    ↓
Language Bridge Tokenizes
    ↓
Highlighting Applied
    ↓
Diagnostics Updated (if available)
```

## Game Development User Journeys

### Primary Flow: Create Game Script
```
START 
  → Launch RealitySyntax
  → Click New Script (+)
  → Choose Template:
    - GameObject Controller (Swift)
    - Performance System (C++)
    - Surface Shader (MSL)
  → Template Loaded in Editor
  → Modify for Specific Needs
  → See Syntax Highlighting
  → Check Debug Panel for Issues
  → Save to Project
END
```

### GameObject Scripting Flow
```
START
  → Create New Swift Script
  → Type "class Player: GameObject"
  → See GameObject Highlighted
  → Add @Published Properties
  → Implement update() Method
  → Add Component Attributes
  → Validate Against GameCore API
END
```

### Performance Optimization Flow
```
START
  → Open Performance-Critical Code
  → Switch to C++ Editor
  → View Performance Metrics (future)
  → Identify Slow Functions
  → Rewrite in C++ for Speed
  → Compare Before/After
  → Debug Panel Shows Timing
END
```

### Shader Development Flow
```
START
  → Create New MSL Shader
  → Choose Shader Template
  → Edit Shader Code
  → See MSL Syntax Highlighting
  → Preview Results (future)
  → Adjust Parameters
  → Export to RealityViewport
END
```

### Cross-Module Navigation

#### From RealityViewport → RealitySyntax
```
3D Scene Selection
  → GameObject Selected
  → "Edit Scripts" Action
  → RealitySyntax Opens
  → Shows Attached Scripts
  → User Edits Script
  → Changes Reflect in Scene
```

#### From RealitySyntax → RealityAssets
```
Script References Asset
  → @AssetReference Property
  → Click Asset Name
  → RealityAssets Opens
  → Asset Selected
  → User Can Modify
  → Reference Updated
```

## Platform-Specific Navigation

### macOS Navigation
```yaml
inspector:
  visibility: Always visible
  width: Fixed 280pt
  resize: Draggable divider
  
fileSelection:
  trigger: Single click
  multiSelect: Cmd+Click (future)
  doubleClick: Open in new tab
  
debugPanel:
  activation: Toolbar button or Cmd+D
  dismissal: Same as activation
  position: Bottom of editor
  resize: Draggable height
  
keyboard:
  - Cmd+N: New file menu
  - Cmd+Shift+N: New from template
  - Cmd+D: Toggle debug panel
  - Cmd+F: Search in file
  - Cmd+Shift+F: Search in project
  - Cmd+R: Run script (future)
  - Cmd+B: Build project (future)
```

### iPad Navigation
```yaml
inspector:
  visibility: Collapsible sidebar
  activation: Sidebar button
  dismissal: Tap collapse
  gestures: Swipe from left edge
  
fileSelection:
  trigger: Tap
  longPress: Context menu
  
splitView:
  modes: [.oneBesideSecondary, .oneOverSecondary]
  adaptive: Based on orientation
  
multitasking:
  splitScreen: Supported
  slideOver: Supported
  multiWindow: Yes
```

### iPhone Navigation
```yaml
inspector:
  visibility: Hidden by default
  activation: Left edge swipe OR hamburger menu
  dismissal: Tap outside OR swipe left
  presentation: Sheet with dimmed background
  
fileSelection:
  trigger: Tap
  feedback: Haptic light
  animation: Slide transition
  
debugPanel:
  activation: Toolbar button
  dismissal: Swipe down
  presentation: Modal sheet
  
gestures:
  - Swipe right: Show inspector
  - Swipe left: Hide inspector  
  - Swipe down: Hide debug panel
  - Pinch: Zoom text (future)
```

### tvOS Navigation
```yaml
navigation:
  style: Focus-based
  remoteControl: Siri Remote
  
fileList:
  selection: Focus + click
  scrolling: Vertical only
  preview: Show first 10 lines
  
codeViewer:
  scrolling: Both directions
  zoom: Play/Pause button
  readOnly: Always
```

## Navigation Quick Reference

| From | To | Trigger | Platform | Animation |
|------|-----|---------|----------|-----------|
| Any | Inspector | Swipe right | iOS | Slide 0.3s |
| Inspector | Editor | Select file | All | Instant |
| Editor | Debug | Debug button | All | Slide down 0.25s |
| Any | New Script | + Button | All | Modal/Sheet |
| Script List | Template | Choose type | All | Push |
| Editor | Parser Status | Automatic | All | Fade 0.15s |
| Any | Search | Cmd/Ctrl+F | Desktop | Focus field |

## State Management Patterns

### File Selection State
```swift
// In SyntaxFileManager
@Published var selectedScript: SyntaxFile?
@Published var scripts: [SyntaxFile] = []

// In OutlinerView  
@Binding var selection: SyntaxFile?

// In SyntaxView
if let script = fileManager.selectedScript {
    // Show appropriate editor based on language
    switch script.language {
    case .swift: SwiftScriptEditor(script: script)
    case .cpp, .msl: CppScriptEditor(script: script)
    }
} else {
    EmptyStateView()
}
```

### Parser State Management
```swift
// In SyntaxManager
@Published var swiftSyntaxStatus: ParserStatus = .checking
@Published var libClangStatus: ParserStatus = .checking

enum ParserStatus {
    case checking
    case loaded
    case notAvailable
    case error(String)
}
```

### Template System
```swift
// Template selection
enum ScriptTemplate {
    case gameObject(language: Language)
    case component(language: Language)
    case system(language: Language)
    case shader(type: ShaderType)
    
    var content: String {
        // Return template content
    }
}
```

## Deep Linking Structure (Future)

### URL Scheme Design
```
realitysyntax://
├── open/[fileId]              Open specific script
├── create/[template]          Create from template
├── debug/[fileId]            Open with debug panel
├── search/[query]            Search in project
├── performance/[fileId]      Show performance metrics
└── asset/[assetId]          Navigate to asset reference