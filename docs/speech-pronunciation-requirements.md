# Speech and Pronunciation Requirements

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers:
Last updated: 2026-06-28
Related documents:
- [Software Requirements Specification (SRS)](software-requirements-specification.md)
- [Glossary](glossary.md)

## 1. Purpose

This document defines the requirements for Speech Recognition (ASR) and Pronunciation Assessment within AudioVerse English. Crucially, it establishes a strict separation between the process of converting speech to text (ASR) and the process of evaluating that speech against expected text (Pronunciation Assessment).

## 2. Scope

This document covers:
- Input constraints for user speech.
- Expected outputs and behavior of the Speech Recognition (ASR) engine.
- Requirements for the Pronunciation Evaluation engine.
- Requirements for user feedback based on assessment scores.
- Benchmark considerations for selecting an ASR engine suitable for offline, low-end device usage.

## 3. Assumptions

- Target devices are low-to-mid-range smartphones (Android and iOS).
- Operations must be performed entirely offline, using on-device models.
- Speech Recognition (ASR) and Pronunciation Assessment are conceptually distinct processes, even if a single library eventually performs both tasks (the architecture must treat them as separate interfaces).
- ASR engines will inherently make mistakes; Pronunciation Assessment must be tolerant of reasonable ASR errors.

## 4. Requirement Taxonomy

Requirements in this document follow a standardized ID format:
- `SR`: Speech Recognition Requirements
- `PRON`: Pronunciation Requirements

## 5. Speech Recognition Requirements (SR)

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| SR-001 | **ASR Independence:** The Speech Recognition engine must be implemented behind a defined interface (adapter) that isolates it from the core application logic. | High | Allows swapping engines (e.g., Vosk, Whisper.cpp) without rewriting the app, mitigating performance risks. | Code review verifies ASR is accessed only via an adapter interface. |
| SR-002 | **Offline Operation:** The ASR engine must operate 100% locally on the device without requiring an active internet connection. | High | Essential for the core offline-first mandate. | ASR successfully converts spoken audio to text while the device is in airplane mode. |
| SR-003 | **Expected Input:** The ASR engine must accept standardized, temporary audio formats as defined by AUD constraints (e.g., 16kHz WAV, max 30s). | High | Ensures consistent behavior and prevents memory exhaustion on low-end devices. | ASR adapter correctly processes a 30s 16kHz WAV file. |
| SR-004 | **Recognition Output:** The ASR engine must return the transcribed text, and optionally, word-level timestamps and confidence scores. | High | Transcribed text is the minimum requirement for the Pronunciation Engine. Confidence scores help handle ambiguous inputs. | ASR returns a string representing the recognized speech. |
| SR-005 | **Latency Constraint:** The ASR transcription process should begin returning results or complete within a reasonable timeframe (e.g., < 2 seconds for a 5-second utterance) on benchmark low-to-mid-range devices. | High | High latency degrades the learning loop and frustrates users. | ASR latency is profiled and meets constraints on target benchmark devices. |
| SR-006 | **Benchmark Mandate:** The choice of the primary ASR engine (e.g., Vosk vs Whisper.cpp) must be deferred until benchmark evidence is collected regarding accuracy, latency, memory usage, and battery impact on target devices. | High | Premature engine selection risks violating low-end device constraints. | An ADR exists documenting the ASR selection based on benchmark data. |

## 6. Pronunciation Assessment Requirements (PRON)

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| PRON-001 | **Assessment Independence:** The Pronunciation Assessment logic must be separate from the ASR engine. It evaluates the ASR output (or audio features) against the expected target text. | High | ASR is for transcription; Pronunciation is for scoring. Separating them allows for specialized scoring algorithms. | Architecture defines a distinct boundary between ASR and Pronunciation Assessment. |
| PRON-002 | **Scoring System:** The engine must generate a score (e.g., percentage, or categorical: Poor, Fair, Good, Excellent) representing how closely the user's speech matched the expected pronunciation. | High | Users need quantifiable feedback on their performance. | Given expected text and user speech (or ASR text), the engine returns a standardized score. |
| PRON-003 | **ASR Error Tolerance:** The scoring mechanism must account for common ASR transcription errors (e.g., homophones, minor misrecognitions) to avoid unfairly penalizing the user for technical limitations. | Medium | ASR on low-end devices will not be perfect. Users should not fail because the ASR misheard a correct pronunciation. | Scoring algorithm includes fuzzy matching or phonetic comparison techniques. |
| PRON-004 | **Feedback Generation:** The system must translate the numerical/categorical score into accessible user feedback (e.g., text strings and audio cues). | High | Scores must be presented in a way that is understandable and accessible to blind/low-vision users. | A score of "Poor" generates a specific, constructive text prompt and/or audio sound. |
| PRON-005 | **Uncertainty Handling:** If the ASR confidence is very low, or the speech is unintelligible, the feedback should prompt the user to try again rather than assigning a failing score. | Medium | Differentiates between poor pronunciation and bad audio capture. | Low confidence ASR input results in a "Please try again" prompt. |

## 7. Open Questions

- What is the minimum acceptable ASR confidence threshold before prompting a retry?
- Should the scoring system provide detailed phoneme-level feedback, or is sentence/word-level sufficient for the MVP?

## 8. Risks

- Finding an ASR model that is accurate enough for pronunciation assessment while remaining small enough for the 100MB app size limit and performant on low-end devices is a significant challenge.

## 9. Review Checklist

- [ ] Is Speech Recognition (ASR) clearly separated from Pronunciation Assessment?
- [ ] Are offline and performance constraints explicitly linked to the requirements?
- [ ] Are requirements testable?