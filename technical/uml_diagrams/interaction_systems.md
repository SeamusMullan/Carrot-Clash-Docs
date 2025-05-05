# Interaction Systems

This diagram details the Interaction Systems architecture in Carrot Clash, showing how players interact with the game world through pickups, interactables, and environmental elements.

## Interaction Core Architecture

```mermaid
classDiagram
    direction TB
    
    class MonoBehaviour {
        <<Unity>>
    }
    
    class InteractManager {
        -float interactionRange
        -Transform interactionSource
        -Interactable currentInteractable
        -LayerMask interactableMask
        
        +CheckForInteractable()
        +Interact()
        +GetCurrentInteractable() Interactable
        +ShowInteractionPrompt()
        +HideInteractionPrompt()
    }
    
    class Interactable {
        <<abstract>>
        #string promptMessage
        #bool canInteract
        #float interactionCooldown
        
        +OnFocus()
        +OnLoseFocus()
        +OnInteract() bool
        +CanInteract() bool
        +GetPromptMessage() string
    }
    
    class Identifiable {
        <<interface>>
        +GetIdentification() string
        +GetDescription() string
    }
    
    class Pickeable {
        <<abstract>>
        #Item_SO itemData
        #GameObject model
        
        +OnInteract() bool
        +PickUp()
        +CanPickUp() bool
    }
    
    class Item_SO {
        <<ScriptableObject>>
        -string itemID
        -string itemName
        -string itemDescription
        -Sprite itemIcon
        -ItemType itemType
        
        +GetItemName() string
        +GetItemDescription() string
        +GetItemIcon() Sprite
        +GetItemType() ItemType
    }
    
    class WeaponPickeable {
        -Weapon_SO weaponData
        
        +OnInteract() bool
        +PickUp()
    }
    
    class BulletsPickeable {
        -BulletsItem_SO bulletsData
        -int amount
        
        +OnInteract() bool
        +PickUp()
    }
    
    class AttachmentPickeable {
        -AttachmentIdentifier_SO attachmentData
        
        +OnInteract() bool
        +PickUp()
    }
    
    class BulletsItem_SO {
        <<ScriptableObject>>
        -int maxAmount
        -WeaponType compatibleType
        
        +GetMaxAmount() int
        +GetCompatibleType() WeaponType
    }
    
    MonoBehaviour <|-- InteractManager
    MonoBehaviour <|-- Interactable
    Interactable <|-- Pickeable
    
    Identifiable <|.. Item_SO
    Pickeable <|-- WeaponPickeable
    Pickeable <|-- BulletsPickeable
    Pickeable <|-- AttachmentPickeable
    
    Pickeable --> Item_SO
    WeaponPickeable --> Weapon_SO
    BulletsPickeable --> BulletsItem_SO
    AttachmentPickeable --> AttachmentIdentifier_SO
    
    InteractManager --> Interactable : manages interaction with
```

## Environment Interactables

```mermaid
classDiagram
    direction TB
    
    class Interactable {
        <<abstract>>
        #string promptMessage
        #bool canInteract
        
        +OnFocus()
        +OnLoseFocus()
        +OnInteract() bool
    }
    
    class DoorInteractable {
        -bool isLocked
        -string keyName
        -bool isOpen
        
        +OnInteract() bool
        +Open()
        +Close()
        +Lock()
        +Unlock()
    }
    
    class Lootbox {
        -List~Item_SO~ possibleItems
        -int minItems
        -int maxItems
        -bool isOpened
        
        +OnInteract() bool
        +GenerateLoot()
        +Open()
    }
    
    class CheckPointView {
        -Vector3 respawnPoint
        -bool isActivated
        
        +OnInteract() bool
        +Activate()
    }
    
    class Crate {
        -GameObject destroyedVersion
        -List~Item_SO~ lootTable
        -bool isDestroyed
        
        +OnInteract() bool
        +Destroy()
        +DropLoot()
    }
    
    class Trigger {
        -GameObject[] objectsToTrigger
        -bool onlyOnce
        -bool hasTriggered
        
        +OnInteract() bool
        +TriggerObjects()
    }
    
    class Destructible {
        -float health
        -GameObject destroyedVersion
        -bool isDestroyed
        
        +TakeDamage(float amount)
        +Destroy()
    }
    
    Interactable <|-- DoorInteractable
    Interactable <|-- Lootbox
    Interactable <|-- CheckPointView
    Interactable <|-- Crate
    Interactable <|-- Trigger
```

## Powerups and Collectibles

```mermaid
classDiagram
    direction TB
    
    class Interactable {
        <<abstract>>
        #string promptMessage
        #bool canInteract
        
        +OnFocus()
        +OnLoseFocus()
        +OnInteract() bool
    }
    
    class PowerUp {
        <<abstract>>
        #float duration
        #float effectValue
        
        +OnInteract() bool
        +ApplyEffect()
        +RemoveEffect()
    }
    
    class HealMultiplierPowerUp {
        -float healingMultiplier
        
        +OnInteract() bool
        +ApplyEffect()
        +RemoveEffect()
    }
    
    class Healthpack {
        -float healAmount
        -bool percentBased
        
        +OnInteract() bool
        +Heal()
    }
    
    class Experience {
        -int experienceAmount
        
        +OnInteract() bool
        +CollectExperience()
    }
    
    class Coin {
        -int value
        
        +OnInteract() bool
        +Collect()
    }
    
    Interactable <|-- PowerUp
    Interactable <|-- Healthpack
    Interactable <|-- Experience
    Interactable <|-- Coin
    
    PowerUp <|-- HealMultiplierPowerUp
```

## Player Interaction Flow

```mermaid
sequenceDiagram
    participant Player
    participant IM as InteractManager
    participant Interactable
    participant Pickeable
    participant WC as WeaponController
    
    Player->>IM: Press Interact Button
    IM->>IM: CheckForInteractable()
    
    alt Interactable Found
        IM->>Interactable: OnFocus()
        Interactable-->>IM: Return Prompt Message
        IM->>Player: Show Interaction Prompt
    end
    
    Player->>IM: Confirm Interaction
    IM->>Interactable: OnInteract()
    
    alt Weapon Pickup
        Interactable->>Pickeable: PickUp()
        Pickeable->>WC: AddWeapon(weaponData)
        WC-->>Player: Switch to New Weapon
    else Ammo Pickup
        Interactable->>Pickeable: PickUp()
        Pickeable->>WC: AddAmmo(amount, type)
        WC-->>Player: Update Ammo Count
    else Other Interaction
        Interactable->>Interactable: Execute Specific Behavior
        Interactable-->>Player: Feedback (Visual/Audio)
    end
    
    Interactable-->>IM: Interaction Complete
    IM->>Player: Hide Interaction Prompt
```

## Interaction Zone Management

```mermaid
flowchart TB
    subgraph PlayerSystems
        Player[Player]
        IM[InteractManager]
        PA[PlayerActions]
    end
    
    subgraph InteractableSystems
        Interactables[Interactable Base]
        Pickeables[Pickeable Items]
        Environment[Environment Interactables]
        Powerups[Powerups]
    end
    
    subgraph ReceiverSystems
        WC[WeaponController]
        PS[PlayerStats]
        Inventory[Inventory]
        EXP[ExperienceManager]
        Coins[CoinManager]
    end
    
    Player --> PA
    PA --> IM
    
    IM --> Interactables
    
    Interactables --> Pickeables
    Interactables --> Environment
    Interactables --> Powerups
    
    Pickeables --> WC
    Pickeables --> Inventory
    
    Powerups --> PS
    
    Environment --> PS
    Environment --> WC
    
    Pickeables --> EXP
    Pickeables --> Coins
```

The Interaction Systems in Carrot Clash provide a flexible framework for player interactions with the game world:

1. **Core Interaction Architecture**:
   - InteractManager: Central component that detects and manages interactions
   - Interactable: Base class for all interactive objects
   - Pickup System: Specialized system for items that can be collected

2. **Environment Interactables**:
   - Doors, crates, checkpoints, and other interactive environmental elements
   - Consistent interaction interface through the Interactable base class
   - Support for complex behaviors like locked doors requiring keys

3. **Powerups and Collectibles**:
   - Health packs, experience points, coins, and temporary buffs
   - Effect-based architecture allowing for easy addition of new powerup types
   - Duration and effect management for temporary powerups

4. **Player Interaction Flow**:
   - Clear sequence from detecting interactables to applying their effects
   - Support for different interaction types with appropriate feedback
   - Integration with weapon system for adding weapons and ammo

This architecture provides several advantages:
- Easy extension with new interactable types
- Consistent player experience through standardized interaction mechanics
- Clear separation between detection (InteractManager), behavior (Interactable subclasses), and effect (receiver systems)
- Straightforward integration with inventory, weapon, and player systems

The interaction system serves as a crucial bridge between the player and the game world, enabling a wide range of gameplay mechanics from collecting items to activating complex environmental elements.