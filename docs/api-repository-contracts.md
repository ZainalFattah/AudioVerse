# API and Repository Contracts

Project: AudioVerse English
Status: Approved
Owner: Jules
Reviewers: User
Date: 2026-06-28
Related documents: [Jules Master Development Handbook](../jules/JULES_MASTER_DEVELOPMENT_HANDBOOK.md), [Database Design Document](database-design-document.md), [Software Architecture Document](software-architecture-document.md)

## 1. Purpose

This document defines the internal contracts (interfaces) between the Domain layer, Data Repositories, Engine Adapters, and Presentation layers for AudioVerse English. These contracts ensure loose coupling, modularity, and adherence to the architecture principles (e.g., offline-first, replaceable engines) without defining implementation details (source code).

## 2. Repository Contracts

Repository contracts abstract the physical storage (SQLite) from the Domain layer. All methods must be non-blocking (asynchronous) and resilient to network states.

### 2.1 ILessonRepository

**Responsibility**: Manages the retrieval of course, unit, and lesson metadata.

* `getLessonsByPackageId(packageId: UUID) -> List<Lesson>`
  * **Input**: The UUID of the content package.
  * **Output**: A list of Lesson domain entities ordered by `sequence_order`.
  * **Errors**: Returns empty list if package is not found.
* `getLessonById(lessonId: UUID) -> Lesson`
  * **Input**: The UUID of the lesson.
  * **Output**: The Lesson domain entity.
  * **Errors**: Throws `EntityNotFoundException`.

### 2.2 IProgressRepository

**Responsibility**: Tracks learner completion and scoring.

* `getUserProgressForLesson(lessonId: UUID) -> Progress`
  * **Input**: The UUID of the lesson.
  * **Output**: The Progress domain entity (status, high score). If none exists, returns a default 'Not Started' state.
* `updateUserProgress(lessonId: UUID, status: Enum, newScore: Int) -> Void`
  * **Input**: Lesson UUID, completion status, and latest attempt score.
  * **Output**: None (persists locally).
  * **Behavior**: Must update `highest_score` only if `newScore` > current `highest_score`. Updates `updated_at` and sets `sync_status` to Pending.

### 2.3 IContentRepository

**Responsibility**: Manages downloaded content packages and their manifests.

* `getInstalledPackages() -> List<ContentPackage>`
  * **Output**: List of available ContentPackage entities.
* `getPackageManifest(packageId: UUID) -> Manifest`
  * **Input**: Package UUID.
  * **Output**: Parsed JSON manifest defining soundscapes and content.
  * **Errors**: Throws `ManifestNotFoundException` or `ParseException`.

### 2.4 ISettingsRepository

**Responsibility**: Manages user preferences and accessibility settings.

* `getSetting(key: String, defaultValue: Any) -> Any`
  * **Input**: Setting key (e.g., `high_contrast_enabled`), default fallback value.
  * **Output**: The requested setting value.
* `updateSetting(key: String, value: Any) -> Void`
  * **Input**: Key and new value.
  * **Output**: None (persists locally). Sets `sync_status` to Pending if applicable.

## 3. Engine Adapter Contracts

Engine contracts provide abstract boundaries for complex functionalities (Speech, AI, Audio), ensuring implementations can be swapped (e.g., Vosk vs Whisper) without affecting the Domain logic.

### 3.1 ISpeechRecognitionEngine

**Responsibility**: Converts audio input to text locally.

* `initialize() -> Future<Void>`
  * **Behavior**: Loads models into memory.
  * **Errors**: Throws `EngineInitializationException` (e.g., insufficient memory).
* `startRecognition() -> Stream<String>`
  * **Output**: A stream of partial or final text transcripts.
* `stopRecognition() -> String`
  * **Output**: The final consolidated text transcript.

### 3.2 IPronunciationEvaluationEngine

**Responsibility**: Evaluates recognized text against target text.

* `evaluate(targetText: String, recognizedText: String) -> PronunciationResult`
  * **Input**: The expected sentence and the actual ASR output.
  * **Output**: A `PronunciationResult` containing a score (0-100) and specific feedback flags (e.g., homophone detected, low confidence).
  * **Behavior**: Must handle fuzzy matching to prevent unfair penalization due to ASR errors.

### 3.3 IAITutorEngine

**Responsibility**: Provides optional, conversational AI assistance.

* `generateResponse(prompt: String, context: SessionContext) -> AIResponse`
  * **Input**: User prompt and context (current lesson, previous turns).
  * **Output**: An `AIResponse` containing the text reply and safety flags.
  * **Errors**: Throws `SafetyViolationException` if out-of-bounds, or `EngineUnavailableException` if offline (for cloud models) or out of memory (for local models).

### 3.4 IAudioPipelineEngine

**Responsibility**: Orchestrates playback and recording.

* `playAsset(manifestPath: String) -> Future<Void>`
  * **Input**: Path to soundscape manifest or single audio file.
  * **Behavior**: Handles mixing, accessibility ducking (if TalkBack interrupts), and playback state.
* `startRecording() -> Future<String>`
  * **Output**: Temporary file path of the recorded WAV.
* `stopRecording() -> Future<Void>`

### 3.5 ICloudSyncEngine

**Responsibility**: Handles background synchronization of local state to the cloud.

* `syncPendingData() -> Future<SyncResult>`
  * **Behavior**: Queries Repositories for records where `sync_status` is Pending. Uploads them and updates status to Synced. Follows Last-Write-Wins based on `updated_at`.
  * **Errors**: Fails silently (graceful degradation) if offline or network times out.

## 4. Accessibility Implementation

* All contracts involving UI feedback (e.g., AI Response, Evaluation Result) must include necessary textual descriptions suitable for Screen Reader consumption.
* Audio pipeline contracts must respect OS-level audio focus events to duck/pause learning audio when a Screen Reader speaks.

## 5. Offline and Sync Behavior

* All Repositories operate entirely offline, reading from and writing to local SQLite.
* The `ICloudSyncEngine` is explicitly separated from the core Domain. Repositories do not call Sync directly; they merely update local state.
