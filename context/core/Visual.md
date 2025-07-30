# RealitySyntax Visual Design Context

**Purpose**: Complete visual design system and UI specifications  
**Version**: 2.0  
**Design Language**: Native SwiftUI + Game-Focused Theming  
**Last Updated**: January 2025

## Visual Hierarchy
```
Z-LAYERS (back→front):
├─[0] Background (Editor background)
├─[1] Content (Code text with syntax highlighting)
├─[2] Controls (Buttons, headers, line numbers)
├─[3] Overlays (Debug panel, diagnostics)
└─[4] Sheets (Modals, alerts, preferences)
```

## Layout Philosophy

### Core Principle: Traditional Code Editor Layout
**Inspector LEFT, Code RIGHT** - Following established patterns from:
- Xcode
- VS Code  
- Visual Studio
- Most professional IDEs

**Important**: This is intentionally OPPOSITE of RealityViewport which uses:
- 3D viewport LEFT
- Inspector panels RIGHT

### Rationale
Game developers expect file trees and navigation on the left when working with code. This muscle memory from their primary tools should be respected. The context switch between 3D editing and code editing is reinforced by the layout flip.

## Quick Reference Tables

### Color Palette

| Element | Light Mode | Dark Mode | Hex | Usage |
|---------|------------|-----------|-----|-------|
| **Background** | systemBackground | systemBackground | Dynamic | Editor main background |
| **Line Numbers BG** | gray.opacity(0.1) | gray.opacity(0.1) | Dynamic | Alternating line backgrounds |
| **Inspector BG** | secondarySystemBackground | secondarySystemBackground | Dynamic | Left panel background |
| **Swift Accent** | orange | orange | #FF9500 | Swift file indicators |
| **C++ Accent** | blue | blue | #007AFF | C++ file indicators |
| **MSL Accent** | purple | purple | #AF52DE | Metal shader indicators |
| **Text Primary** | label | label | Dynamic | Code text |
| **Text Secondary** | secondaryLabel | secondaryLabel | Dynamic | Line numbers, hints |
| **Dividers** | separator | separator | Dynamic | Split view dividers |
| **Error** | red | red | #FF3B30 | Diagnostics, errors |
| **Warning** | orange | orange | #FF9500 | Warnings, issues |
| **Success** | green | green | #34C759 | Parser loaded, success |

### Game-Specific Syntax Colors

| Token Type | Swift | C++ | MSL | Purpose |
|------------|-------|-----|-----|---------|
| **Keywords** | .purple | .purple | .purple | Language keywords |
| **Types** | .cyan | .cyan | .cyan | Type names |
| **Functions** | .blue | .blue | .mint | Function calls |
| **Strings** | .red | .red | .red | String literals |
| **Numbers** | .orange | .orange | .orange | Numeric values |
| **Comments** | .green | .green | .green | Documentation |
| **Attributes** | .orange | .yellow | .pink | Decorators/attributes |
| **GameObject** | .indigo | .indigo | - | Game-specific types |

### Spacing System

| Token | Value | Usage |
|-------|-------|-------|
| **tiny** | 4pt | Icon padding |
| **small** | 8pt | Internal component padding |
| **medium** | 16pt | Component spacing |
| **large** | 24pt | Section gaps |
| **inspector** | 280pt | Default inspector width (macOS) |
| **minEditor** | 400pt | Minimum editor width |
| **debugPanel** | 200pt | Debug panel height |

### Typography Scale

| Style | Size | Weight | Usage |
|-------|------|--------|-------|
| **Code** | 13pt | SF Mono Regular | Editor content |
| **Line Numbers** | 11pt | SF Mono Light | Line number column |
| **File Name** | 14pt | SF Pro Semibold | Inspector file items |
| **Header** | 16pt | SF Pro Bold | Section headers |
| **Status** | 12pt | SF Pro Regular | Parser status text |
| **Debug** | 11pt | SF Mono Regular | Debug panel output |
| **Diagnostic** | 12pt | SF Pro Medium | Error messages |

### Icon System

| Icon | SF Symbol | Size | Usage |
|------|-----------|------|-------|
| **Swift** | swift | 16pt | Swift file icon |
| **C++** | curlybraces | 16pt | C++ file icon |
| **MSL** | cpu | 16pt | Metal shader icon |
| **GameObject** | gamecontroller | 14pt | Game script indicator |
| **Component** | puzzlepiece | 14pt | Game component |
| **Debug** | ladybug | 16pt | Debug panel toggle |
| **Parser OK** | checkmark.circle.fill | 14pt | Parser loaded |
| **Parser Fail** | xmark.circle.fill | 14pt | Parser not loaded |
| **Add** | plus.circle | 20pt | New file button |
| **Search** | magnifyingglass | 16pt | Search field |
| **Error** | exclamationmark.circle | 14pt | Error indicator |
| **Warning** | exclamationmark.triangle | 14pt | Warning indicator |

## Platform-Specific Adaptations

### macOS Design

```
┌─────────────────────────────────────────────────┐
│ Toolbar (New, Search, Debug)                   │
├────────────────┬────────────────────────────────┤
│                │ Editor Header                  │
│                ├────────────────────────────────┤
│   Inspector    │ [Line#] Code content          │
│   (280pt)      │         with syntax           │
│                │         highlighting          │
│                │                                │
│                ├────────────────────────────────┤
│                │ Debug Panel (collapsible)      │
└────────────────┴────────────────────────────────┘
```

**Key Features:**
- Fixed 280pt inspector width
- HSplitView with draggable divider
- Native toolbar with game actions
- Parser status in toolbar
- Hover states on all interactive elements
- Focus ring support
- Window tab support for multiple scripts

### iPad Design

```
┌─────────────────────────────────────────────────┐
│         Navigation Bar                          │
├────────────────┬────────────────────────────────┤
│                │                                │
│   Inspector    │         Code Editor            │
│   (Sidebar)    │                                │
│                │                                │
│                │                                │
└────────────────┴────────────────────────────────┘
```

**Key Features:**
- NavigationSplitView with sidebar
- Collapsible inspector
- Full keyboard support
- Multi-window support
- Drag and drop files

### iPhone Design

```
┌─────────────────────────────────────┐
│     Compact Header (File, Debug)    │
├─────────────────────────────────────┤
│                                     │
│          Code Editor                │
│     (Syntax highlighted)            │
│                                     │
│                                     │
├─────────────────────────────────────┤
│         Bottom Toolbar              │
└─────────────────────────────────────┘

[Inspector slides in from LEFT edge when triggered]
```

**Key Features:**
- Full-screen editor
- Inspector as sliding overlay from LEFT
- Compact header with essentials
- Bottom toolbar with material background
- Safe area respected
- Gesture-based inspector dismissal
- Touch-optimized tap targets (44pt minimum)

### tvOS Design (Read-Only)

```
┌─────────────────────────────────────┐
│          File Browser               │
├─────────────────────────────────────┤
│                                     │
│       Code Viewer                   │
│    (Syntax highlighted)             │
│                                     │
└─────────────────────────────────────┘
```

**Key Features:**
- Focus-based navigation
- Read-only code viewing
- Large, TV-optimized text
- Simple file browser

## Component Specifications

### Parser Status View
```yaml
container:
  background: Material.ultraThinMaterial
  cornerRadius: 8
  padding: horizontal=12, vertical=6
  position: toolbar_trailing (macOS), hidden (iOS)
  
layout:
  HStack:
    spacing: 16
    content: [SwiftSyntaxStatus, LibClangStatus]
    
statusItem:
  icon: SF Symbol (14pt)
  text: Font.caption (12pt) 
  spacing: 4
  colors:
    loaded: green
    notLoaded: secondary
    loading: orange with rotation animation
```

### Editor Header
```yaml
macOS:
  height: 44
  background: Material.bar
  content:
    leading: fileIcon + fileName + modifiedIndicator
    center: scriptStats (lines, size)
    trailing: debugToggle + parserStatus

iOS:
  height: 44  
  background: Material.bar
  content:
    leading: inspectorToggle + fileName
    trailing: debugToggle
```

### Line Number Column
```yaml
width: 
  calculated: max(40, lineCount.digits * characterWidth + 16)
  
appearance:
  font: .monospacedDigit(.system(size: 11))
  color: secondaryLabel
  textAlignment: trailing
  padding: horizontal=8
  
alternatingBackground:
  even: Color.gray.opacity(0.05)
  odd: Color.clear
  
diagnosticIndicators:
  error: red.circle.fill (in gutter)
  warning: orange.triangle.fill (in gutter)
```

### Debug Panel Overlay
```yaml
activation: slideDown animation
position: bottom of editor header
height: 200pt (resizable on macOS)
background: Material.thick
content:
  tabs:
    - Tokens: Show tokenization results
    - Parser: Show parser status and stats
    - Performance: Show timing metrics
    
dismissal: 
  - Toggle button
  - Swipe up (iOS)
  - Escape key (macOS)
```

### Syntax Highlighting Overlay
```yaml
rendering:
  method: AttributedString overlay
  timing: Debounced 100ms after typing
  performance: Async on background queue
  
highlighting:
  keywords: Color based on language
  types: Cyan with semibold
  functions: Blue (Swift/C++), Mint (MSL)
  gameObjects: Indigo with underline
  strings: Red with slight transparency
  comments: Green with italic
```

### Diagnostic Overlays
```yaml
inline_diagnostics:
  error:
    underline: wavy red
    hover: Show full message
    icon: exclamationmark.circle
    
  warning:
    underline: wavy orange  
    hover: Show suggestion
    icon: exclamationmark.triangle
    
gutter_indicators:
  position: After line numbers
  size: 12pt
  interaction: Click to jump to issue
```

## Animation Specifications

### View Transitions
| Animation | Duration | Curve | Usage |
|-----------|----------|-------|-------|
| Inspector Slide | 0.3s | Spring(0.8, 0.7) | iOS inspector |
| Debug Panel | 0.25s | EaseInOut | Debug overlay |
| File Selection | 0.2s | EaseOut | Inspector selection |
| Button Press | 0.1s | EaseIn | All buttons |
| Parser Status | 1.0s | Linear loop | Loading spinner |
| Diagnostic Appear | 0.15s | EaseOut | Error underlines |

### Haptic Feedback (iOS)
- **File Selection**: Selection changed (light)
- **Button Tap**: Impact (light)
- **Delete Confirm**: Warning (medium)
- **Parser Success**: Success (light)
- **Error Appear**: Error (medium)
- **Code Completion**: Selection (light)

## Visual States

### Editor States
```yaml
empty:
  message: "Select a script or create a new one"
  icon: doc.text.viewfinder (48pt, secondary)
  actions: [NewSwiftScript, NewCppScript, NewShader]
  
loading:
  message: "Loading script..."
  progressView: indeterminate
  
error:
  message: errorDescription
  icon: exclamationmark.triangle
  color: red
  action: retry
  
parsing:
  indicator: Small spinner in header
  message: "Analyzing code..."
```

### Parser Status States
```yaml
loaded:
  icon: checkmark.circle.fill
  color: green
  text: "Ready"
  
notLoaded:
  icon: xmark.circle.fill
  color: secondary  
  text: "Not Available"
  
loading:
  icon: arrow.triangle.2.circlepath
  animation: rotating
  color: orange
  text: "Loading..."
  
error:
  icon: exclamationmark.circle
  color: red
  text: "Failed"
```

## Game-Specific Visual Elements

### GameObject Indicators
- Special icon for GameObject-derived classes
- Indigo color coding
- Component badges in inspector

### Performance Warnings
- Yellow background tint for slow functions
- Inline performance metrics
- Heat map overlay mode (future)

### Shader Preview (Future)
- Mini preview of shader output
- Live parameter tweaking
- Performance metrics

## Accessibility

### VoiceOver Labels
- **File items**: "[Language] script: [Name], [Status], [Modified]"
- **Parser status**: "[Parser]: [Status]"
- **Line numbers**: "Line [Number]"
- **Debug panel**: "Debug panel [open/closed]"
- **Diagnostics**: "[Severity]: [Message] on line [Number]"

### Dynamic Type
- Code editor: Fixed size (monospace requirement)
- UI elements: Respect Dynamic Type
- Minimum sizes enforced
- Maximum sizes capped

### Color Contrast
- All text meets WCAG AA standards
- Status indicators have text labels
- Not solely reliant on color
- High contrast mode support

## Design Principles

1. **Game Developer Focused**: UI optimized for game scripting workflows
2. **Performance Visible**: Always show impact of code on game performance  
3. **Language Clarity**: Clear visual distinction between Swift/C++/MSL
4. **Native Feel**: Respect platform conventions while maintaining consistency
5. **Information Density**: Show relevant info without overwhelming

## Future Visual Enhancements

### Planned
- Minimap for code navigation
- Breadcrumb bar for symbol navigation
- Split editor support
- Theme customization
- Performance heat maps
- Inline shader previews

### Under Consideration  
- Code folding with previews
- Git status in gutter
- AI suggestion indicators
- Collaborative editing badges
- Visual breakpoints
- Memory usage indicators

---

*This visual design specification has been updated to reflect the SwiftSyntax/LibClang architecture and game engine focus of RealitySyntax.*