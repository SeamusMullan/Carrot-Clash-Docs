# Player Subsystem

This diagram details the Player Subsystem architecture, showing classes, relationships, and state management for player behavior.

## Player Class Hierarchy

```mermaid
classDiagram
    direction TB
    
    class MonoBehaviour {
        <<Unity>>
    }
    
    class PlayerMovement {
        -float moveSpeed
        -float walkSpeed
        -float sprintSpeed
        -float crouchSpeed
        -float jumpForce
        -bool canJump
        -bool isCrouching
        -bool isGrounded
        
        +Move()
        +Sprint()
        +Crouch()
        +Jump()
        +CheckGrounded()
    }
    
    class PlayerStats {
        -float maxHealth
        -float currentHealth
        -float maxShield
        -float currentShield
        -float healthRegenRate
        -float shieldRegenRate
        -bool isDead
        
        +TakeDamage(float amount)
        +Heal(float amount)
        +RegenHealth()
        +RegenShield()
        +Die()
    }
    
    class PlayerGraphics {
        -Animator animator
        -GameObject model
        
        +UpdateAnimations()
        +PlayAnimation(string animName)
    }
    
    class PlayerMultipliers {
        -float damageMultiplier
        -float speedMultiplier
        -float healthMultiplier
        
        +ApplyDamageMultiplier(float damage)
        +ApplySpeedMultiplier(float speed)
        +ApplyHealthMultiplier(float health)
    }
    
    MonoBehaviour <|-- PlayerMovement
    MonoBehaviour <|-- PlayerStats
    MonoBehaviour <|-- PlayerGraphics
    MonoBehaviour <|-- PlayerMultipliers
    
    PlayerStats o-- PlayerMultipliers
    PlayerMovement o-- PlayerMultipliers
```

## Player State Management

```mermaid
classDiagram
    direction TB
    
    class IPlayerState {
        <<interface>>
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class PlayerBaseState {
        #PlayerMovement playerMovement
        #PlayerStats playerStats
        #PlayerStateFactory stateFactory
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
        #GetPlayerMovement()
        #GetPlayerStats()
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
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class PlayerDashState {
        -float dashSpeed
        -float dashTime
        -float dashCooldown
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class PlayerClimbState {
        -float climbSpeed
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class PlayerDeadState {
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
        +Respawn()
    }
    
    class PlayerStateFactory {
        -PlayerDefaultState defaultState
        -PlayerJumpState jumpState
        -PlayerCrouchState crouchState
        -PlayerDashState dashState
        -PlayerClimbState climbState
        -PlayerDeadState deadState
        
        +CreateState(PlayerStates stateType)
    }
    
    class PlayerStates {
        <<enumeration>>
        DEFAULT
        JUMP
        CROUCH
        DASH
        CLIMB
        DEAD
    }
    
    IPlayerState <|.. PlayerBaseState
    PlayerBaseState <|-- PlayerDefaultState
    PlayerBaseState <|-- PlayerJumpState
    PlayerBaseState <|-- PlayerCrouchState
    PlayerBaseState <|-- PlayerDashState
    PlayerBaseState <|-- PlayerClimbState
    PlayerBaseState <|-- PlayerDeadState
    
    PlayerStateFactory --> PlayerBaseState : creates
    PlayerStateFactory --> PlayerStates : uses
```

## Player Input Flow

```mermaid
sequenceDiagram
    participant IM as InputManager
    participant PA as PlayerActions
    participant PM as PlayerMovement
    participant PS as PlayerStateFactory
    participant WC as WeaponController
    
    IM->>PA: Register Input Actions
    
    alt Movement Input
        PA->>IM: OnMove(InputValue value)
        IM->>PM: Process Move Input
        PM->>PS: Determine State Change
    else Jump Input
        PA->>IM: OnJump()
        IM->>PM: Trigger Jump
        PM->>PS: Switch to Jump State
    else Crouch Input
        PA->>IM: OnCrouch()
        IM->>PM: Toggle Crouch
        PM->>PS: Switch to Crouch State
    else Dash Input
        PA->>IM: OnDash()
        IM->>PM: Trigger Dash
        PM->>PS: Switch to Dash State
    else Weapon Input
        PA->>IM: OnFire()
        IM->>WC: Trigger Weapon Fire
    end
```

## Player Component Dependencies

```mermaid
flowchart TB
    subgraph PlayerComponents
        PS[PlayerStats]
        PM[PlayerMovement]
        PG[PlayerGraphics]
        PMult[PlayerMultipliers]
    end
    
    subgraph StateManagement
        PSF[PlayerStateFactory]
        PBS[PlayerBaseState]
    end
    
    subgraph InputHandling
        IM[InputManager]
        PA[PlayerActions]
    end
    
    subgraph WeaponHandling
        WC[WeaponController]
        WS[WeaponStateFactory]
    end
    
    PS --> PSF
    PM --> PSF
    
    PSF --> PBS
    
    IM --> PA
    PA --> PM
    PA --> WC
    
    PM --> PS
    PG --> PS
    PMult --> PS
    PMult --> PM
    
    PM --> WC
    PS --> WC
    
    WC --> WS
```

The player subsystem is a complex structure built on several key components:

1. **Core Player Components**: PlayerMovement, PlayerStats, PlayerGraphics, and PlayerMultipliers handle the fundamental player mechanics
2. **State Management System**: Using the State Pattern with a factory to control player behavior states
3. **Input System Integration**: Shows how player input is processed and routed to appropriate subsystems
4. **Component Dependencies**: Illustrates how the various player components depend on each other

The player state system is particularly important as it enables clean transitions between different player behaviors (running, jumping, crouching, etc.) without cluttering the player controller with conditional logic.