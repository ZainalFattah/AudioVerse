# App Module Breakdown

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers: User
Last updated: 2026-06-28
Related documents:

- [Software Architecture Document](software-architecture-document.md)
- [Software Requirements Specification](software-requirements-specification.md)

## 1. Purpose

This document outlines the breakdown of modules for the future AudioVerse English Flutter application. It maps architectural boundaries and functional requirements to concrete, implementable software modules without providing source code.

## 2. Module Principles

- **Separation of Concerns:** Each module focuses on a specific, isolated responsibility.
- **Dependency Inversion:** High-level policy modules do not depend on low-level detail modules. Both depend on abstractions (interfaces).
- **Loose Coupling:** Modules communicate through well-defined contracts.
- **Accessibility by Default:** Presentation modules handle accessibility concerns directly, rather than as an afterthought.

## 3. Module Inventory

### 3.1 Presentation Module

- **Responsibility:** Handles all user interface rendering, interactions, accessibility semantics (TalkBack/VoiceOver labels, gesture handling, focus order), and visual states (High Contrast, Blank Screen mode).
- **Sub-components:**
  - `AccessibilityInteraction`: Manages focus, semantic announcements, and gesture overrides.
  - `UIComponents`: Reusable, accessible widgets (buttons, progress bars, lists).
  - `Screens`: Onboarding, Home/Catalog, Lesson Player, Practice, Feedback, Settings.
- **Dependencies:** Domain, Audio (for audio cues), State Management.

### 3.2 Domain Module

- **Responsibility:** Contains the core business logic, use cases, and application state. Orchestrates the flow of data between repositories and presentation.
- **Sub-components:**
  - `LessonFlow`: Manages curriculum progression, attempt tracking, and scoring logic.
  - `Settings`: Manages user preferences (A11Y settings, offline modes).
  - `StateManagement`: Handles app state (e.g., using Riverpod/Bloc).
- **Dependencies:** Data Contracts, Engine Contracts.

### 3.3 Data Module

- **Responsibility:** Handles all local persistence, data parsing, and caching. Implements the repository contracts defined by the Domain module.
- **Sub-components:**
  - `LocalDatabase`: SQLite operations, schema, and migrations.
  - `ContentPackageParser`: Parses downloaded lesson ZIPs and JSON manifests.
  - `Repositories`: Implementations for `LessonRepository`, `ProgressRepository`, `SettingsRepository`.
- **Dependencies:** SQLite plugin, File System plugin.

### 3.4 Audio Module

- **Responsibility:** Manages the entire audio pipeline: playback of structured soundscapes, recording of practice attempts, and managing audio focus/interruptions.
- **Sub-components:**
  - `AudioPlayer`: Handles structured asset playback, mixing, and timing.
  - `AudioRecorder`: Manages ephemeral WAV capture (max 30s) and immediate deletion post-processing.
  - `AudioSession`: Handles OS-level interruptions (e.g., phone calls, screen reader ducking).
- **Dependencies:** Domain (contracts), Native Audio Plugins.

### 3.5 Speech Adapter Module

- **Responsibility:** Provides the abstraction layer and concrete implementations for Speech Recognition (ASR).
- **Sub-components:**
  - `ISpeechRecognitionEngine`: The interface.
  - `CandidateAdapters`: Implementations for Vosk, Whisper.cpp, or ML Kit (to be selected post-benchmark).
- **Dependencies:** Domain (contracts).

### 3.6 Pronunciation Adapter Module

- **Responsibility:** Provides the abstraction layer for evaluating transcribed speech against target text. Handles fuzzy matching and confidence scoring.
- **Sub-components:**
  - `IPronunciationEvaluationEngine`: The interface.
  - `EvaluationLogic`: Scoring algorithm handling homophones and typical ASR errors.
- **Dependencies:** Domain (contracts).

### 3.7 AI Tutor Adapter Module (Optional)

- **Responsibility:** Provides an isolated, optional engine for conversational practice, guarded by strict English-learning constraints.
- **Sub-components:**
  - `IAITutorEngine`: The interface.
  - `Guardrails`: Enforces safety and topic constraints.
  - `FallbackMode`: Handles offline graceful degradation.
- **Dependencies:** Domain (contracts).

### 3.8 Settings Module

- **Responsibility:** Manages all user-configurable options, including accessibility preferences, data clearing, and optional cloud sync toggles.
- **Sub-components:**
  - `AccessibilityPreferences`: High contrast, blank screen mode, scalable text.
  - `DataManagement`: UI to clear local storage.
- **Dependencies:** Presentation, Domain, Data.

### 3.9 Sync Module (Optional)

- **Responsibility:** Handles optional cloud synchronization via Supabase. Must operate transparently in the background without blocking offline usage.
- **Sub-components:**
  - `SyncEngine`: Background task manager.
  - `ConflictResolver`: Implements "Last-Write-Wins" logic based on updated timestamps.
  - `SupabaseAdapter`: Concrete implementation of cloud syncing.
- **Dependencies:** Data Module (for reading/writing local state).

## 4. Dependencies and Communication Flow

- The **Presentation** layer observes state from the **Domain** layer and dispatches actions/events.
- The **Domain** layer executes business logic and interfaces with the **Data** layer via repository interfaces.
- The **Domain** layer utilizes **Engine Adapters** (Audio, Speech, Pronunciation, AI) via abstract interfaces to perform complex tasks.
- The **Data** layer manages local state and communicates with the **Sync** layer to persist data to the cloud in the background.

## 5. Review Checklist

- Are boundaries clear? Yes.
- Are modules too broad or too fragmented? They represent standard domain boundaries without over-engineering.
- Do they support replaceable engines? Yes, via the Adapter modules.

## 6. Open Questions

- Specific state management approach is pending Technical Design.
- Which specific ASR implementation will be chosen?

## 7. Next Steps

Proceed to Phase 33 - Database Design.
