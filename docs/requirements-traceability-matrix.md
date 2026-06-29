# Requirements Traceability Matrix

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers:
Last updated: 2026-06-28
Related documents:

- [Software Requirements Specification](software-requirements-specification.md)

## 1. Purpose

This document provides a trace from the software requirements defined in the Software Requirements Specification (SRS) through to the planned architecture modules, priority, validation strategy, and status. It ensures that every approved requirement is accounted for in the system design and test plan.

## 2. Matrix

| Requirement ID | Description | Priority | Source | Planned Architecture/Module | Planned Validation (Test Type) | Status | Owner |
| --- | --- | --- | --- | --- | --- | --- | --- |
| FR-001 | **Onboarding:** The app shall provide an introductory flow guiding the user through initial setup and permission requests (e.g., microphone). | High | SRS | Core Application / UI Layer | Integration Test | Planned | Jules |
| FR-002 | **Lesson Catalog:** The app shall display a navigable list of locally available Courses, Units, and Lessons. | High | SRS | Core Application / UI Layer | Integration Test | Planned | Jules |
| FR-003 | **Audio Playback:** The app shall provide controls to play, pause, and resume audio from Lessons and Soundscapes. | High | SRS | Core Application / UI Layer | Integration Test | Planned | Jules |
| FR-004 | **Speaking Practice:** The app shall allow users to record their speech for a practice attempt upon explicit user command. | High | SRS | Core Application / UI Layer | Integration Test | Planned | Jules |
| FR-005 | **Feedback Delivery:** The app shall deliver the result of a pronunciation assessment via text and audio feedback. | High | SRS | Core Application / UI Layer | Integration Test | Planned | Jules |
| FR-006 | **Progress Tracking:** The app shall record the completion status and scores of Lessons and Units to a local database. | High | SRS | Core Application / UI Layer | Integration Test | Planned | Jules |
| FR-007 | **Settings Management:** The app shall allow users to configure preferences such as playback speed and optional features (e.g., AI Tutor enablement). | Medium | SRS | Core Application / UI Layer | Integration Test | Planned | Jules |
| FR-008 | **Content Package Management:** The app shall allow users to view downloaded content packages and delete them to free up space. | Medium | SRS | Core Application / UI Layer | Integration Test | Planned | Jules |
| NFR-001 | **Device Compatibility:** The app must be installable and functional on Android devices with at least 2GB of RAM (e.g., Android Go devices) and iOS devices supported by the latest two major iOS versions. | High | SRS | System Infrastructure | Performance / Benchmark Test | Planned | Jules |
| NFR-002 | **Startup Performance:** The app should load to a usable, interactive state (ready for screen reader focus) within 3 seconds on mid-range devices, and 5 seconds on low-end devices. | Medium | SRS | System Infrastructure | Performance / Benchmark Test | Planned | Jules |
| NFR-003 | **Offline Reliability:** Core learning flows, database reads/writes, and playback must function continuously and identically whether the device is connected to the internet, in airplane mode, or switching between networks. | High | SRS | System Infrastructure | Performance / Benchmark Test | Planned | Jules |
| NFR-004 | **Storage Efficiency:** Baseline installation of the app (excluding downloaded lesson content packages) must remain under 100MB on disk. | High | SRS | System Infrastructure | Performance / Benchmark Test | Planned | Jules |
| NFR-005 | **Battery and Resource Constraints:** The app, during active speaking practice (running audio capture and ASR), must not cause severe thermal throttling or consume more than 15% battery per hour of continuous active use on benchmark mid-range devices. | Medium | SRS | System Infrastructure | Performance / Benchmark Test | Planned | Jules |
| NFR-006 | **Maintainability (Modular Engines):** The Speech Recognition, Pronunciation Assessment, and AI Tutor systems must be implemented using distinct, replaceable interfaces (adapters), rather than being tightly coupled to the core application logic. | High | SRS | System Infrastructure | Performance / Benchmark Test | Planned | Jules |
| A11Y-001 | **Screen Reader Compatibility:** All interactive elements, states, and navigation flows must be fully compatible and logically structured for TalkBack (Android) and VoiceOver (iOS). | High | SRS | Accessibility Interaction Layer | Accessibility Test / Screen Reader Check | Planned | Jules |
| A11Y-002 | **Blank Screen Mode:** The app must be fully operable with the screen completely dimmed or turned off. | High | SRS | Accessibility Interaction Layer | Accessibility Test / Screen Reader Check | Planned | Jules |
| A11Y-003 | **High Contrast Mode:** The app must support a high contrast visual theme that complies with WCAG 2.1 AAA standards. | High | SRS | Accessibility Interaction Layer | Accessibility Test / Screen Reader Check | Planned | Jules |
| A11Y-004 | **Scalable Text:** The application UI must gracefully adapt to OS-level text scaling (up to 200%). | High | SRS | Accessibility Interaction Layer | Accessibility Test / Screen Reader Check | Planned | Jules |
| A11Y-005 | **Gesture Navigation Support:** The app must support standard accessibility gesture navigation without conflicting with OS-level screen reader gestures. | High | SRS | Accessibility Interaction Layer | Accessibility Test / Screen Reader Check | Planned | Jules |
| A11Y-006 | **Audio and Haptic Feedback:** Critical actions must be accompanied by distinct non-intrusive audio and/or haptic cues. | Medium | SRS | Accessibility Interaction Layer | Accessibility Test / Screen Reader Check | Planned | Jules |
| A11Y-007 | **Accessible Error Recovery:** Error messages must be announced immediately by the screen reader, clear, and provide a straightforward way to recover. | High | SRS | Accessibility Interaction Layer | Accessibility Test / Screen Reader Check | Planned | Jules |
| A11Y-008 | **Audio-First Prompts:** Instructions should be available via audio, minimizing the need to read text with a screen reader. | Medium | SRS | Accessibility Interaction Layer | Accessibility Test / Screen Reader Check | Planned | Jules |
| OFF-001 | **Core Functionality:** Core learning flows (catalog, playback, practice, local SR/scoring) must function without internet. | High | SRS | Data Layer / Offline Storage | Offline Integration Test | Planned | Jules |
| OFF-002 | **Local Progress Persistence:** User progress and settings must instantly save to the local SQLite database. | High | SRS | Data Layer / Offline Storage | Offline Integration Test | Planned | Jules |
| OFF-003 | **Content Availability:** Downloaded packages remain accessible indefinitely without server validation. | High | SRS | Data Layer / Offline Storage | Offline Integration Test | Planned | Jules |
| OFF-004 | **Graceful Degradation:** Cloud-dependent features invoked while offline must gracefully degrade or fallback. | High | SRS | Data Layer / Offline Storage | Offline Integration Test | Planned | Jules |
| AUD-001 | **Asset Format & Quality:** Audio assets must be MP3 or Ogg Vorbis, targeting 64-96kbps to balance clarity and file size. | High | SRS | Audio Orchestration Layer | Audio Pipeline Test | Planned | Jules |
| AUD-002 | **Soundscape Manifest:** The learning experience is driven by a JSON manifest defining a deterministic sequence of audio events, explicit timings, and spatial cues. | High | SRS | Audio Orchestration Layer | Audio Pipeline Test | Planned | Jules |
| AUD-003 | **Accessible Playback Controls:** Users must have granular control (Play, Pause, Skip +/- 10s, segment skip) and speed adjustments (0.5x to 1.5x, pitch-preserved). | High | SRS | Audio Orchestration Layer | Audio Pipeline Test | Planned | Jules |
| AUD-004 | **Screen Reader Audio Focus:** Playback must duck or pause appropriately when TalkBack/VoiceOver announcements occur. | High | SRS | Audio Orchestration Layer | Audio Pipeline Test | Planned | Jules |
| AUD-005 | **Recording Constraints:** Microphone recording must be stored temporarily, strictly limited in length (e.g., 30s), use an optimal format (e.g., 16kHz WAV), and be aggressively deleted after use. | High | SRS | Audio Orchestration Layer | Audio Pipeline Test | Planned | Jules |
| AUD-006 | **Mandatory Transcripts:** Every speech audio asset MUST have a corresponding textual transcript linkable in the soundscape manifest. | High | SRS | Audio Orchestration Layer | Audio Pipeline Test | Planned | Jules |
| CONT-001 | **Curriculum Hierarchy:** The curriculum must be structured hierarchically (Course -> Unit -> Lesson). | High | SRS | Content Management / Repository | Data Ingestion Test | Planned | Jules |
| CONT-002 | **Lesson Structure:** Lessons must include metadata, audio prompts, target phrases, and full text transcripts. | High | SRS | Content Management / Repository | Data Ingestion Test | Planned | Jules |
| CONT-003 | **Self-Contained Packages:** Content is distributed in self-contained packages requiring no network access once downloaded. | High | SRS | Content Management / Repository | Data Ingestion Test | Planned | Jules |
| CONT-004 | **Versioning and Migrations:** Content packages must be versioned. Updating a package must not lose user progress. | High | SRS | Content Management / Repository | Data Ingestion Test | Planned | Jules |
| SR-001 | **ASR Independence:** The Speech Recognition engine must be implemented behind a defined interface (adapter) that isolates it from the core application logic. | High | SRS | Speech Adapter Layer | Adapter Integration Test | Planned | Jules |
| SR-002 | **Offline Operation:** The ASR engine must operate 100% locally on the device without requiring an active internet connection. | High | SRS | Speech Adapter Layer | Adapter Integration Test | Planned | Jules |
| SR-003 | **Expected Input:** The ASR engine must accept standardized, temporary audio formats (e.g., 16kHz WAV, max 30s). | High | SRS | Speech Adapter Layer | Adapter Integration Test | Planned | Jules |
| SR-004 | **Recognition Output:** The ASR engine must return the transcribed text, and optionally, word-level timestamps and confidence scores. | High | SRS | Speech Adapter Layer | Adapter Integration Test | Planned | Jules |
| SR-005 | **Latency Constraint:** The ASR transcription process should complete within a reasonable timeframe (e.g., < 2s for a 5s utterance). | High | SRS | Speech Adapter Layer | Adapter Integration Test | Planned | Jules |
| SR-006 | **Benchmark Mandate:** The choice of primary ASR engine must be deferred until benchmark evidence is collected. | High | SRS | Speech Adapter Layer | Adapter Integration Test | Planned | Jules |
| PRON-001 | **Assessment Independence:** The Pronunciation Assessment logic must be separate from the ASR engine. | High | SRS | Pronunciation Evaluation Layer | Unit Test / Module Test | Planned | Jules |
| PRON-002 | **Scoring System:** The engine must generate a score (e.g., categorical or percentage) representing pronunciation accuracy. | High | SRS | Pronunciation Evaluation Layer | Unit Test / Module Test | Planned | Jules |
| PRON-003 | **ASR Error Tolerance:** The scoring mechanism must account for common ASR transcription errors (homophones, etc.). | Medium | SRS | Pronunciation Evaluation Layer | Unit Test / Module Test | Planned | Jules |
| PRON-004 | **Feedback Generation:** The system must translate the score into accessible user feedback (text strings and audio cues). | High | SRS | Pronunciation Evaluation Layer | Unit Test / Module Test | Planned | Jules |
| PRON-005 | **Uncertainty Handling:** If ASR confidence is very low, feedback should prompt the user to try again. | Medium | SRS | Pronunciation Evaluation Layer | Unit Test / Module Test | Planned | Jules |
| AI-001 | **Modularity and Replaceability:** The AI Tutor must be implemented behind a defined adapter interface, completely decoupled from core lesson logic. | High | SRS | AI Tutor Adapter Layer | Adapter Test / Fallback Test | Planned | Jules |
| AI-002 | **Optional Feature:** The AI Tutor must be strictly optional. Core app functionality must not break when disabled. | High | SRS | AI Tutor Adapter Layer | Adapter Test / Fallback Test | Planned | Jules |
| AI-003 | **Clear Separation from ASR:** The AI Tutor must not be used for Speech Recognition or Pronunciation Assessment. | High | SRS | AI Tutor Adapter Layer | Adapter Test / Fallback Test | Planned | Jules |
| AI-004 | **Graceful Degradation (Offline):** If cloud-backed, the AI Tutor must fail gracefully with an accessible message when offline. | High | SRS | AI Tutor Adapter Layer | Adapter Test / Fallback Test | Planned | Jules |
| AI-005 | **Content Safety Guardrails:** The AI Tutor must be restricted to English language learning topics and reject unsafe content. | High | SRS | AI Tutor Adapter Layer | Adapter Test / Fallback Test | Planned | Jules |
| AI-006 | **Benchmark Requirement (Local AI):** Any local embedded AI model must be benchmarked on target low-end devices before integration. | High | SRS | AI Tutor Adapter Layer | Adapter Test / Fallback Test | Planned | Jules |
| AI-007 | **Accessible Output:** Output from the AI Tutor must be compatible with screen readers and audio playback. | High | SRS | AI Tutor Adapter Layer | Adapter Test / Fallback Test | Planned | Jules |
| SEC-001 | **Voice Recording Ephemerality:** Voice recordings must be aggressively deleted immediately after processing and never permanently stored or synced. | High | SRS | Security / Data Management Layer | Security Review / Data Integrity Test | Planned | Jules |
| SEC-002 | **Local-First Storage:** The SQLite database is the absolute source of truth; progress and profile data reside locally by default. | High | SRS | Security / Data Management Layer | Security Review / Data Integrity Test | Planned | Jules |
| SEC-003 | **Opt-In Sync:** Any cloud synchronization must require explicit, informed user consent. | High | SRS | Security / Data Management Layer | Security Review / Data Integrity Test | Planned | Jules |
| SEC-004 | **Data Deletion Control:** Users must have a clear, accessible way to delete all local progress, profile, and downloaded content data. | High | SRS | Security / Data Management Layer | Security Review / Data Integrity Test | Planned | Jules |
| DATA-001 | **Minimal Telemetry:** If telemetry is collected, it must be anonymous, strictly related to app stability/usage, and opt-in. | Medium | SRS | Security / Data Management Layer | Security Review / Data Integrity Test | Planned | Jules |
| SYNC-001 | **Optional Sync:** Cloud synchronization must be strictly optional. | High | SRS | Sync Layer | Sync / Conflict Resolution Test | Planned | Jules |
| SYNC-002 | **Local Source of Truth during Offline:** The local database is the sole source of truth when offline; the app must never block progress waiting for network. | High | SRS | Sync Layer | Sync / Conflict Resolution Test | Planned | Jules |
| SYNC-003 | **Conflict Resolution:** Sync engine resolves conflicts, defaulting to merging progress or last-write-wins. | High | SRS | Sync Layer | Sync / Conflict Resolution Test | Planned | Jules |
| SYNC-004 | **Sync State Visibility:** The app must provide an accessible way to check sync status (synced, pending, offline). | Medium | SRS | Sync Layer | Sync / Conflict Resolution Test | Planned | Jules |
| SYNC-005 | **Failure Tolerance:** Failed sync operations must queue for later background retry without interrupting the user. | High | SRS | Sync Layer | Sync / Conflict Resolution Test | Planned | Jules |
