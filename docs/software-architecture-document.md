# Software Architecture Document

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers: User
Last updated: 2026-06-28
Related documents:

- [Software Requirements Specification](software-requirements-specification.md)
- [Architecture Principles](architecture-principles.md)

## 1. Purpose

This Software Architecture Document (SAD) outlines the high-level system architecture, boundaries, layers, modules, data flow, deployment assumptions, and quality attributes of AudioVerse English. The document serves as the technical blueprint, satisfying the constraints and principles outlined for offline-first operation, accessibility, and modular engine interfaces.

## 2. System Context

AudioVerse English is a mobile application running on low-to-mid-range Android and iOS devices. The application operates primarily offline, relying on pre-downloaded content packages and local persistence.

```text
[ Learner ]
    |
    v (Voice, Touch, Gestures, Screen Reader)
+-------------------------------------------------+
|               AudioVerse English                |
|                                                 |
|  [ Content Packages ] <--> [ Local Database ]   |
|                                                 |
|  [ Audio Engine ]          [ Speech Engine ]    |
|  [ Pronunciation Engine ]  [ AI Tutor ]         |
+-------------------------------------------------+
    ^
    | (Optional Sync / Content Downloads)
    v
[ Cloud Backend (e.g., Supabase / CDN) ]
```

### 2.1 Assumptions and Deployment

- Target Platforms: Android (8.0+) and iOS (12+).
- Hardware: Minimum 2GB RAM, representing low-end/mid-range devices.
- Network: Offline-first; network access is optional and only utilized for content downloads and background sync.
- Backend: Supabase is considered for optional sync, but the core system does not strictly depend on it.

## 3. Architecture Layers

The architecture employs a layered approach to enforce separation of concerns, heavily relying on dependency injection to provide modular interfaces.

### 3.1 Presentation and Accessibility Layer

- **Responsibility:** Handles UI rendering, user interaction, screen reader (TalkBack/VoiceOver) integration, gesture capture, and high-contrast/blank-screen state toggling.
- **Traceability:** Links to A11Y-001 through A11Y-008.

### 3.2 Domain and State Management Layer

- **Responsibility:** Manages the core business logic, application state, learning flows, and orchestration between repositories and engines.
- **Traceability:** Links to FR-002, FR-006.

### 3.3 Engine Interface (Adapter) Layer

- **Responsibility:** Defines abstract interfaces (Contracts) for complex external or resource-intensive tasks (Speech Recognition, Pronunciation Assessment, AI Tutor, Audio). Implementations can be swapped dynamically.
- **Traceability:** Links to SR-001, PRON-001, AI-001.

### 3.4 Data and Repository Layer

- **Responsibility:** Manages persistence, content package parsing, and optional remote sync operations using local SQLite as the source of truth.
- **Traceability:** Links to OFF-002, SYNC-002, SEC-002.

## 4. Module Breakdown

### 4.1 Lesson Flow Module

Manages the curriculum, rendering lessons, and orchestrating practice sessions and progress recording.

### 4.2 Audio Orchestration Module

Handles playback of structured soundscapes (JSON-driven assets) and recording ephemeral audio (max 30s) for speaking practice.

### 4.3 Speech and Pronunciation Adapters

Abstract modules that define contracts for converting spoken audio to text, and assessing the transcribed text against a target string. Implementations (e.g., Vosk, Whisper.cpp, ML Kit) are bound at runtime via dependency injection. No model is hard-bound until benchmark evidence (Phase 25) exists.

### 4.4 Optional AI Tutor Module

An isolated module for conversation practice and feedback. It must enforce English-only guardrails and graceful degradation (failing gracefully if offline without breaking core app logic).

### 4.5 Local Sync Module

Manages SQLite transactions and optionally syncs progress with the cloud in the background. Handles conflict resolution silently.

## 5. Data Flow and Ephemeral Voice Processing

The system enforces strict ephemeral processing for voice data.

```text
[ Microphone ]
    |
    v (Temporary 16kHz WAV in memory/cache)
[ Speech Recognition Adapter ]
    |
    v (Transcribed Text + Confidence)
[ Pronunciation Assessment Adapter ]
    |
    v (Pronunciation Score)
[ Local Database (SQLite) ] <- Score is saved
    |
[ Cache Manager ] -> Deletes temporary WAV file immediately
```

- **Traceability:** Links to SEC-001 (Voice Recording Ephemerality).

## 6. Quality Attributes

### 6.1 Accessibility

- Core flows are fully operable without sight. UI elements map directly to semantic labels for TalkBack and VoiceOver. Visual feedback always has parallel audio/haptic cues.

### 6.2 Offline-First Reliability

- Progress, lessons, and settings are written directly to SQLite. No waiting for a network request is allowed in the core loop.

### 6.3 Replaceability and Performance

- Engine logic is decoupled. The application acts as an orchestrator, minimizing battery drain and avoiding thermal throttling by isolating resource-heavy processing to specific, short-lived adapter calls.

## 7. Open Questions and Next Steps

- **State Management Framework:** Which specific state management solution (e.g., Riverpod, Bloc) best balances testability and low boilerplate? (To be explored in Technical Design).
- **Dependency Injection Framework:** How will adapters be injected and managed to optimize memory usage?
- **ASR Benchmark:** What will be the chosen ASR engine based on Phase 25 benchmarks?

## 8. Quality Gate Status

- Ready for Technical Design once reviewed and approved.
