# ConnectHub

ConnectHub is a Java Swing social platform that includes authentication, friend management, profile management, posts/stories, and an extensible social interaction backbone.

This repository was refactored into a cleaner modular architecture and extended with scalable Group, Chat, and Notification capabilities while preserving legacy application behavior.

## Project Overview

ConnectHub provides:

- User sign-up/login with hashed passwords
- Friend requests, acceptance/rejection, blocking, and suggestions
- Feed generation from friends' posts and stories
- User profile viewing and editing
- Group management (create/join/leave/view members/post in groups)
- Direct messaging with conversation history
- Event-driven notifications for activity (messages, friend requests, posts, group activity)

## Architecture Overview

The project now follows a modular layered design:

- Presentation layer: Swing windows (legacy-compatible UI classes)
- Application/service layer: business use-cases and orchestration
- Domain layer: entities such as User, Group, Message, Notification
- Data access layer: repository interfaces + JSON implementations
- Event layer: publish/subscribe event bus for decoupled notifications
- Factory/composition layer: centralized dependency wiring

## Folder and Module Structure

```text
src/
├── com/connecthub/
│   ├── model/                  # Core domain entities
│   ├── repository/             # Repository interfaces
│   ├── repository/json/        # JSON-backed repository implementations
│   ├── service/                # Service contracts
│   ├── service/impl/           # Service implementations
│   ├── events/                 # Domain events and event bus (Observer/Pub-Sub)
│   ├── factory/                # Dependency composition (ConnectHubFactory)
│   └── util/                   # Shared utilities (hashing, validation)
├── LoginWindow.java            # Legacy UI entry windows (preserved)
├── SignUpWindow.java
├── NewsFeedWindow.java
├── FriendManagementWindow.java
├── myProfile.java
├── AccountManagement.java      # Legacy compatibility facades over new services
├── FriendManagement.java
├── NewsFeed.java
├── ProfileManager.java
├── MainContentCreation.java
├── UserDatabase.java
├── ConnectHubContext.java      # Legacy bridge to modern factory
├── LegacyMapper.java           # Legacy-modern model mapper
├── GroupManagement.java        # Group feature adapter
├── ChatManagement.java         # Chat feature adapter
└── NotificationDispatcher.java # Notification feature adapter

users.json
posts.json
stories.json
groups.json
conversations.json
notifications.json
```

## Major Refactoring Improvements

- Introduced clear separation between models, repositories, services, events, and utilities
- Moved business logic out of persistence and UI-heavy classes into dedicated service layer
- Reduced duplicated hashing/lookup/persistence patterns via shared components
- Added factory-based dependency composition to avoid ad-hoc object creation
- Preserved existing legacy APIs to minimize UI breakage and migration risk

## SOLID Principles Applied

- Single Responsibility Principle:
	each repository/service class owns one focused responsibility
- Open/Closed Principle:
	service contracts and repository interfaces allow extension without modifying callers
- Liskov Substitution Principle:
	code depends on abstractions (`*Service`, `*Repository`) and can swap implementations
- Interface Segregation Principle:
	narrow interfaces per subsystem (Account, Friend, Feed, Profile, Group, Chat, Notification)
- Dependency Inversion Principle:
	higher-level modules depend on interfaces, with concrete dependencies wired in `ConnectHubFactory`

## Design Patterns Introduced

- Repository Pattern: abstraction over JSON persistence
- Factory Pattern: `ConnectHubFactory` composes the app graph
- Strategy-like decoupling via interfaces: multiple service/repository implementations are swappable
- Observer / Publish-Subscribe Pattern: `EventBus` + `NotificationEventSubscriber`
- Facade Pattern: legacy classes (`AccountManagement`, `FriendManagement`, etc.) act as compatibility facades over the modern layer
- Singleton-style access point: `ConnectHubContext` exposes one shared factory instance for legacy code

## New Features

### Group System

- Create group with owner role
- Join and leave groups
- View group members
- Add posts inside a group
- Backed by `GroupService` + `GroupRepository` (`groups.json`)

### Chat System

- Direct messages between users
- Conversation auto-creation per user pair
- Message history retrieval
- Backed by `ChatService` + `ConversationRepository` (`conversations.json`)

### Notification System

- Event-driven notifications for:
	- new direct messages
	- friend request activity
	- post activity
	- group activity
- Backed by `NotificationService` + `NotificationRepository` (`notifications.json`)
- Uses `EventBus` and `NotificationEventSubscriber`

## Why This Architecture Scales Better

- New features can be added at service/repository boundaries without rewriting UI windows
- Event-driven notifications reduce tight coupling between social actions and side effects
- Repository contracts make future migration from JSON to SQL/NoSQL straightforward
- Compatibility facade approach allows iterative migration with low regression risk
- Cleaner modules and shorter methods improve maintenance and onboarding

## Running the Application

1. Open the project in IntelliJ IDEA.
2. Ensure required dependencies are available in the IDE project configuration.
3. Run `Main.java`.
4. The app will use local JSON files as storage.

## Notes

- Existing UI behavior is preserved while internal architecture is modernized.
- Legacy classes remain available as adapters to prevent disruptive UI rewrites.
- New Group/Chat/Notification features are available through the new service layer and adapters.

