# High-Level System Architecture

This diagram represents the top-level architecture of the Carrot Clash game, showing major subsystems and their interactions.

## System Components Overview

```mermaid
classDiagram
    direction TB
    
    class GameManager {
        <<Core>>
        -Manages game state
        -Orchestrates subsystems
    }
    
    class PlayerSystem {
        <<Subsystem>>
        -Handles player mechanics
        -Manages player state
    }
    
    class WeaponSystem {
        <<Subsystem>>
        -Manages weapons
        -Handles combat mechanics
    }
    
    class EnemySystem {
        <<Subsystem>>
        -Controls enemy behavior
        -Manages enemy spawning
    }
    
    class UISystem {
        <<Subsystem>>
        -Manages user interface
        -Handles player feedback
    }
    
    class InputSystem {
        <<Subsystem>>
        -Processes player input
        -Maps to game actions
    }
    
    class EnvironmentSystem {
        <<Subsystem>>
        -Manages world interactions
        -Controls environmental effects
    }
    
    class SoundSystem {
        <<Subsystem>>
        -Handles audio playback
        -Manages sound effects
    }
    
    class PoolManager {
        <<Service>>
        -Object pooling
        -Performance optimization
    }
    
    class GraphyMonitor {
        <<Service>>
        -Performance monitoring
        -Debug visualization
    }
    
    GameManager --> PlayerSystem
    GameManager --> WeaponSystem
    GameManager --> EnemySystem
    GameManager --> UISystem
    GameManager --> EnvironmentSystem
    GameManager --> SoundSystem
    
    PlayerSystem --> InputSystem
    PlayerSystem --> WeaponSystem
    
    UISystem ..> PlayerSystem : displays info
    UISystem ..> WeaponSystem : displays info
    
    WeaponSystem --> PoolManager : uses
    EnemySystem --> PoolManager : uses
    
    GraphyMonitor ..> GameManager : monitors
```

## Subsystem Dependency Diagram

```mermaid
flowchart TB
    subgraph Core["Core Systems"]
        GM["GameManager"]
        PM["PoolManager"]
        SM["SoundManager"]
        GS["GameSettingsManager"]
    end
    
    subgraph Player["Player Systems"]
        PS["PlayerStats"]
        PM2["PlayerMovement"]
        PG["PlayerGraphics"]
        PSF["PlayerStateFactory"]
    end
    
    subgraph Weapons["Weapon Systems"]
        WC["WeaponController"]
        WA["WeaponAnimator"]
        WSF["WeaponStateFactory"]
        ATT["Attachments"]
    end
    
    subgraph Input["Input Systems"]
        IM["InputManager"]
        PA["PlayerActions"]
    end
    
    subgraph UI["UI Systems"]
        UIC["UIController"]
        UIE["UIEvents"]
        CH["Crosshair"]
        HM["Hitmarker"]
        TNM["ToastNotificationMessage"]
    end
    
    subgraph Enemies["Enemy Systems"]
        IDam["IDamageable"]
        EH["EnemyHealth"]
        TT["TrainingTarget"]
        TUR["Turret"]
    end
    
    subgraph PickUps["Pickup Systems"]
        IM2["InteractManager"]
        INT["Interactable"]
        PIC["Pickeable"]
    end
    
    subgraph Performance["Performance Monitoring"]
        GM2["GraphyManager"]
        GD["GraphyDebugger"]
    end
    
    Core --> Player
    Core --> Weapons
    Core --> Enemies
    
    Player --> Input
    Player --> Weapons
    
    Weapons --> PickUps
    
    UI --> Player
    UI --> Weapons
    
    Performance -.-> Core
```

## Communication Flow Diagram

```mermaid
sequenceDiagram
    participant Player
    participant InputManager
    participant PlayerStateFactory
    participant WeaponController
    participant EnemySystem
    participant UIController
    
    Player->>InputManager: Process Input
    InputManager->>PlayerStateFactory: Determine State
    PlayerStateFactory->>Player: Update State
    
    alt Combat Interaction
        Player->>WeaponController: Fire Weapon
        WeaponController->>EnemySystem: Apply Damage
        EnemySystem->>UIController: Show Hitmarker
        UIController->>Player: Visual Feedback
    else Movement Interaction
        Player->>PlayerStateFactory: Change Movement State
        PlayerStateFactory->>Player: Apply Movement
        Player->>UIController: Update UI Elements
    end
```

The diagrams above illustrate the high-level architecture of the Carrot Clash game system:

1. **System Components Overview**: Shows the major subsystems and their relationships
2. **Subsystem Dependency Diagram**: Illustrates how the various subsystems depend on each other
3. **Communication Flow Diagram**: Demonstrates how data and commands flow between components during typical gameplay operations

These diagrams provide a foundation for understanding the overall system architecture before diving into more detailed subsystem diagrams.