# Enemy and Combat System

This diagram details the Enemy and Combat System architecture, showing classes, relationships, and the damage system mechanics.

## Combat Interface Hierarchy

```mermaid
classDiagram
    direction TB
    
    class IDamageable {
        <<interface>>
        +TakeDamage(float amount, Vector3 hitPosition)
        +Die()
        +IsAlive() bool
    }
    
    class MonoBehaviour {
        <<Unity>>
    }
    
    class EnemyHealth {
        -float maxHealth
        -float currentHealth
        -bool isDead
        -GameObject deathEffect
        
        +TakeDamage(float amount, Vector3 hitPosition)
        +Die()
        +IsAlive() bool
        +Heal(float amount)
    }
    
    class PlayerStats {
        -float maxHealth
        -float currentHealth
        -float maxShield
        -float currentShield
        
        +TakeDamage(float amount, Vector3 hitPosition)
        +Die()
        +IsAlive() bool
        +Heal(float amount)
        +RegenHealth()
        +RegenShield()
    }
    
    class Destructible {
        -float health
        -GameObject destroyedVersion
        -bool isDestroyed
        
        +TakeDamage(float amount, Vector3 hitPosition)
        +Die()
        +IsAlive() bool
    }
    
    class ExplosiveBarrel {
        -float health
        -float explosionRadius
        -float explosionDamage
        -GameObject explosionEffect
        
        +TakeDamage(float amount, Vector3 hitPosition)
        +Die()
        +Explode()
        +IsAlive() bool
    }
    
    class DamageMultiplier {
        -float multiplier
        -BodyPart bodyPart
        
        +GetMultiplier() float
        +ApplyMultiplier(float damage) float
    }
    
    class BodyPart {
        <<enumeration>>
        HEAD
        CHEST
        LIMBS
        BACK
    }
    
    IDamageable <|.. EnemyHealth
    IDamageable <|.. PlayerStats
    IDamageable <|.. Destructible
    IDamageable <|.. ExplosiveBarrel
    
    MonoBehaviour <|-- EnemyHealth
    MonoBehaviour <|-- Destructible
    MonoBehaviour <|-- ExplosiveBarrel
    MonoBehaviour <|-- DamageMultiplier
    
    EnemyHealth --> DamageMultiplier : uses
    DamageMultiplier --> BodyPart : references
```

## Enemy Types and Behavior

```mermaid
classDiagram
    direction TB
    
    class MonoBehaviour {
        <<Unity>>
    }
    
    class EnemyHealth {
        -float maxHealth
        -float currentHealth
        -bool isDead
    }
    
    class TrainingTarget {
        -bool canRespawn
        -float respawnTime
        
        +ResetTarget()
        +OnHit()
    }
    
    class CircularTargetEnemy {
        -float moveSpeed
        -Vector3 center
        -float radius
        -bool clockwise
        
        +Move()
        +UpdatePosition()
    }
    
    class Turret {
        -float rotationSpeed
        -float detectionRange
        -float fireRate
        -GameObject projectile
        -Transform target
        
        +DetectPlayer()
        +RotateTowardsTarget()
        +FireProjectile()
    }
    
    class TurretProjectile {
        -float damage
        -float speed
        -float lifeTime
        
        +Initialize(float dmg, float spd)
        +OnCollisionEnter(Collision collision)
    }
    
    MonoBehaviour <|-- EnemyHealth
    MonoBehaviour <|-- TrainingTarget
    MonoBehaviour <|-- CircularTargetEnemy
    MonoBehaviour <|-- Turret
    MonoBehaviour <|-- TurretProjectile
    
    TrainingTarget --> EnemyHealth : has
    CircularTargetEnemy --> EnemyHealth : has
    Turret --> EnemyHealth : has
    Turret --> TurretProjectile : spawns
```

## Damage Flow System

```mermaid
sequenceDiagram
    participant WC as WeaponController
    participant Bullet
    participant RaycastHit
    participant Target as IDamageable
    participant DM as DamageMultiplier
    participant Effects as WeaponEffects
    
    alt Projectile Weapon
        WC->>Bullet: Instantiate(damage, speed)
        Bullet->>+Target: OnCollision -> TakeDamage(damage, hitPos)
        Target->>DM: CheckMultiplier(hitPos)
        DM->>Target: Return Modified Damage
        Target->>Target: Apply Damage
        Target-->>-Bullet: Return Hit Confirmation
        Bullet->>Effects: Play Impact Effect
    else Raycast Weapon
        WC->>RaycastHit: Physics.Raycast()
        RaycastHit->>+Target: Get IDamageable Component
        WC->>Target: TakeDamage(damage, hitPos)
        Target->>DM: CheckMultiplier(hitPos)
        DM->>Target: Return Modified Damage
        Target->>Target: Apply Damage
        Target-->>-WC: Return Hit Confirmation
        WC->>Effects: Play Impact Effect at RaycastHit.point
    end
    
    alt Target Health <= 0
        Target->>Target: Die()
        Target->>WC: Notify Kill
        WC->>WC: Award Kill (XP, Score, etc.)
    end
```

## Combat Interaction Model

```mermaid
flowchart TB
    subgraph DamageSystem
        ID[IDamageable]
        DM[DamageMultiplier]
    end
    
    subgraph WeaponSystem
        WC[WeaponController]
        Bullet[Bullet]
        GW[GravitonWeapon]
    end
    
    subgraph Enemies
        EH[EnemyHealth]
        TT[TrainingTarget]
        TUR[Turret]
    end
    
    subgraph Environment
        DES[Destructible]
        EB[ExplosiveBarrel]
        HT[HurtTrigger]
    end
    
    subgraph Player
        PS[PlayerStats]
    end
    
    subgraph Effects
        WE[WeaponEffects]
        PE[PoolManager]
    end
    
    WC --> ID: Damage
    Bullet --> ID: Damage
    GW --> ID: Damage
    
    ID --> EH: Implementation
    ID --> DES: Implementation
    ID --> EB: Implementation
    ID --> PS: Implementation
    
    DM --> ID: Modifies
    
    HT --> PS: Damages
    EB --> ID: Area Damage
    
    WE --> PE: Uses
```

## Environmental Hazards and Interactions

```mermaid
classDiagram
    direction TB
    
    class IDamageable {
        <<interface>>
        +TakeDamage(float amount, Vector3 hitPosition)
        +Die()
        +IsAlive() bool
    }
    
    class MonoBehaviour {
        <<Unity>>
    }
    
    class HurtTrigger {
        -float damageAmount
        -float damageInterval
        -bool continuousDamage
        
        +OnTriggerEnter(Collider other)
        +OnTriggerStay(Collider other)
        +OnTriggerExit(Collider other)
        -ApplyDamage(IDamageable target)
    }
    
    class JumpPad {
        -float jumpForce
        -bool additive
        
        +OnTriggerEnter(Collider other)
        -LaunchPlayer(Rigidbody rb)
    }
    
    class ExplosiveBarrel {
        -float health
        -float explosionRadius
        -float explosionDamage
        -GameObject explosionEffect
        
        +TakeDamage(float amount, Vector3 hitPosition)
        +Die()
        +Explode()
    }
    
    class PointCapture {
        -float captureRadius
        -float captureTime
        -float captureProgress
        
        +OnTriggerStay(Collider other)
        +IsBeingCaptured() bool
        +GetCaptureProgress() float
    }
    
    MonoBehaviour <|-- HurtTrigger
    MonoBehaviour <|-- JumpPad
    MonoBehaviour <|-- ExplosiveBarrel
    MonoBehaviour <|-- PointCapture
    
    IDamageable <|.. ExplosiveBarrel
    
    HurtTrigger --> IDamageable : damages
    ExplosiveBarrel --> IDamageable : damages in radius
```

The Enemy and Combat System is built on a flexible damage interface that supports various entities:

1. **IDamageable Interface**: The core interface that unifies all combat interactions, implemented by enemies, players, and destructible objects
2. **Damage Multipliers**: Support for locational damage with different multipliers based on hit location
3. **Enemy Variety**: Different enemy types with unique behaviors and attack patterns
4. **Damage Flow**: Detailed flow of how damage is applied from weapons to targets, including projectile and raycast weapons
5. **Environmental Interactions**: Hazards and interactive elements that can cause damage or affect gameplay

This architecture allows for:
- Easy addition of new enemy types that respond to damage consistently
- Flexible damage system that works with both direct-hit weapons and projectiles
- Integration of environmental hazards into the combat ecosystem
- Clear separation of damage dealing (weapons) and damage receiving (enemies, players, objects) with the IDamageable interface

The combat system serves as the backbone of gameplay interactions, connecting player actions, weapon effects, and enemy responses in a cohesive framework.