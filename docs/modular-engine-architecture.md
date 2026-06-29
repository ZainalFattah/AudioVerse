# Modular Engine Interface Architecture

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers: User
Last updated: 2026-06-28
Related documents:

- [Software Architecture Document](software-architecture-document.md)
- [Architecture Principles](architecture-principles.md)
- [Software Requirements Specification](software-requirements-specification.md)

## 1. Purpose

This document defines the conceptual interfaces for the core engines in AudioVerse English: Speech Recognition (ASR), Pronunciation Assessment, AI Tutor, Audio Pipeline, and Cloud Sync. This architecture ensures that complex, resource-heavy, or rapidly changing implementations are isolated behind abstract adapter interfaces. This fulfills the principle of replaceability and prevents the core application logic from being tightly coupled to specific third-party tools or models.

## 2. Scope

This document specifies:

- The abstract interfaces (contracts) for each modular engine.
- The responsibilities of the adapters implementing these interfaces.
- The required failure and offline degradation behaviors.
- The overarching testing strategy for engine boundaries.

## 3. Key Principles

- **Replaceability:** Any concrete engine implementation (e.g., Vosk, Whisper.cpp, ML Kit) must be swappable without modifying the core domain logic.
- **Loose Coupling:** The core application interacts only with interfaces. Dependency injection provides the concrete adapter at runtime.
- **Ephemerality:** Interfaces dealing with sensitive data (like voice audio) must enforce immediate cleanup post-processing.
- **Graceful Degradation:** All interfaces must gracefully handle failures, especially offline scenarios for optional cloud-dependent engines.

## 4. Engine Interfaces

### 4.1 Speech Recognition (ASR) Engine

**Responsibility:** Converts spoken audio into text locally on the device.

**Conceptual Interface (`ISpeechRecognitionEngine`):**

- **Inputs:** `AudioFilePath` (path to a temporary 16kHz WAV file, max 30s).
- **Outputs:** `TranscriptionResult` containing:
  - `transcribedText` (String)
  - `confidenceScore` (Float, 0.0 to 1.0)
  - `wordTimestamps` (List of Objects, optional)
- **Errors/Failures:**
  - `AudioFormatNotSupportedError`
  - `EngineInitializationError`
  - `TranscriptionFailedError` (e.g., audio too noisy)

**Adapter Responsibilities:**

- The adapter (e.g., `VoskAdapter` or `WhisperAdapter`) must load the local acoustic model.
- It must parse the specific output format of the underlying library into the standardized `TranscriptionResult`.
- It must ensure the engine operates within the defined latency constraints.

### 4.2 Pronunciation Evaluation Engine

**Responsibility:** Evaluates a learner's transcribed speech against the expected text and generates a score. This is distinctly separate from the ASR engine.

**Conceptual Interface (`IPronunciationEvaluationEngine`):**

- **Inputs:**
  - `expectedText` (String)
  - `actualTranscription` (TranscriptionResult from ASR)
- **Outputs:** `PronunciationScoreResult` containing:
  - `overallScore` (Float/Percentage)
  - `feedbackCategory` (Enum: Excellent, Good, NeedsPractice, Unclear)
  - `detailedFeedback` (String/Accessible Message)
- **Errors/Failures:**
  - `EvaluationFailedError`

**Adapter Responsibilities:**

- Implement fuzzy matching or phonetic comparison to tolerate common ASR transcription errors (e.g., homophones).
- Handle scenarios where the ASR `confidenceScore` is too low, returning a feedback category like `Unclear` (prompting a retry) instead of penalizing the user's pronunciation score.

### 4.3 AI Tutor Engine

**Responsibility:** Provides conversational practice and feedback. This is an optional, modular engine.

**Conceptual Interface (`IAITutorEngine`):**

- **Inputs:**
  - `userPrompt` (String, transcribed from ASR)
  - `conversationContext` (Object, recent conversation history)
- **Outputs:** `AITutorResponse` containing:
  - `tutorReplyText` (String)
  - `suggestedCorrections` (List of Strings, optional)
- **Errors/Failures:**
  - `EngineUnavailableOfflineError` (if cloud-based)
  - `ContentSafetyViolationError` (if input triggers guardrails)
  - `ModelLoadError` (if local model fails)

**Adapter Responsibilities:**

- Implement strict English-learning guardrails, rejecting off-topic or unsafe prompts.
- Implement graceful degradation: if the engine is offline-incapable and the network drops, the adapter must catch this and return an `EngineUnavailableOfflineError` that the domain layer can handle cleanly.

### 4.4 Audio Pipeline Engine

**Responsibility:** Orchestrates playback of structured soundscapes and records temporary audio for speaking practice.

**Conceptual Interface (`IAudioPipelineEngine`):**

- **Inputs:**
  - Playback: `SoundscapeManifest` (JSON struct), `Action` (Play, Pause, Stop).
  - Recording: `Action` (Start, Stop), `OutputConfig` (16kHz WAV).
- **Outputs:**
  - Playback: `PlaybackState` events (Playing, Paused, Completed, Interrupted).
  - Recording: `AudioFilePath` (path to recorded temporary file).
- **Errors/Failures:**
  - `AudioAssetNotFoundError`
  - `MicrophonePermissionDeniedError`
  - `RecordingHardwareError`

**Adapter Responsibilities:**

- Ensure recording strictly halts at the 30-second cap.
- Manage spatial mixing of audio layers based on the soundscape manifest.
- Handle system audio interruptions (e.g., incoming calls, TalkBack spoken feedback) gracefully.

### 4.5 Cloud Sync Engine

**Responsibility:** Optionally synchronizes local SQLite progress to a remote backend.

**Conceptual Interface (`ICloudSyncEngine`):**

- **Inputs:**
  - `localChanges` (List of localized data transaction records)
- **Outputs:** `SyncResult` containing:
  - `status` (Success, ConflictResolved, FailedOffline)
  - `remoteUpdates` (List of data records to apply locally)
- **Errors/Failures:**
  - `NetworkUnavailableError`
  - `AuthenticationError`

**Adapter Responsibilities:**

- Ensure all sync attempts happen on a background thread.
- If offline, the adapter must immediately return `NetworkUnavailableError` without blocking or retrying infinitely on the main thread. The core app relies on the local database and ignores this error, queuing the sync for later.
- Handle data conflicts using the last-write-wins (timestamp-based) resolution strategy.

## 5. Testing Strategy

The modular engine interfaces enable robust testing:

- **Domain Logic Tests:** The core application logic can be unit-tested by injecting Mock adapters that simulate engine behaviors (e.g., a `MockASRAdapter` that instantly returns pre-defined text).
- **Adapter Integration Tests:** Adapters can be tested in isolation to verify they correctly translate specific library outputs (e.g., testing the `VoskAdapter` against a known audio file to ensure it outputs a valid `TranscriptionResult`).
- **Failure Simulation:** Mocks will be used to simulate network drops for the AI Tutor and Sync adapters to verify the core app degrades gracefully and maintains the offline-first experience.

## 6. Open Questions

- What specific dependency injection framework (e.g., `get_it`, Riverpod) will be used in Flutter to bind these interfaces to concrete adapters at runtime?
- For local AI and ASR models, how will the large model files be distributed and loaded by the adapters without exceeding the 100MB base app size limit? (Likely requiring post-install download logic).
