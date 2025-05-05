# UI System

This diagram details the UI System architecture, showing classes, relationships, and how UI components interact with gameplay systems.

## UI Component Structure

```mermaid
classDiagram
    direction TB
    
    class MonoBehaviour {
        <<Unity>>
    }
    
    class UIController {
        -PlayerStats playerStats
        -WeaponController weaponController
        -UIElements uiElements
        
        +UpdateHealthUI()
        +UpdateAmmoUI()
        +UpdateWeaponUI()
        +UpdateObjectiveUI()
        +TogglePauseMenu()
        +ShowGameOverScreen()
    }
    
    class UIEvents {
        +OnHealthChanged(float health, float maxHealth)
        +OnAmmoChanged(int current, int total)
        +OnWeaponChanged(Weapon_SO weapon)
        +OnObjectiveUpdated(string objective)
        +OnGameStateChanged(GameState state)
        +OnKillConfirmed()
    }
    
    class Crosshair {
        -CrosshairShape crosshairShape
        -float baseSize
        -float currentSize
        -bool dynamic
        
        +UpdateCrosshair()
        +ExpandOnShoot()
        +ResetSize()
        +SetColor(Color color)
    }
    
    class CrosshairShape {
        -List~Vector2~ points
        -float thickness
        -Color color
        
        +Draw()
        +SetPoints(List~Vector2~ newPoints)
    }
    
    class Hitmarker {
        -float displayTime
        -bool isDisplaying
        -AudioClip hitSound
        -AudioClip killSound
        
        +ShowHitmarker(bool isKill)
        +HideHitmarker()
    }
    
    class WeaponsInventoryUISlot {
        -Image weaponIcon
        -Text weaponName
        -WeaponController weaponController
        
        +Initialize(Weapon_SO weapon)
        +SelectSlot()
        +DeselectSlot()
    }
    
    class AttachmentUIElement {
        -AttachmentType attachmentType
        -Image icon
        -Button selectButton
        
        +Initialize(AttachmentIdentifier_SO attachment)
        +Select()
        +Deselect()
    }
    
    class AttachmentGroupUI {
        -List~AttachmentUIElement~ elements
        -AttachmentType groupType
        
        +AddElement(AttachmentUIElement element)
        +RemoveElement(AttachmentUIElement element)
        +ToggleVisibility(bool visible)
    }
    
    class RebindUI {
        -InputManager inputManager
        -Text actionText
        -Button rebindButton
        -string actionName
        
        +StartRebind()
        +EndRebind()
        +UpdateBindingDisplay()
    }
    
    class ToastNotificationMessage {
        -float displayTime
        -float fadeTime
        -Queue~Notification~ messageQueue
        
        +ShowNotification(string message, NotificationType type)
        +ProcessMessageQueue()
    }
    
    class ToastNotification {
        -string message
        -NotificationType type
        -float duration
        
        +Show()
        +Hide()
        +SetMessage(string message)
    }
    
    class NotificationType {
        <<enumeration>>
        INFO
        WARNING
        ERROR
        SUCCESS
    }
    
    class CowsinsButton {
        -AudioClip hoverSound
        -AudioClip clickSound
        -float scaleOnHover
        
        +OnPointerEnter()
        +OnPointerExit()
        +OnPointerClick()
    }
    
    MonoBehaviour <|-- UIController
    MonoBehaviour <|-- Crosshair
    MonoBehaviour <|-- CrosshairShape
    MonoBehaviour <|-- Hitmarker
    MonoBehaviour <|-- WeaponsInventoryUISlot
    MonoBehaviour <|-- AttachmentUIElement
    MonoBehaviour <|-- AttachmentGroupUI
    MonoBehaviour <|-- RebindUI
    MonoBehaviour <|-- ToastNotificationMessage
    MonoBehaviour <|-- ToastNotification
    MonoBehaviour <|-- CowsinsButton
    
    UIController --> UIEvents : subscribes
    UIController --> Crosshair : manages
    UIController --> Hitmarker : manages
    UIController --> WeaponsInventoryUISlot : manages
    UIController --> AttachmentGroupUI : manages
    
    Crosshair --> CrosshairShape : uses
    AttachmentGroupUI --> AttachmentUIElement : contains
    ToastNotificationMessage --> ToastNotification : creates
    ToastNotificationMessage --> NotificationType : uses
```

## UI Information Flow

```mermaid
sequenceDiagram
    participant PS as PlayerStats
    participant WC as WeaponController
    participant UIE as UIEvents
    participant UIC as UIController
    participant UI as UI Components
    
    PS->>UIE: Health Changed
    UIE->>UIC: OnHealthChanged Event
    UIC->>UI: Update Health Bar
    
    WC->>UIE: Ammo Changed
    UIE->>UIC: OnAmmoChanged Event
    UIC->>UI: Update Ammo Counter
    
    WC->>UIE: Weapon Switched
    UIE->>UIC: OnWeaponChanged Event
    UIC->>UI: Update Weapon UI
    
    WC->>UIE: Enemy Hit
    UIE->>UIC: OnHitConfirmed Event
    UIC->>UI: Show Hitmarker
    
    WC->>UIE: Enemy Killed
    UIE->>UIC: OnKillConfirmed Event
    UIC->>UI: Show Kill Hitmarker
```

## UI State and Interaction

```mermaid
stateDiagram-v2
    [*] --> GameplayUI
    
    GameplayUI --> PauseMenuUI: Player Presses Pause
    PauseMenuUI --> GameplayUI: Resume Game
    
    GameplayUI --> WeaponSelectionUI: Open Weapon Wheel
    WeaponSelectionUI --> GameplayUI: Select Weapon/Close
    
    GameplayUI --> AttachmentUI: Customize Weapon
    AttachmentUI --> GameplayUI: Confirm/Cancel
    
    GameplayUI --> DeathScreenUI: Player Dies
    DeathScreenUI --> GameplayUI: Respawn
    
    PauseMenuUI --> SettingsUI: Open Settings
    SettingsUI --> PauseMenuUI: Back
    
    SettingsUI --> ControlsUI: Rebind Controls
    ControlsUI --> SettingsUI: Back
    
    PauseMenuUI --> [*]: Quit Game
```

## UI Layering and Canvas Management

```mermaid
flowchart TB
    subgraph CanvasHierarchy
        direction TB
        MasterCanvas[Master Canvas]
        GameplayCanvas[Gameplay Canvas]
        OverlayCanvas[Overlay Canvas]
        MenuCanvas[Menu Canvas]
        PopupCanvas[Popup Canvas]
        
        MasterCanvas --> GameplayCanvas
        MasterCanvas --> OverlayCanvas
        MasterCanvas --> MenuCanvas
        MasterCanvas --> PopupCanvas
    end
    
    subgraph GameplayElements
        HUD[HUD Elements]
        Crosshair[Crosshair]
        Hitmarker[Hitmarker]
        AmmoCounter[Ammo Counter]
        HealthBar[Health Bar]
        Compass[Compass]
    end
    
    subgraph OverlayElements
        Notifications[Toast Notifications]
        Indicators[Damage Indicators]
        Objectives[Objective Markers]
    end
    
    subgraph MenuElements
        PauseMenu[Pause Menu]
        SettingsPanel[Settings Panel]
        WeaponWheel[Weapon Selection]
        AttachmentPanel[Attachment UI]
    end
    
    subgraph PopupElements
        DialogBox[Dialog Box]
        ConfirmationBox[Confirmation Box]
        TutorialTips[Tutorial Popups]
    end
    
    GameplayCanvas --> GameplayElements
    OverlayCanvas --> OverlayElements
    MenuCanvas --> MenuElements
    PopupCanvas --> PopupElements
```

## UI Component Dependencies

```mermaid
classDiagram
    direction TB
    
    class UIController {
        +UpdateHealthUI()
        +UpdateAmmoUI()
        +UpdateWeaponUI()
    }
    
    class PlayerStats {
        -float currentHealth
        -float maxHealth
    }
    
    class WeaponController {
        -int currentAmmo
        -int totalAmmo
        -Weapon_SO currentWeapon
    }
    
    class InputManager {
        +GetButtonDown(string action)
        +RegisterInputAction(string action)
    }
    
    class UIEvents {
        +OnHealthChanged(float health, float maxHealth)
        +OnAmmoChanged(int current, int total)
    }
    
    class SoundManager {
        +PlayUISound(AudioClip clip)
        +PlayButtonSound()
    }
    
    UIController --> PlayerStats : reads
    UIController --> WeaponController : reads
    UIController --> UIEvents : subscribes
    UIController --> InputManager : reads input
    
    PlayerStats --> UIEvents : triggers
    WeaponController --> UIEvents : triggers
    
    UIController --> SoundManager : plays sounds
    CowsinsButton --> SoundManager : plays sounds
```

The UI System in Carrot Clash serves as the bridge between gameplay systems and player feedback:

1. **Core UI Components**: Organized structure of UI elements from crosshair and hitmarkers to weapon selection and notifications
2. **Event-Driven Architecture**: Uses an event system (UIEvents) to decouple gameplay systems from UI updates
3. **Layer Management**: Clear hierarchical organization of UI elements based on their purpose and priority
4. **State Management**: Defines clear UI states and transitions between gameplay, menus, and overlays
5. **Customization Support**: Dedicated UI components for weapon customization and control rebinding

The UI system employs several best practices:
- Event-based communication to avoid direct dependencies between gameplay and UI
- Hierarchical organization of canvases to manage rendering order and input priority
- Component-based design allowing for easy addition or removal of UI elements
- Clear state transitions to handle different game states (playing, paused, customizing)

This modular approach allows for flexible UI updates as the game evolves, with a clear separation between game logic and visual representation.