# State Management and Navigation Design

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers: User
Date: 2026-06-28
Related documents:

- [Software Requirements Specification](software-requirements-specification.md)
- [Software Architecture Document](software-architecture-document.md)
- [API and Repository Contracts](api-repository-contracts.md)

## Purpose and Scope

This document defines the approach for state management and navigation in the AudioVerse English Flutter application. It evaluates Flutter state and navigation options against the project's strict requirements for offline-first resilience and comprehensive accessibility, specifically ensuring screen reader compatibility (TalkBack/VoiceOver) and logical focus management.

Out of scope: Concrete UI widget implementation, detailed routing maps for every sub-screen, and implementation of specific view models. This document only defines the frameworks, patterns, and principles to be used.

## Requirements Summary

This design fulfills the following constraints and requirements:

- **FR-002, FR-006**: Core flows for onboarding and lesson progression require stable state management.
- **A11Y-001, A11Y-002, A11Y-008**: Focus order, gesture conflict avoidance, and screen reader flow must be maintained across navigation transitions.
- **OFF-001, OFF-002**: UI state must correctly reflect local SQLite data and degrade gracefully when offline. Application should easily restore state when restarted offline.
- **Performance**: Must operate efficiently on low-to-mid-range devices (2GB RAM).

## Technical Design

### Architecture Context

State management and navigation reside in the Presentation and Domain layers. The state management solution bridges the UI with Repository interfaces (e.g., `ILessonRepository`), exposing reactive state streams. Navigation dictates how these UI screens are pushed and popped, heavily impacting accessibility focus.

### Component Breakdown

1. **State Management Framework**: Riverpod is selected as the primary state management framework. It provides robust dependency injection (crucial for swapping Engine Adapters), compile-time safety, and handles asynchronous state (offline SQLite queries) effectively using `AsyncValue`.
2. **Navigation Framework**: `go_router` (Navigator 2.0) is selected. It supports deep linking (useful for future notifications), declarative routing, and predictable back-stack management, which is critical for accessible navigation.
3. **Controllers/ViewModels**: Each major screen (e.g., Lesson Player) will have a dedicated `Notifier` or `AsyncNotifier` (Riverpod) that interacts with Repositories and exposes a simple state object to the UI.

### Data Flow

```text
[ SQLite Database ]
       | (Repository interface)
[ Riverpod AsyncNotifier ] (Fetches data, handles offline fallback)
       | (Exposes AsyncValue<State>)
[ Flutter UI Widget ] (Renders based on data/loading/error state)
       | (User action or Screen Reader gesture)
[ Notifier Method Call ] (e.g., markLessonComplete())
       | (Updates Repository & invalidates local state)
[ UI Rebuilds ]
```

### Interfaces / APIs

- `ProviderScope`: Wraps the application to hold state.
- `NotifierProvider` / `AsyncNotifierProvider`: Used for feature-level state management (e.g., `lessonPlayerProvider`).
- `GoRouter`: Defines the routing table (e.g., `/home`, `/lesson/:id`).

### State Management

State will be localized as much as possible. Global state is reserved for overarching configurations (e.g., User Preferences, Sync Status).

- **Immutable State**: All state objects exposed by Notifiers must be immutable (using `freezed` or standard Dart `final` classes).
- **Dependency Injection**: Engine adapters (ASR, Pronunciation) will be injected via Riverpod providers, allowing easy mocking for tests.

### Accessibility implementation

Navigation and state transitions have profound impacts on accessibility:

- **Focus Recovery**: When navigating back (`context.pop()`), the focus must return to a logical element (e.g., the button that triggered the navigation). We will utilize Flutter's `FocusNode` and `FocusScope` API in conjunction with route listeners to explicitly manage focus upon transitions.
- **Announcement of Route Changes**: `go_router` transitions will trigger semantic announcements. A custom `NavigatorObserver` will be implemented to use `SemanticsService.announce()` to explicitly state the new screen name for TalkBack/VoiceOver users.
- **Loading States**: Asynchronous state transitions (e.g., saving progress) must provide audio/haptic feedback, not just visual spinners. `AsyncValue.loading` will trigger a localized semantic announcement (e.g., "Saving progress...").

### Offline / Sync behavior

- **Persistence**: UI state is fundamentally derived from SQLite. If the app is killed, the next launch reads from the local repository. We do not rely on persisting complex transient UI state across sessions unless it is saved to the database.
- **Sync Indication**: A global `syncStatusProvider` will track background sync operations (Pending, Syncing, Offline). This state will be accessible globally but will not block navigation or core flows.

## Alternatives Considered

- **Bloc/Cubit**: A strong contender for state management. *Rejected* because Riverpod offers a more concise syntax for dependency injection and managing asynchronous data streams, which is beneficial for the offline-first repository pattern.
- **Provider**: *Rejected* as it is largely superseded by Riverpod, which avoids `BuildContext` dependency for state access and provides better compile-time safety.
- **Standard Navigator 1.0**: *Rejected* because declarative routing (`go_router`) provides better handling of deep links and centralized route definition, making it easier to manage a consistent focus strategy.

## Testing Strategy

- **Unit Tests**: Test Notifiers/ViewModels independently by mocking Repositories and injected Engine Adapters.
- **Widget Tests**: Use `ProviderScope` with overrides to provide mock state and verify UI rendering and Semantic trees.
- **Integration Tests**: Verify navigation flows and explicit focus shifts using `integration_test` package. Check TalkBack/VoiceOver semantics trees across route changes.

## Migration and Rollout

- This design applies to new development. No migration from existing code is required.
- Implementation will begin with a basic routing scaffold and a single global state provider (e.g., Settings) before expanding to lesson flows.

## Known Risks and Limitations

- **Focus Management Complexity**: Explicitly managing focus on route changes in Flutter can be fragile. We must ensure custom route observers do not interfere with default OS accessibility behaviors.
- **Memory Management**: Over-use of global providers could impact the 2GB RAM constraint. State must be properly scoped and disposed of (using `.autoDispose` where appropriate).
