# Phase 21 - Modular Engine Interface Architecture Execution Notes

Project: AudioVerse English
Phase: Phase 21
Status: Complete
Date: 2026-06-28

## Documents created or changed

- `docs/modular-engine-architecture.md` (Created)
- `tasks/phase-21-modular-engine-architecture.md` (This file)
- `docs/documentation-inventory.md` (Updated status - pending next step)

## Key decisions

- The architecture requires abstract interfaces (`ISpeechRecognitionEngine`, `IPronunciationEvaluationEngine`, `IAITutorEngine`, `IAudioPipelineEngine`, `ICloudSyncEngine`) for core engines.
- Adapters are responsible for handling concrete implementations, error translation, and enforcing constraints (like English-only guardrails for AI, or 30s limits for Audio).
- Offline degradation behavior is pushed to the adapter boundary, ensuring the core domain logic remains offline-first.

## Open questions

- What specific dependency injection framework (e.g., `get_it`, Riverpod) will be used in Flutter to bind these interfaces?
- How will the large model files for local AI and ASR be distributed and loaded by the adapters without exceeding the 100MB base app size limit?

## Risks

- Managing adapter versions and ensuring consistent error handling across different underlying engines (e.g., Vosk vs Whisper) could become complex.

## Quality gate result

- Pass. The architecture successfully defines conceptual boundaries, fulfilling the principle of modularity without locking into specific technologies prematurely.

## Recommended next phase

- Phase 22 - Data Architecture and SQLite Strategy
