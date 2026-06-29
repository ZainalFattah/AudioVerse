# Pronunciation Evaluation Architecture

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers:
Last updated: 2026-06-28
Related documents:

- [Speech and Pronunciation Requirements](speech-pronunciation-requirements.md)
- [Software Architecture Document](software-architecture-document.md)

## 1. Purpose

This document defines the Pronunciation Assessment architecture and feedback pipeline for AudioVerse English. It explicitly outlines how pronunciation scoring is conducted separately from Speech Recognition (ASR), ensuring modularity, clear component responsibilities, and effective learner feedback.

## 2. Scope

This architecture covers:

- Separation of Speech Recognition (ASR) from Pronunciation Evaluation.
- Handling of expected text and audio features.
- The scoring mechanism for pronunciation.
- Handling of confidence and uncertainty from ASR outputs.
- Feedback generation pipeline.

## 3. Architecture Overview

The Pronunciation Evaluation Engine sits strictly downstream of the Speech Recognition (ASR) Engine.

```text
Microphone
  -> Audio Pipeline
  -> Speech Recognition (ASR) Adapter
  -> Pronunciation Evaluation Adapter
  -> Scoring
  -> Feedback Generator
```

This ensures that the pronunciation assessment logic evaluates the result of the ASR (transcribed text, timestamps, phonetic outputs, or audio features) against the *expected text*, without being coupled to a specific ASR model.

## 4. Components and Responsibilities

### 4.1 Expected Text

The Pronunciation Engine must receive the expected text (what the learner was prompted to say) as an input alongside the user's audio or ASR output.

- **Source:** The expected text is defined in the lesson content manifest.
- **Processing:** The engine compares the recognized speech against this expected text.

### 4.2 Audio Features and ASR Output

The engine evaluates pronunciation based on available data from the ASR step:

- **Transcribed Text:** The raw string recognized by the ASR.
- **Word/Phoneme Timestamps:** If provided by the ASR, these can be used for rhythm or detailed phonetic assessment.
- **Confidence Scores:** Crucial for understanding the reliability of the transcription.

### 4.3 Scoring Mechanism

The scoring module applies rules to compare the ASR output to the expected text:

- **Fuzzy Matching:** Due to ASR errors (especially on low-end devices), the scoring must use fuzzy matching. For example, if the expected text is "read" and the ASR outputs "red" (homophones), the scoring should consider this acceptable.
- **Score Types:** The engine returns a categorical score (e.g., Poor, Fair, Good, Excellent) or a percentage to represent the match quality.

### 4.4 Uncertainty Handling

Because ASR engines will make mistakes, the architecture must handle low-confidence scenarios gracefully:

- **Low Confidence Threshold:** If the ASR confidence falls below a defined threshold (e.g., due to background noise or mumbled speech), the pronunciation engine does *not* assign a failing score.
- **Fallback Action:** Instead of failing the user, the system triggers a "Please try again" or "I didn't quite catch that" state. This prevents unfair penalization for ASR failures.

### 4.5 Feedback Generation

The final step is converting the score into accessible feedback.

- **Feedback Payload:** The feedback generator produces text prompts (for screen readers) and triggers corresponding audio cues (success chimes, encouraging voice prompts).
- **Constructive Messaging:** A "Poor" score generates constructive feedback, not a simple failure tone, to maintain a supportive learning environment.

## 5. Alternatives Considered

- **Combined ASR and Pronunciation Model:** Rejected. Coupling ASR and pronunciation scoring into one model violates the modular engine constraints and makes it difficult to swap ASR models (e.g., Vosk to Whisper) based on low-end device benchmarks.

## 6. Open Questions

- What specific fuzzy matching algorithms (e.g., Levenshtein distance on phonetic representations) will be used for the initial MVP?
- What is the exact confidence score threshold required to trigger an "Uncertainty / Try Again" response instead of a grade?

## 7. Risks

- The chosen ASR model might not provide word-level confidence scores or timestamps on low-end devices, limiting the granularity of pronunciation feedback.

## 8. Acceptance Criteria

- The architecture explicitly separates ASR and Pronunciation Assessment.
- Mechanisms for fuzzy matching and uncertainty handling are defined.
- The pipeline clearly flows from ASR output -> Evaluation -> Feedback.
