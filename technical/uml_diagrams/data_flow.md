# Data Flow

This diagram details the Data Flow architecture in Carrot Clash, showing how information moves between different systems, how events are propagated, and how persistent data is managed.

## Core System Data Flow

```mermaid
flowchart TB
    subgraph InputLayer["Input Layer"]
        IM["InputManager"]
        PA["PlayerActions"]
    end
    
    subgraph GameplayLayer["Gameplay Layer"]
        PS["PlayerStats"]
        PM["PlayerMovement"]
        WC["WeaponController"]
        EH["EnemyHealth"]
    end
    
    subgraph ManagerLayer["Manager Layer"]
        GS["GameSettingsManager"]
        SM["SoundManager"]
        EM["ExperienceManager"]
        CM["CoinManager"]
        PMgr["PoolManager"]
    end
    
    subgraph UILayer["UI Layer"]
        UIC["UIController"]
        UIE["UIEvents"]
        TNM["ToastNotificationMessage"]
    end
    
    subgraph StorageLayer["Storage Layer"]
        Prefs["PlayerPrefs"]
        SaveSystem["Save System"]
    end
    
    InputLayer --> GameplayLayer
    GameplayLayer --> UILayer
    GameplayLayer --> ManagerLayer
    ManagerLayer --> StorageLayer
    UILayer --> StorageLayer
    
    ManagerLayer --> GameplayLayer
    ManagerLayer --> UILayer
    StorageLayer --> ManagerLayer
    InputLayer --> UILayer
```

## Event-Based Communication

```mermaid
sequenceDiagram
    participant GameplaySystems
    participant UIEvents
    participant UIController
    participant SoundManager
    participant UIComponents
    
    GameplaySystems->>UIEvents: Trigger Event (e.g., OnHealthChanged)
    UIEvents->>UIController: Propagate Event
    UIController->>UIComponents: Update UI Elements
    UIController->>SoundManager: Play Appropriate Sound
    
    Note over GameplaySystems,UIComponents: Event-based architecture decouples systems
    
    GameplaySystems->>UIEvents: Trigger Event (e.g., OnWeaponChanged)
    UIEvents->>UIController: Propagate Event
    UIController->>UIComponents: Update Weapon UI
    
    GameplaySystems->>UIEvents: Trigger Event (e.g., OnEnemyKilled)
    UIEvents->>UIController: Propagate Event
    UIController->>UIComponents: Show Kill Notification
    UIController->>SoundManager: Play Kill Sound
```

## Data Persistence Flow

```mermaid
flowchart TB
    subgraph RuntimeData["Runtime Data"]
        PlayerData["Player Data"]
        WeaponData["Weapon Data"]
        SettingsData["Settings Data"]
        ProgressData["Progress Data"]
    end
    
    subgraph PersistenceManager["Persistence Manager"]
        Serializer["Data Serializer"]
        FileIO["File I/O Handler"]
        EncryptionLayer["Encryption Layer"]
    end
    
    subgraph StorageTypes["Storage Types"]
        LocalFiles["Local Files"]
        PlayerPrefsStorage["PlayerPrefs"]
    end
    
    RuntimeData --> PersistenceManager
    PersistenceManager --> StorageTypes
    
    StorageTypes --> PersistenceManager
    PersistenceManager --> RuntimeData
    
    Serializer --> FileIO
    FileIO --> EncryptionLayer
    EncryptionLayer --> StorageTypes
    
    StorageTypes --> EncryptionLayer
    EncryptionLayer --> FileIO
    FileIO --> Serializer
    Serializer --> RuntimeData
```

## ScriptableObject Data Architecture

```mermaid
classDiagram
    direction TB
    
    class ScriptableObject {
        <<Unity>>
    }
    
    class Weapon_SO {
        -string weaponName
        -WeaponType weaponType
        -float damage
        -float fireRate
        -float range
        -int maxAmmo
        -int maxTotalAmmo
        
        +GetWeaponStats()
    }
    
    class AttachmentIdentifier_SO {
        -string attachmentID
        -AttachmentType attachmentType
        -GameObject attachmentPrefab
        
        +GetAttachmentInfo()
    }
    
    class Item_SO {
        -string itemID
        -string itemName
        -string itemDescription
        -Sprite itemIcon
        -ItemType itemType
        
        +GetItemInfo()
    }
    
    class BulletsItem_SO {
        -int maxAmount
        -WeaponType compatibleType
        
        +GetMaxAmount()
        +GetCompatibleType()
    }
    
    class ProceduralShot_SO {
        -float recoilX
        -float recoilY
        -float recoilZ
        -float snappiness
        -float returnSpeed
        
        +GetRecoilSettings()
    }
    
    ScriptableObject <|-- Weapon_SO
    ScriptableObject <|-- AttachmentIdentifier_SO
    ScriptableObject <|-- Item_SO
    ScriptableObject <|-- BulletsItem_SO
    ScriptableObject <|-- ProceduralShot_SO
    
    Item_SO <|-- BulletsItem_SO
    
    Weapon_SO --> ProceduralShot_SO : references
```

## Performance Monitoring Data Flow

```mermaid
flowchart TB
    subgraph GameSystems["Game Systems"]
        CPU["CPU Usage"]
        GPU["GPU Performance"]
        RAM["RAM Usage"]
        Audio["Audio System"]
    end
    
    subgraph GraphyMonitor["Graphy Monitor"]
        GM["GraphyManager"]
        FPS["FpsMonitor"]
        RAM2["RamMonitor"]
        Audio2["AudioMonitor"]
    end
    
    subgraph Visualization["Visualization"]
        FPSGraph["FpsGraph"]
        RAMGraph["RamGraph"]
        AudioGraph["AudioGraph"]
        GMText["GraphText Displays"]
    end
    
    CPU --> FPS
    GPU --> FPS
    RAM --> RAM2
    Audio --> Audio2
    
    FPS --> FPSGraph
    FPS --> GMText
    
    RAM2 --> RAMGraph
    RAM2 --> GMText
    
    Audio2 --> AudioGraph
    Audio2 --> GMText
    
    GM --> FPS
    GM --> RAM2
    GM --> Audio2
```

## Object Pooling Data Flow

```mermaid
sequenceDiagram
    participant Client as Client System
    participant PM as PoolManager
    participant Pool as Object Pool
    participant Obj as Pooled Object
    
    Client->>PM: Request Object(prefabID)
    PM->>Pool: GetObject(prefabID)
    
    alt Pool Has Available Objects
        Pool->>Pool: Find Inactive Object
        Pool->>Obj: Activate Object
        Obj-->>Client: Return Active Object
    else Create New Object
        Pool->>Pool: Instantiate New Object
        Pool->>Obj: Add to Pool
        Obj-->>Client: Return New Object
    end
    
    Note over Client,Obj: Object is used by client system
    
    Client->>PM: Return Object(object)
    PM->>Pool: ReturnObject(object)
    Pool->>Obj: Deactivate Object
    Pool->>Pool: Mark as Available
```

The Data Flow architecture in Carrot Clash is designed for efficient communication between systems:

1. **Core System Data Flow**:
   - Layered architecture with clear data paths between input, gameplay, management, UI, and storage
   - Directional flow showing how data cascades through the system layers

2. **Event-Based Communication**:
   - Decoupled systems communicating through events rather than direct references
   - UIEvents serving as a central event bus for UI-related notifications
   - Clear sequence of event propagation from gameplay systems to UI components

3. **Data Persistence Flow**:
   - Structured approach to saving and loading game data
   - Multi-layered process including serialization, encryption, and storage
   - Support for different storage methods (local files and PlayerPrefs)

4. **ScriptableObject-Based Data Architecture**:
   - Extensive use of ScriptableObjects for shareable configuration data
   - Clear inheritance and relationship model between different data types
   - Decoupling of data from behavior through ScriptableObject references

5. **Performance Monitoring**:
   - Comprehensive monitoring of system performance through Graphy
   - Data collection from various system components (CPU, GPU, RAM, Audio)
   - Visualization through graphs and text displays

6. **Object Pooling**:
   - Efficient resource management through object pooling
   - Clear request and return flow between client systems and the pool manager
   - Optimization for frequently instantiated/destroyed objects

These data flow patterns facilitate:
- Maintainable code through decoupled systems
- Scalable architecture that can handle increasing complexity
- Optimized performance through efficient resource management
- Robust data handling with proper persistence mechanisms

The data flow architecture serves as the foundation for communication between all game systems, ensuring that information moves efficiently throughout the application while maintaining clean separation of concerns.