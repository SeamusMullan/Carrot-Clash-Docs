# Carrot Clash UML Diagrams

This directory contains comprehensive UML (Unified Modeling Language) diagrams documenting the architecture and interactions between different components of the Carrot Clash game. These diagrams are intended for professional developers to understand the system structure and relationships.

## Available Diagrams

1. [System Overview](/technical/uml_diagrams/system_overview.md) - High-level architecture of the entire game system
2. [Player Subsystem](/technical/uml_diagrams/player_subsystem.md) - Detailed view of player-related classes and interactions
3. [Weapon System](/technical/uml_diagrams/weapon_system.md) - Weapon mechanics, attachments, and interactions
4. [Enemy and Combat System](/technical/uml_diagrams/enemy_combat_system.md) - Enemy classes, damage system, and combat interactions
5. [UI System](/technical/uml_diagrams/ui_system.md) - User interface components and their connections
6. [State Management](/technical/uml_diagrams/state_management.md) - State pattern implementation for player and weapon states
7. [Interaction Systems](/technical/uml_diagrams/interaction_systems.md) - Pickup system, interactables, and environment interactions
8. [Data Flow](/technical/uml_diagrams/data_flow.md) - How data moves between major components

## Diagram Notation

These UML diagrams follow standard UML 2.0 notation:
- **Classes** are represented by rectangles with the class name at the top
- **Interfaces** are annotated with the «interface» stereotype
- **Inheritance** is shown with a solid line and hollow triangle pointing to the parent
- **Implementation** is shown with a dashed line and hollow triangle pointing to the interface
- **Association** is represented by a solid line between classes
- **Aggregation** is shown with a hollow diamond at the container end
- **Composition** is shown with a filled diamond at the container end
- **Multiplicity** is indicated with numbers near the relationship ends (1, 0..*, etc.)
- **Dependencies** are represented by dashed arrows

## Purpose

These diagrams serve as both documentation and development guides. They provide:
- A clear understanding of the system architecture
- Visual representation of class relationships
- Reference for implementing new features
- Aid in onboarding new developers
- Foundation for maintaining code consistency

## Tools Used

These diagrams are written in [Mermaid](https://mermaid.js.org/) syntax, which can be rendered directly in modern Markdown viewers including GitHub, GitLab, and various documentation tools.