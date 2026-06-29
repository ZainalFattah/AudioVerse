# ADR-0004: Use Modular Engine Architecture

Project: AudioVerse English
Status: Accepted
Date: 2026-06-28
Decision owners: Jules
Related documents: [Modular Engine Architecture](../docs/modular-engine-architecture.md), [Software Architecture Document](../docs/software-architecture-document.md)

## Context

AudioVerse English relies on several complex technologies: Speech Recognition (ASR), Pronunciation Assessment, optional AI Tutors, and potentially Cloud Sync. The landscape for these technologies (especially AI and ASR) is rapidly changing. Hardcoding the application to a specific engine creates significant long-term risk.

## Decision

We will use a **Modular Engine Architecture** for all complex external dependencies. Core business logic will interact with these engines exclusively through abstract interfaces defined within the domain layer. Concrete implementations will be provided via adapter modules.

## Options Considered

| Option | Pros | Cons |
| --- | --- | --- |
| Modular Engines (Interfaces/Adapters) | Highly flexible, easy to swap engines (e.g., Vosk to Whisper), enables mocking for tests, isolates engine-specific errors. | Requires more initial boilerplate, slightly more complex setup. |
| Tight Coupling (Direct Integration) | Faster initial development, less abstraction. | Extremely difficult to swap engines later, tests become brittle, application logic becomes polluted with engine details. |

## Consequences

- We must define clear, robust interfaces for: `ISpeechRecognitionEngine`, `IPronunciationEvaluationEngine`, `IAITutorEngine`, `IAudioPipelineEngine`, and `ICloudSyncEngine`.
- Dependency injection will be required to provide concrete implementations at runtime.
- New engines can be integrated by writing a new adapter without touching core application code.

## Accessibility Impact

Neutral directly, but allows us to swap in better-performing engines that might provide faster feedback, improving the overall accessible experience.

## Offline Impact

Positive. This architecture allows us to easily implement "Offline Mock" adapters or strictly local adapters (like Vosk) alongside potential future cloud adapters.

## Performance Impact

Neutral. The overhead of interface dispatch is negligible.

## Security and Privacy Impact

Positive. It provides a clear boundary where data sanitization (e.g., stripping PII before sending to an AI tutor) can be enforced within the adapter.

## Follow-Up

- Finalize the specific methods and data types required for each engine interface.
