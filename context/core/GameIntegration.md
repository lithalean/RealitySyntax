# RealitySyntax Game Integration Documentation

**Purpose**: Document game engine scripting workflows and integration patterns  
**Version**: 1.0  
**Context**: Part of Orchard Game Engine ecosystem  
**Last Updated**: January 2025

## Orchard Game Engine Architecture

RealitySyntax is the scripting module within the Orchard ecosystem:

```
┌─────────────────────────────────────────────────────┐
│                 Orchard Game Engine                 │
│                                                     │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐│
│  │  Reality    │  │  Reality    │  │  Reality    ││
│  │  Viewport   │  │  Syntax     │  │  Assets     ││
│  │             │  │             │  │             ││
│  │ 3D Editing  │  │Script Editor│  │Asset Pipeline││
│  └─────────────┘  └─────────────┘  └─────────────┘│
│                          │                          │
│  ┌───────────────────────┴─────────────────────┐  │
│  │              GameCore Runtime               │  │
│  │         (Engine without Editor)             │  │
│  └─────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────┘
```

## Language Roles in Game Development

### Swift - Gameplay & Logic Layer
**Primary Use**: High-level game logic, AI, game systems

```swift
// Example: Player Controller in Swift
import GameCore

class PlayerController: GameObject {
    @Published var health: Float = 100.0
    @Published var position: SIMD3<Float> = .zero
    
    func update(deltaTime: Float) {
        // Game logic in Swift
        handleInput()
        updatePhysics(deltaTime)
        checkCollisions()
    }
    
    func takeDamage(_ amount: Float) {
        health -= amount
        if health <= 0 {
            triggerDeathSequence()
        }
    }
}
```

**Why Swift for Gameplay**:
- Type safety prevents runtime errors
- Property wrappers for reactive game state
- Clean syntax for game designers
- Native performance on Apple platforms
- Easy integration with iOS/macOS features

### C++ - Performance Layer
**Primary Use**: Physics, rendering optimizations, engine internals

```cpp
// Example: Particle System in C++
#include <GameCore/ParticleSystem.h>
#include <simd/simd.h>

class ParticleEmitter : public GameObject {
private:
    std::vector<Particle> particles;
    float emissionRate = 100.0f;
    
public:
    void update(float deltaTime) override {
        // Performance-critical update
        emitParticles(deltaTime);
        
        // SIMD optimized particle updates
        #pragma clang loop vectorize(enable)
        for (auto& particle : particles) {
            particle.position += particle.velocity * deltaTime;
            particle.life -= deltaTime;
        }
        
        // Remove dead particles
        particles.erase(
            std::remove_if(particles.begin(), particles.end(),
                [](const Particle& p) { return p.life <= 0; }),
            particles.end()
        );
    }
};
```

**Why C++ for Performance**:
- Direct memory management
- SIMD optimizations
- Minimal overhead
- Integration with existing game libraries
- Cross-platform if needed

### MSL - Shader Programming
**Primary Use**: GPU shaders for rendering effects

```metal
// Example: Toon Shader in MSL
#include <metal_stdlib>
using namespace metal;

struct VertexOut {
    float4 position [[position]];
    float3 normal;
    float2 texCoord;
};

fragment float4 toonShading(VertexOut in [[stage_in]],
                           texture2d<float> albedo [[texture(0)]],
                           constant float3& lightDir [[buffer(0)]]) {
    // Sample texture
    float4 color = albedo.sample(sampler(), in.texCoord);
    
    // Calculate toon shading
    float NdotL = dot(normalize(in.normal), -lightDir);
    float intensity = step(0.5, NdotL) * 0.5 + 0.5;
    
    return color * intensity;
}
```

**Why MSL for Shaders**:
- Native Metal performance
- Direct GPU programming
- Advanced rendering effects
- Compute shaders for gameplay

## Common Game Scripting Patterns

### Component System Integration
```swift
// Swift component definition
@Component
class HealthComponent: GameComponent {
    var maxHealth: Float = 100
    var currentHealth: Float = 100
    
    func heal(_ amount: Float) {
        currentHealth = min(currentHealth + amount, maxHealth)
    }
}

// Usage in game object
class Enemy: GameObject {
    @Attached var health: HealthComponent
    @Attached var ai: AIComponent
    @Attached var renderer: MeshRenderer
}
```

### Event System
```swift
// Game events in Swift
enum GameEvent {
    case playerDied(position: SIMD3<Float>)
    case levelCompleted(score: Int)
    case itemCollected(item: Item)
}

// Event handling
class GameManager: EventListener {
    func handleEvent(_ event: GameEvent) {
        switch event {
        case .playerDied(let position):
            spawnDeathEffect(at: position)
            showGameOverUI()
        case .levelCompleted(let score):
            saveHighScore(score)
            loadNextLevel()
        case .itemCollected(let item):
            updateInventory(with: item)
        }
    }
}
```

### State Machines
```swift
// AI State machine in Swift
enum EnemyState {
    case idle
    case patrol(waypoints: [SIMD3<Float>])
    case chase(target: GameObject)
    case attack
}

## Editor Integration Workflows

### Script Creation Flow
1. **New Script from Template**
   ```swift
   // RealitySyntax provides game-specific templates
   Templates:
   - Player Controller (Swift)
   - Enemy AI (Swift)
   - Weapon System (Swift)
   - Particle System (C++)
   - Physics Optimizer (C++)
   - Surface Shader (MSL)
   - Post Process Effect (MSL)
   ```

2. **Automatic GameObject Binding**
   ```swift
   // Scripts automatically integrate with GameCore
   @ScriptableObject
   class PlayerController: GameObject {
       // Auto-generated inspector properties
       @InspectorField var moveSpeed: Float = 5.0
       @InspectorField var jumpHeight: Float = 2.0
   }
   ```

### Live Scripting Workflow

#### Hot Reload (Planned)
```yaml
Development Mode:
  1. Edit script in RealitySyntax
  2. Save triggers compilation
  3. GameCore hot-swaps component
  4. See changes without restart
  
Production Mode:
  1. Scripts compiled AOT
  2. Optimized for release
  3. No runtime compilation
```

#### Script Validation
```swift
// RealitySyntax validates game scripts
Validation Checks:
- Component attribute usage
- GameObject inheritance
- Required method implementations
- Performance warnings
```

### Cross-Module Communication

#### RealitySyntax ↔ RealityViewport
```swift
// Selecting object in viewport shows attached scripts
ViewportSelection -> RealitySyntax:
  - List attached scripts
  - Open script for editing
  - Show component properties

// Double-click script opens in viewport
ScriptSelection -> RealityViewport:
  - Focus game object
  - Highlight in scene
  - Show in hierarchy
```

#### RealitySyntax ↔ RealityAssets
```swift
// Asset references in scripts
@ScriptableObject
class EnemySpawner: GameObject {
    @AssetReference var enemyPrefab: Prefab
    @AssetReference var spawnEffect: ParticleSystem
    @AssetReference var spawnSound: AudioClip
}

// Clicking asset opens in RealityAssets
// Dragging from RealityAssets creates reference
```

## Performance Profiling Integration

### Script Performance Metrics
```swift
// Performance overlay in RealitySyntax
Script Performance:
├── PlayerController.swift
│   ├── update(): 0.23ms avg
│   ├── handleInput(): 0.05ms
│   └── Memory: 1.2KB
├── ParticleSystem.cpp
│   ├── update(): 0.89ms avg
│   ├── Particles: 5000
│   └── Memory: 156KB
```

### Optimization Hints
```swift
// RealitySyntax provides performance hints
⚠️ Performance Warning:
- Array allocation in update loop
- Consider pre-allocating

💡 Optimization Suggestion:
- Use SIMD types for vector math
- Enable whole module optimization
```

## Build Integration

### Compilation Pipeline
```
RealitySyntax (Edit) 
    ↓
Script Compiler (Validate)
    ↓
GameCore (Link)
    ↓
Runtime (Execute)
```

### Build Configurations
```yaml
Debug Build:
  - Full debug symbols
  - Assertions enabled
  - Hot reload support
  - Performance profiling

Release Build:
  - Optimized code
  - Stripped symbols
  - AOT compilation
  - Metal shader compilation
```

## Common Game Programming Tasks

### Input Handling
```swift
// Swift input handling
class PlayerController: GameObject {
    func handleInput() {
        let input = InputManager.shared
        
        // Movement
        let moveX = input.getAxis(.horizontal)
        let moveZ = input.getAxis(.vertical)
        velocity = normalize(float3(moveX, 0, moveZ)) * moveSpeed
        
        // Actions
        if input.isPressed(.jump) && isGrounded {
            velocity.y = jumpVelocity
        }
        
        if input.isPressed(.fire) {
            fireWeapon()
        }
    }
}
```

### Physics Integration
```cpp
// C++ physics optimization
class PhysicsOptimizer : public GameObject {
    void updatePhysics(float deltaTime) {
        // Broad phase collision detection
        spatialHash.update(allColliders);
        
        // Narrow phase with SIMD
        for (auto& pair : spatialHash.getPotentialCollisions()) {
            if (checkDetailedCollision(pair.first, pair.second)) {
                resolveCollision(pair.first, pair.second);
            }
        }
    }
};
```

### Rendering Effects
```metal
// MSL post-processing
kernel void bloomEffect(
    texture2d<float, access::read> input [[texture(0)]],
    texture2d<float, access::write> output [[texture(1)]],
    constant BloomParams& params [[buffer(0)]],
    uint2 gid [[thread_position_in_grid]]) {
    
    // Gaussian blur for bloom
    float4 color = float4(0);
    for (int x = -params.radius; x <= params.radius; x++) {
        for (int y = -params.radius; y <= params.radius; y++) {
            float2 offset = float2(x, y);
            float weight = gaussian(length(offset), params.sigma);
            color += input.read(gid + offset) * weight;
        }
    }
    
    output.write(color, gid);
}
```

## Debugging Game Scripts

### Debug Visualization
```swift
// Debug drawing from scripts
class AIDebugger: GameObject {
    @DebugOnly var showPaths = true
    
    func debugDraw() {
        if showPaths {
            // Draw AI navigation mesh
            DebugRenderer.drawMesh(navigationMesh, color: .blue)
            
            // Draw current path
            DebugRenderer.drawPath(currentPath, color: .yellow)
            
            // Draw detection radius
            DebugRenderer.drawSphere(position, detectionRadius, color: .red)
        }
    }
}
```

### Console Integration
```swift
// Game console commands
@ConsoleCommand("spawn")
func spawnEntity(type: String, position: SIMD3<Float>?) {
    let pos = position ?? playerPosition
    EntityManager.spawn(type, at: pos)
}

@ConsoleCommand("god")
func toggleGodMode() {
    player.invincible = !player.invincible
    print("God mode: \(player.invincible)")
}
```

## Script Organization

### Recommended Project Structure
```
GameProject/
├── Scripts/
│   ├── Player/
│   │   ├── PlayerController.swift
│   │   ├── PlayerInventory.swift
│   │   └── PlayerStats.swift
│   ├── Enemies/
│   │   ├── EnemyAI.swift
│   │   ├── EnemySpawner.swift
│   │   └── BossController.swift
│   ├── Systems/
│   │   ├── GameManager.swift
│   │   ├── SaveSystem.swift
│   │   └── AudioManager.swift
│   ├── Performance/
│   │   ├── ParticleSystem.cpp
│   │   ├── PhysicsOptimizer.cpp
│   │   └── LODSystem.cpp
│   └── Shaders/
│       ├── CharacterShader.metal
│       ├── EnvironmentShader.metal
│       └── PostProcess.metal
```

## Best Practices

### Swift Game Scripts
1. Use `@Published` for observable game state
2. Leverage value types for data
3. Avoid allocations in update loops
4. Use async/await for loading operations
5. Profile with Instruments

### C++ Performance Code
1. Use SIMD types for math
2. Cache-friendly data layouts
3. Avoid virtual functions in hot paths
4. Profile-guided optimization
5. Memory pool allocations

### MSL Shaders
1. Minimize texture samples
2. Use half precision where possible
3. Avoid dynamic branching
4. Leverage threadgroup memory
5. Profile with Metal debugger

## Future Enhancements

### Planned Features
1. **Visual Scripting** - Node-based alternative
2. **Script Packages** - Shareable game systems
3. **AI Behavior Trees** - Visual AI editing
4. **Performance Analyzer** - Real-time profiling
5. **Script Marketplace** - Community content

### Integration Improvements
1. **Seamless Debugging** - Breakpoints across modules
2. **Asset Preview** - See assets in script editor
3. **Live Collaboration** - Multi-user editing
4. **Cloud Scripts** - Server-side game logic
5. **Script Versioning** - Built-in git support

---

*This document outlines how RealitySyntax serves as the scripting hub for Orchard game development, enabling developers to create games using the right language for each task.*