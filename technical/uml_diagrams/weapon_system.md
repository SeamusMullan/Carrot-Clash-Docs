# Weapon System

This diagram details the Weapon System architecture, showing classes, relationships, and state management for weapons, attachments, and combat mechanics.

## Weapon Class Structure

```mermaid
classDiagram
    direction TB
    
    class MonoBehaviour {
        <<Unity>>
    }
    
    class WeaponController {
        -Weapon_SO weaponData
        -float currentAmmo
        -float totalAmmo
        -bool isReloading
        -bool canShoot
        -float nextTimeToFire
        
        +Shoot()
        +Reload()
        +SwitchWeapon(Weapon_SO newWeapon)
        +AddAmmo(int amount)
        +CanShoot() bool
        +Inspect()
    }
    
    class WeaponAnimator {
        -Animator animator
        -WeaponController weaponController
        
        +PlayShootAnimation()
        +PlayReloadAnimation()
        +PlaySwitchAnimation()
        +PlayInspectAnimation()
    }
    
    class Weapon_SO {
        <<ScriptableObject>>
        -string weaponName
        -WeaponType weaponType
        -float damage
        -float fireRate
        -float range
        -int maxAmmo
        -int maxTotalAmmo
        -bool autoReload
        -AudioClip shootSound
        -AudioClip reloadSound
        -GameObject projectile
        
        +GetWeaponStats()
    }
    
    class WeaponType {
        <<enumeration>>
        PISTOL
        RIFLE
        SHOTGUN
        SMG
        SNIPER
        MELEE
        EXPLOSIVE
    }
    
    class Bullet {
        -float damage
        -float speed
        -float lifeTime
        -bool hasHit
        
        +Initialize(float damage, float speed)
        +OnCollisionEnter(Collision collision)
    }
    
    class WeaponIdentification {
        -string weaponID
        -Weapon_SO weaponData
        
        +GetWeaponData() Weapon_SO
    }
    
    class WeaponEffects {
        -ParticleSystem muzzleFlash
        -TrailRenderer bulletTrail
        -GameObject impactEffect
        
        +PlayMuzzleFlash()
        +SpawnBulletTrail(Vector3 startPos, Vector3 endPos)
        +PlayImpactEffect(Vector3 position, Quaternion rotation)
    }
    
    MonoBehaviour <|-- WeaponController
    MonoBehaviour <|-- WeaponAnimator
    MonoBehaviour <|-- Bullet
    MonoBehaviour <|-- WeaponIdentification
    MonoBehaviour <|-- WeaponEffects
    
    WeaponController o-- Weapon_SO
    WeaponController o-- WeaponAnimator
    WeaponController o-- WeaponEffects
    
    Weapon_SO --> WeaponType
    WeaponIdentification --> Weapon_SO
    
    WeaponController --> Bullet : creates
```

## Weapon State System

```mermaid
classDiagram
    direction TB
    
    class IWeaponState {
        <<interface>>
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
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
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
        -HandleShooting()
    }
    
    class WeaponReloadState {
        -float reloadTimer
        -float reloadTime
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
        -HandleReloading()
    }
    
    class WeaponInspectState {
        -float inspectTime
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
    }
    
    class MeleeState {
        -float meleeTimer
        -float meleeDamage
        -float meleeRange
        -float meleeAngle
        
        +EnterState()
        +ExitState()
        +UpdateState()
        +CheckSwitchState()
        -PerformMeleeAttack()
    }
    
    class WeaponStateFactory {
        -WeaponDefaultState defaultState
        -WeaponShootingState shootingState
        -WeaponReloadState reloadState
        -WeaponInspectState inspectState
        -MeleeState meleeState
        
        +CreateState(WeaponStates stateType)
    }
    
    class WeaponStates {
        <<enumeration>>
        DEFAULT
        SHOOTING
        RELOADING
        INSPECTING
        MELEE
    }
    
    IWeaponState <|.. WeaponBaseState
    WeaponBaseState <|-- WeaponDefaultState
    WeaponBaseState <|-- WeaponShootingState
    WeaponBaseState <|-- WeaponReloadState
    WeaponBaseState <|-- WeaponInspectState
    WeaponBaseState <|-- MeleeState
    
    WeaponStateFactory --> WeaponBaseState : creates
    WeaponStateFactory --> WeaponStates : uses
```

## Attachment System

```mermaid
classDiagram
    direction TB
    
    class Attachment {
        <<abstract>>
        #string attachmentName
        #GameObject attachmentModel
        #WeaponController weaponController
        
        +ApplyAttachment()
        +RemoveAttachment()
    }
    
    class Scope {
        -float zoomFactor
        -float aimSpeed
        
        +ApplyAttachment()
        +RemoveAttachment()
        +AimDownSight()
    }
    
    class Barrel {
        -float damageModifier
        -float rangeModifier
        -float recoilModifier
        
        +ApplyAttachment()
        +RemoveAttachment()
    }
    
    class Grip {
        -float stabilityModifier
        -float recoilModifier
        
        +ApplyAttachment()
        +RemoveAttachment()
    }
    
    class Stock {
        -float accuracyModifier
        -float recoilModifier
        
        +ApplyAttachment()
        +RemoveAttachment()
    }
    
    class Magazine {
        -int additionalAmmo
        -float reloadSpeedModifier
        
        +ApplyAttachment()
        +RemoveAttachment()
    }
    
    class Laser {
        -float accuracyModifier
        -Color laserColor
        
        +ApplyAttachment()
        +RemoveAttachment()
        +ToggleLaser()
    }
    
    class Flashlight {
        -float intensity
        -float range
        
        +ApplyAttachment()
        +RemoveAttachment()
        +ToggleFlashlight()
    }
    
    class AttachmentIdentifier_SO {
        <<ScriptableObject>>
        -string attachmentID
        -AttachmentType attachmentType
        -GameObject attachmentPrefab
        
        +GetAttachmentInfo()
    }
    
    class CompatibleAttachments {
        -List~AttachmentType~ supportedTypes
        
        +IsCompatible(AttachmentType type) bool
        +GetCompatibleTypes() List~AttachmentType~
    }
    
    Attachment <|-- Scope
    Attachment <|-- Barrel
    Attachment <|-- Grip
    Attachment <|-- Stock
    Attachment <|-- Magazine
    Attachment <|-- Laser
    Attachment <|-- Flashlight
    
    WeaponController --> Attachment : contains
    Attachment --> AttachmentIdentifier_SO : references
    WeaponController --> CompatibleAttachments : has
```

## Weapon Interaction Flow

```mermaid
sequenceDiagram
    participant Player
    participant Input as InputManager
    participant WC as WeaponController
    participant WSF as WeaponStateFactory
    participant WA as WeaponAnimator
    participant WE as WeaponEffects
    participant Target as IDamageable
    
    Player->>Input: Press Fire Button
    Input->>WC: Fire Input Received
    WC->>WSF: Change to Shooting State
    
    WSF->>WC: Update Current State
    WC->>WA: Play Shoot Animation
    WC->>WE: Play Muzzle Flash & SFX
    
    alt Hit Detection
        WC->>+Target: Apply Damage (Raycast Hit)
        Target->>-WC: Confirm Hit
        WC->>WE: Play Impact Effect
    else Miss
        WC->>WE: Play Miss Effect (Optional)
    end
    
    alt Auto Reload
        WC->>WC: Check Ammo Count
        WC->>WSF: Change to Reload State
        WSF->>WC: Update Current State
        WC->>WA: Play Reload Animation
    end
```

## Combat System Relationships

```mermaid
flowchart TB
    subgraph WeaponComponents
        WC[WeaponController]
        W_SO[Weapon_SO]
        WA[WeaponAnimator]
        WE[WeaponEffects]
    end
    
    subgraph StateManagement
        WSF[WeaponStateFactory]
        WBS[WeaponBaseState]
    end
    
    subgraph Attachments
        ATT[Attachment Base]
        SCOPE[Scope]
        BARREL[Barrel]
        GRIP[Grip]
        STOCK[Stock]
        MAG[Magazine]
    end
    
    subgraph CombatInteraction
        ID[IDamageable]
        DM[DamageMultiplier]
        PS[PlayerStats]
        EH[EnemyHealth]
    end
    
    WC --> W_SO
    WC --> WA
    WC --> WE
    WC --> WSF
    
    WSF --> WBS
    
    WC --> ATT
    ATT --> SCOPE
    ATT --> BARREL
    ATT --> GRIP
    ATT --> STOCK
    ATT --> MAG
    
    WC -- "Applies Damage" --> ID
    ID --> DM
    ID --> PS
    ID --> EH
```

The Weapon System is a sophisticated component with several key elements:

1. **Core Weapon Components**: WeaponController, WeaponAnimator, and Weapon_SO (ScriptableObject) handle the core weapon mechanics
2. **State Management**: A dedicated state system for weapons that handles different states like shooting, reloading, and inspecting
3. **Modular Attachment System**: A flexible attachment system allowing players to customize weapons with various attachments
4. **Combat Interaction Flow**: Detailed sequence showing how player input translates to weapon actions and damage application
5. **System Relationships**: Shows how the weapon system connects to other game systems like player stats and enemy health

The weapon system is designed to be highly modular, allowing for easy addition of new weapon types and attachments while maintaining a clean architecture.