# Software Requirements Specification (SRS)

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers:
Last updated: 2026-06-28
Related documents:
- [Project Charter](project-charter.md)
- [Stakeholder & User Model](stakeholder-user-model.md)
- [Glossary](glossary.md)
- [Domain Model](domain-model.md)

---

## 1. Purpose

This document outlines the software requirements for AudioVerse English, an offline-first, audio-based English learning platform designed primarily for blind and low-vision learners. It serves as the single source of truth for all requirements, guiding the architectural and technical design phases.

## 2. Scope

The scope of this SRS includes functional requirements, non-functional requirements, accessibility requirements, offline capabilities, audio/soundscape interactions, speech recognition, pronunciation assessment, AI tutor interactions, and data security/privacy constraints for the MVP and subsequent phases of AudioVerse English.

Features that are explicitly excluded from the MVP (e.g., complex social features, full cloud dependency, generative AI soundscapes) will not have requirements elaborated in this version of the document.

## 3. Audience

The intended audience for this document includes:
- Development and engineering teams (including AI agents) responsible for architecture and implementation.
- Product managers defining the product roadmap.
- QA and accessibility testers validating system behavior against requirements.
- Stakeholders reviewing project direction and scope.

## 4. Definitions

Please refer to the [Glossary](glossary.md) for detailed definitions of domain terms such as "Lesson", "Soundscape", "Speech Recognition", and "Pronunciation Evaluation".

## 5. Assumptions

- Target devices include low-to-mid-range Android and iOS smartphones.
- Users may have intermittent, limited, or completely unavailable internet connectivity.
- Accessibility tools (TalkBack on Android, VoiceOver on iOS) are presumed active and heavily utilized by the primary user base.

## 6. Requirement Taxonomy and Conventions

Requirements in this document follow a standardized ID format to ensure stable traceability.

| Prefix | Category | Description |
| --- | --- | --- |
| `FR` | Functional | Core product behavior, features, and user journeys. |
| `NFR` | Non-Functional | Performance, reliability, compatibility, and maintainability. |
| `A11Y` | Accessibility | Mandatory accessibility behaviors (TalkBack, high contrast, etc.). |
| `OFF` | Offline | Requirements for functioning without internet connectivity. |
| `AUD` | Audio | Soundscape, audio assets, playback, and recording rules. |
| `SR` | Speech Recognition | Converting user speech to text. |
| `PRON` | Pronunciation | Scoring and evaluating learner pronunciation against expected text. |
| `AI` | AI Tutor | Interactions with the optional, modular AI Tutor engine. |
| `DATA` | Data | Data schemas, retention, profiles, and migrations. |
| `SEC` | Security & Privacy | Protection of user data, voice recordings, and permissions. |
| `SYNC` | Sync | Optional cloud synchronization behavior and conflict resolution. |
| `TEST` | Test | specific testing criteria and testability needs. |
| `CONT` | Content | Content packages, curriculum hierarchy, and versioning. |

## 7. Requirements Traceability Matrix

(Placeholder: A separate Traceability Matrix document will link these requirement IDs to architecture components, technical designs, and test cases.)

---

## 8. Functional Requirements (FR)

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| FR-001 | **Onboarding:** The app shall provide an introductory flow guiding the user through initial setup and permission requests (e.g., microphone). | High | Ensures the user understands the app's purpose and grants necessary permissions before starting. | User can complete setup and grant permissions using TalkBack/VoiceOver; permissions are correctly saved. |
| FR-002 | **Lesson Catalog:** The app shall display a navigable list of locally available Courses, Units, and Lessons. | High | Users must be able to select what they want to learn from available content. | Catalog is fully navigable with screen readers; catalog correctly reflects available local content packages. |
| FR-003 | **Audio Playback:** The app shall provide controls to play, pause, and resume audio from Lessons and Soundscapes. | High | Core to the audio-first learning experience. | Audio plays correctly; pause/resume works reliably; playback state changes are audibly announced to screen readers. |
| FR-004 | **Speaking Practice:** The app shall allow users to record their speech for a practice attempt upon explicit user command. | High | Necessary for active language and pronunciation practice. | User can start and stop recording using accessible controls; recording is captured and passed to the assessment pipeline. |
| FR-005 | **Feedback Delivery:** The app shall deliver the result of a pronunciation assessment via text and audio feedback. | High | Users need to know how well they pronounced the target phrase to improve. | Feedback is generated after a recording; screen readers read the feedback automatically or upon focus. |
| FR-006 | **Progress Tracking:** The app shall record the completion status and scores of Lessons and Units to a local database. | High | Allows users to track their learning journey across sessions without relying on the cloud. | Completing a lesson updates its status locally; progress persists after app restart. |
| FR-007 | **Settings Management:** The app shall allow users to configure preferences such as playback speed and optional features (e.g., AI Tutor enablement). | Medium | Enhances usability and allows toggling of experimental or resource-intensive modular features. | Settings changes are saved locally and applied immediately to subsequent app usage. |
| FR-008 | **Content Package Management:** The app shall allow users to view downloaded content packages and delete them to free up space. | Medium | Essential for target low-end devices with limited storage capacity. | User can see a list of installed packages and successfully delete one, which removes it from local storage and the catalog. |

## 9. Non-Functional Requirements (NFR)

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| NFR-001 | **Device Compatibility:** The app must be installable and functional on Android devices with at least 2GB of RAM (e.g., Android Go devices) and iOS devices supported by the latest two major iOS versions. | High | AudioVerse English targets a diverse user base, including those with older, low-to-mid-range devices. | App successfully installs and passes core functional tests on a benchmark 2GB RAM Android device and older supported iOS devices. |
| NFR-002 | **Startup Performance:** The app should load to a usable, interactive state (ready for screen reader focus) within 3 seconds on mid-range devices, and 5 seconds on low-end devices. | Medium | Lengthy startup times frustrate visually impaired users and hinder short, frequent learning sessions. | Time-to-interactive metric is under defined limits in benchmark tests. |
| NFR-003 | **Offline Reliability:** Core learning flows, database reads/writes, and playback must function continuously and identically whether the device is connected to the internet, in airplane mode, or switching between networks. | High | Realizes the fundamental "offline-first" architectural constraint. | App passes a full lesson cycle while internet connection is rapidly toggled on and off without crashing or losing progress state. |
| NFR-004 | **Storage Efficiency:** Baseline installation of the app (excluding downloaded lesson content packages) must remain under 100MB on disk. | High | Low-end devices often have severe storage constraints. | Release build size (APK/IPA) and installed footprint without cache/content does not exceed 100MB. |
| NFR-005 | **Battery and Resource Constraints:** The app, during active speaking practice (running audio capture and ASR), must not cause severe thermal throttling or consume more than 15% battery per hour of continuous active use on benchmark mid-range devices. | Medium | ASR and AI models are resource-intensive; they must be optimized or degraded gracefully to avoid draining the user's device. | Battery usage profiling over a 1-hour simulated learning session stays within the 15% limit. |
| NFR-006 | **Maintainability (Modular Engines):** The Speech Recognition, Pronunciation Assessment, and AI Tutor systems must be implemented using distinct, replaceable interfaces (adapters), rather than being tightly coupled to the core application logic. | High | Essential to prevent vendor lock-in and allow for future upgrades (e.g., swapping Vosk for Whisper.cpp) without rewriting the app. | Architecture/code review verifies that engine logic is hidden behind generic interfaces and can be mocked entirely for tests. |

## 10. Accessibility Requirements (A11Y)

*See [Accessibility Requirements](accessibility-requirements.md) for full details.*

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| A11Y-001 | **Screen Reader Compatibility:** All interactive elements, states, and navigation flows must be fully compatible and logically structured for TalkBack (Android) and VoiceOver (iOS). | High | Essential for Total Blind users to operate the app. | The core learning flow can be completed with a screen reader without visual cues; focus order is logical. |
| A11Y-002 | **Blank Screen Mode:** The app must be fully operable with the screen completely dimmed or turned off. | High | Many Total Blind users navigate with their screens off for privacy and battery saving. | Core learning flow is completable with screen off/dimmed while using a screen reader. |
| A11Y-003 | **High Contrast Mode:** The app must support a high contrast visual theme that complies with WCAG 2.1 AAA standards. | High | Critical for Low Vision learners who rely on visual interfaces. | High contrast mode can be toggled; contrast ratios are >= 7:1. |
| A11Y-004 | **Scalable Text:** The application UI must gracefully adapt to OS-level text scaling (up to 200%). | High | Low Vision users often increase system font sizes. | All screens are usable at 200% system font size; no overlapping or hidden text. |
| A11Y-005 | **Gesture Navigation Support:** The app must support standard accessibility gesture navigation without conflicting with OS-level screen reader gestures. | High | Users rely on swipe gestures to navigate; custom app gestures must not interfere. | Swiping logically moves focus; no custom gestures block TalkBack/VoiceOver defaults. |
| A11Y-006 | **Audio and Haptic Feedback:** Critical actions must be accompanied by distinct non-intrusive audio and/or haptic cues. | Medium | Provides immediate confirmation without waiting for screen reader speech. | Haptic/audio cues trigger correctly on key interactions; cues can be toggled. |
| A11Y-007 | **Accessible Error Recovery:** Error messages must be announced immediately by the screen reader, clear, and provide a straightforward way to recover. | High | Blind users cannot visually scan the screen for error icons. | Screen reader announces errors and focus shifts to the recovery action. |
| A11Y-008 | **Audio-First Prompts:** Instructions should be available via audio, minimizing the need to read text with a screen reader. | Medium | Reduces cognitive load and speeds up learning. | Exercise instructions are spoken naturally before or alongside screen reader focus. |

## 11. Offline-First Requirements (OFF)

*See [Offline-First Requirements](offline-first-requirements.md) for full details.*

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| OFF-001 | **Core Functionality:** Core learning flows (catalog, playback, practice, local SR/scoring) must function without internet. | High | Realizes the offline-first mandate. | App can complete a full lesson cycle while in airplane mode using downloaded content. |
| OFF-002 | **Local Progress Persistence:** User progress and settings must instantly save to the local SQLite database. | High | Prevents data loss when offline or closing the app. | Progress is updated locally immediately upon lesson completion and persists after app restart. |
| OFF-003 | **Content Availability:** Downloaded packages remain accessible indefinitely without server validation. | High | Users should not be locked out of downloaded content if they stay offline for long periods. | Content downloaded months ago opens successfully while offline. |
| OFF-004 | **Graceful Degradation:** Cloud-dependent features invoked while offline must gracefully degrade or fallback. | High | Prevents UI lockups or crashes when offline. | Attempting a cloud action while offline announces an accessible error/fallback and allows the user to continue. |

## 12. Audio and Soundscape Requirements (AUD)

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| AUD-001 | [Placeholder] | | | |

## 13. Content and Curriculum Requirements (CONT)

*See [Content and Curriculum Requirements](content-curriculum-requirements.md) for full details.*

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| CONT-001 | **Curriculum Hierarchy:** The curriculum must be structured hierarchically (Course -> Unit -> Lesson). | High | Organizes content into a logical, accessible progression. | Database schema successfully models this hierarchy; UI displays it correctly. |
| CONT-002 | **Lesson Structure:** Lessons must include metadata, audio prompts, target phrases, and full text transcripts. | High | Transcripts are essential for Low Vision users and useful for testing. | A loaded lesson exposes all required fields, including transcripts, to the app layer. |
| CONT-003 | **Self-Contained Packages:** Content is distributed in self-contained packages requiring no network access once downloaded. | High | Necessary for the offline-first mandate. | App can ingest a content package ZIP and play the lesson entirely offline. |
| CONT-004 | **Versioning and Migrations:** Content packages must be versioned. Updating a package must not lose user progress. | High | Enables content fixes without punishing the learner's progress. | Ingesting v2 of a package retains the completion status from v1. |

## 14. Speech Recognition Requirements (SR)

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| SR-001 | [Placeholder] | | | |

## 15. Pronunciation Assessment Requirements (PRON)

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| PRON-001 | [Placeholder] | | | |

## 16. AI Tutor Requirements (AI)

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| AI-001 | [Placeholder] | | | |

## 17. Security, Privacy, and Data Requirements (SEC / DATA)

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| SEC-001 | [Placeholder] | | | |

## 18. Sync Requirements (SYNC)

*See [Offline-First Requirements](offline-first-requirements.md) for full details.*

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| SYNC-001 | **Optional Sync:** Cloud synchronization must be strictly optional. | High | Ensures the app remains fully usable for users who prefer not to create accounts or use data. | A user can use all core features indefinitely without ever signing in or enabling sync. |
| SYNC-002 | **Local Source of Truth during Offline:** The local database is the sole source of truth when offline; the app must never block progress waiting for network. | High | Maintains the offline-first user experience. | No "waiting for network" spinners or blocks appear during core flows when offline. |
| SYNC-003 | **Conflict Resolution:** Sync engine resolves conflicts, defaulting to merging progress or last-write-wins. | High | Ensures data integrity across devices. | Syncing after offline progress resolves conflicts without crashing, preferring the most recent change. |
| SYNC-004 | **Sync State Visibility:** The app must provide an accessible way to check sync status (synced, pending, offline). | Medium | Allows users to confirm if their data is backed up. | Screen reader can announce the current sync state in settings. |
| SYNC-005 | **Failure Tolerance:** Failed sync operations must queue for later background retry without interrupting the user. | High | Network instability shouldn't ruin the learning experience. | Disconnecting during a sync attempt results in a silent background queueing, not an intrusive modal. |

---

## 19. Risks

- The major risk during the requirements phase is "scope creep," especially regarding AI functionality and cloud synchronization. These must remain explicitly optional and modular, prioritizing the offline-first core.

## 20. Open Questions

- None at this stage. Detailed requirements phases will likely generate specific questions regarding engine limits and content structuring.

## 21. Review Checklist

- [ ] Does the structure map to all required ID categories?
- [ ] Is the document ready to receive detailed requirements without architectural conflation?

## 22. Change Log

| Date | Version | Author | Changes |
| --- | --- | --- | --- |
| 2026-06-28 | 0.1 | Jules | Initial SRS Framework creation (Phase 08) |
| 2026-06-28 | 0.2 | Jules | Added Functional Requirements (Phase 09) |
| 2026-06-28 | 0.3 | Jules | Added Non-Functional Requirements (Phase 10) |
| 2026-06-28 | 0.4 | Jules | Added Accessibility Requirements (Phase 11) |
| 2026-06-28 | 0.5 | Jules | Added Offline-First and Sync Requirements (Phase 12) |
| 2026-06-28 | 0.6 | Jules | Added Content and Curriculum Requirements (Phase 13) |
