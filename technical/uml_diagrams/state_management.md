# State Management

This diagram details the State Management architecture used throughout Carrot Clash, focusing on the implementation of the State Pattern for player and weapon systems.

## State Pattern Overview

```mermaid
classDiagram
    direction TB
    
    class IState {
        <<interface>>
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class BaseState {
        #Context context
        #StateFactory stateFactory
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class ConcreteStateA {
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class ConcreteStateB {
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class Context {
        -IState currentState
        
        +SwitchState(IState newState)
        +Update()
    }
    
    class StateFactory {
        -Context context
        
        +CreateState(StateEnum stateType)
    }
    
    IState <|.. BaseState
    BaseState <|-- ConcreteStateA
    BaseState <|-- ConcreteStateB
    
    Context o-- IState
    Context --> StateFactory
    StateFactory --> BaseState : creates
```

## Player State Management

```mermaid
classDiagram
    direction TB
    
    class PlayerBaseState {
        #PlayerMovement playerMovement
        #PlayerStats playerStats
        #PlayerStateFactory stateFactory
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class PlayerDefaultState {
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class PlayerJumpState {
        -float jumpTime
        -float maxJumpTime
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class PlayerCrouchState {
        -bool isSliding
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class PlayerDashState {
        -float dashTime
        -Vector3 dashDirection
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class PlayerClimbState {
        -float climbSpeed
        -bool isAtTop
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class PlayerDeadState {
        -float respawnTime
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
        +Respawn()
    }
    
    class PlayerStateFactory {
        -PlayerMovement movement
        -PlayerStats stats
        
        +DefaultState() PlayerBaseState
        +JumpState() PlayerBaseState
        +CrouchState() PlayerBaseState
        +DashState() PlayerBaseState
        +ClimbState() PlayerBaseState
        +DeadState() PlayerBaseState
    }
    
    class PlayerMovement {
        -PlayerBaseState currentState
        
        +SetState(PlayerBaseState newState)
        +Update()
    }
    
    PlayerBaseState <|-- PlayerDefaultState
    PlayerBaseState <|-- PlayerJumpState
    PlayerBaseState <|-- PlayerCrouchState
    PlayerBaseState <|-- PlayerDashState
    PlayerBaseState <|-- PlayerClimbState
    PlayerBaseState <|-- PlayerDeadState
    
    PlayerMovement o-- PlayerBaseState
    PlayerMovement --> PlayerStateFactory
    PlayerStateFactory --> PlayerBaseState : creates
```

## Weapon State Management

```mermaid
classDiagram
    direction TB
    
    class WeaponBaseState {
        #WeaponController weaponController
        #WeaponStateFactory stateFactory
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class WeaponDefaultState {
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class WeaponShootingState {
        -float fireTimer
        -bool isAutomatic
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class WeaponReloadState {
        -float reloadTimer
        -bool isReloadCancellable
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class WeaponInspectState {
        -float inspectDuration
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class MeleeState {
        -float attackDuration
        -int comboCounter
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class WeaponStateFactory {
        -WeaponController controller
        
        +DefaultState() WeaponBaseState
        +ShootingState() WeaponBaseState
        +ReloadState() WeaponBaseState
        +InspectState() WeaponBaseState
        +MeleeState() WeaponBaseState
    }
    
    class WeaponController {
        -WeaponBaseState currentState
        
        +SetState(WeaponBaseState newState)
        +Update()
    }
    
    WeaponBaseState <|-- WeaponDefaultState
    WeaponBaseState <|-- WeaponShootingState
    WeaponBaseState <|-- WeaponReloadState
    WeaponBaseState <|-- WeaponInspectState
    WeaponBaseState <|-- MeleeState
    
    WeaponController o-- WeaponBaseState
    WeaponController --> WeaponStateFactory
    WeaponStateFactory --> WeaponBaseState : creates
```

## State Transition Diagram - Player

```mermaid
stateDiagram-v2
    [*] --> Default
    
    Default --> Jump: Press Jump Button
    Jump --> Default: Land on Ground
    
    Default --> Crouch: Press Crouch Button
    Crouch --> Default: Release Crouch Button
    
    Default --> Dash: Press Dash Button
    Dash --> Default: Dash Completed
    
    Default --> Climb: Near Climbable Surface
    Climb --> Default: Reach Top/Release Climb
    
    Default --> Dead: Health <= 0
    Jump --> Dead: Health <= 0
    Crouch --> Dead: Health <= 0
    Dash --> Dead: Health <= 0
    Climb --> Dead: Health <= 0
    
    Dead --> Default: Respawn
```

## State Transition Diagram - Weapon

```mermaid
stateDiagram-v2
    [*] --> Default
    
    Default --> Shooting: Press Fire Button
    Shooting --> Default: Complete Shot/Release Fire
    
    Default --> Reloading: Press Reload/Empty Ammo
    Reloading --> Default: Reload Complete
    
    Default --> Inspecting: Press Inspect Button
    Inspecting --> Default: Inspection Complete
    
    Default --> Melee: Press Melee Button
    Melee --> Default: Melee Attack Complete
    
    Inspecting --> Shooting: Press Fire Button
    Inspecting --> Reloading: Press Reload Button
    Inspecting --> Melee: Press Melee Button
    
    Shooting --> Reloading: Empty Ammo
    Reloading --> Shooting: Cancel Reload + Press Fire
```

## State Decision Flow

```mermaid
flowchart TB
    subgraph PlayerStateDecision["Player State Decision Flow"]
        InputCheck["Check Input"]
        EnvironmentCheck["Check Environment"]
        StateConditions["Evaluate State Conditions"]
        CurrentState["Get Current State"]
        SwitchState["Switch State if Needed"]
        
        InputCheck --> StateConditions
        EnvironmentCheck --> StateConditions
        CurrentState --> StateConditions
        StateConditions --> SwitchState
    end
    
    subgraph WeaponStateDecision["Weapon State Decision Flow"]
        WeaponInputCheck["Check Weapon Input"]
        WeaponConditions["Evaluate Weapon Conditions"]
        WeaponCurrentState["Get Current Weapon State"]
        AmmoCheck["Check Ammunition"]
        WeaponSwitchState["Switch Weapon State if Needed"]
        
        WeaponInputCheck --> WeaponConditions
        AmmoCheck --> WeaponConditions
        WeaponCurrentState --> WeaponConditions
        WeaponConditions --> WeaponSwitchState
    end
    
    PlayerStateDecision -.-> WeaponStateDecision
```

The State Pattern implementation in Carrot Clash offers several key advantages:

1. **Clean Separation of Behaviors**: Each state encapsulates specific behavior, making the code more organized and maintainable
2. **Reduced Conditional Logic**: Instead of complex if/else chains, behavior is determined by the current state object
3. **Easier State Transitions**: State transitions are handled by the state objects themselves through their CheckSwitchState method
4. **Extensibility**: New states can be added with minimal changes to existing code
5. **Reusability**: The pattern is applied consistently across different systems (player and weapons)

State management is a core architectural pattern in the game, handling:
- Player movement states (running, jumping, crouching, etc.)
- Weapon operation states (shooting, reloading, inspecting)
- Clear transition rules between states
- Hierarchical relationships between states

This approach results in more maintainable code that can be extended with new behaviors without disrupting existing functionality, while keeping the core logic of each component clean and focused.