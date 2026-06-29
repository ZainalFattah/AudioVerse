# AI Tutor Architecture

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers:
Last updated: 2026-06-28
Related documents:

- [Software Architecture Document](software-architecture-document.md)
- [Modular Engine Interface Architecture](modular-engine-architecture.md)
- [AI Tutor Requirements](ai-tutor-requirements.md)

## 1. Purpose

This document defines the module architecture and safety constraints for the AI Tutor in AudioVerse English. The AI Tutor is an optional, modular engine designed to provide dynamic conversational practice and feedback for English learners, while adhering to the app's strict offline-first, low-end device, and accessibility requirements.

## 2. Scope

This document covers:

- The `IAITutorEngine` interface and its boundary within the application.
- Implementation candidates for both local (embedded) and cloud-based models.
- Safety guardrails restricted to English language learning.
- Fallback behaviors and graceful degradation for offline scenarios.
- Exclusions and non-goals (e.g., ASR processing, core lesson flow dependency).

## 3. Audience

Engineering team, product managers, and reviewers.

## 4. Definitions

- **AI Tutor:** An optional conversational AI module for free-form or guided English practice.
- **Guardrails:** Explicit instructions or rule-based filters preventing the AI from engaging in unsafe or non-educational topics.
- **Graceful Degradation:** The ability of the application to maintain core functionality when the AI Tutor is unavailable (e.g., due to no network for cloud models).

## 5. Assumptions

- The AI Tutor must not interfere with or replace the core lesson progression.
- Local AI models on 2GB RAM devices will likely face strict resource constraints and require benchmarking.
- Cloud AI models can provide better conversational quality but violate strict offline-first requirements if made mandatory.

## 6. Architecture Design

### 6.1 Modular Nature and Interface

The AI Tutor is strictly separate from the core lesson flow and Speech Recognition (ASR). It operates behind the `IAITutorEngine` conceptual interface (as defined in the Modular Engine Interface Architecture).

- **Core interaction:** The core app provides a text string (from the ASR adapter) and conversation context to the `IAITutorEngine`. The engine returns a text reply and optional suggested corrections.
- **Separation from ASR:** The AI Tutor does not handle audio transcription. It relies entirely on the output provided by the ASR module.
- **Optionality:** The application must remain fully functional if the AI Tutor is disabled by the user or removed from the build.

### 6.2 Implementation Candidates

To ensure replaceability, the AI Tutor can be implemented using different adapters:

- **Local Candidates (Embedded AI):** Small, quantized models (e.g., Llama.cpp style models, TinyLlama). These models must fit within strict memory (<500MB RAM overhead) and storage constraints, making them suitable for low-end devices without internet access. They must be benchmarked before inclusion.
- **Cloud Candidates:** APIs such as OpenAI, Anthropic, or Supabase Edge Functions connecting to an LLM. These offer higher quality but require internet connectivity and explicit user consent for data privacy.
- **Rule-Based Fallback:** A non-generative, template-based engine that uses predefined conversational paths. This is the ultimate fallback if true AI is unfeasible on the device.

### 6.3 Safety Guardrails

The AI Tutor adapter must enforce strict boundaries:

- **Topic Restriction:** The system prompt or rule engine must explicitly constrain the AI to English learning topics (e.g., grammar, vocabulary, roleplay scenarios).
- **Inappropriate Content:** The adapter must filter or pivot away from unsafe, offensive, or off-topic prompts. If a guardrail is triggered, the engine throws a `ContentSafetyViolationError` or returns a graceful pivot message (e.g., "Let's focus on our English practice. How would you say...").

### 6.4 Fallback Mode and Graceful Degradation

- If a cloud-based adapter is active and the device goes offline, the adapter must immediately return an `EngineUnavailableOfflineError`.
- The presentation layer must catch this error and inform the user via an accessible audio/text message (e.g., "The AI Tutor is currently offline. Returning to the main lesson.").
- The application must not crash, hang, or endlessly retry. It must seamlessly fall back to the structured, offline core lesson flow.

## 7. Risks

- Benchmark failure: Local models may prove too slow or memory-intensive for target low-end devices, forcing reliance on cloud or rule-based alternatives.
- Hallucinations: Generative models may provide incorrect grammar advice or stray from the English learning context despite guardrails.

## 8. Open Questions

- Will the MVP include a functional generative AI adapter, or will it rely on a mock/rule-based adapter until benchmarking is complete?
- How will the AI's text responses be synthesized into audio (local TTS vs. cloud TTS)?

## 9. Acceptance Criteria

- AI Tutor architecture clearly defines the interface boundary and modularity.
- Local and cloud candidates are identified.
- Safety guardrails and fallback behaviors are documented.
- The document confirms strict separation from ASR and core lesson flow.

## 10. Review Checklist

- [ ] Is the AI Tutor separated from ASR and core lesson flow?
- [ ] Are safety guardrails restricted to English learning?
- [ ] Is offline graceful degradation defined?
- [ ] Is the AI Tutor explicitly optional?

## 11. Change Log

| Date | Version | Author | Changes |
| --- | --- | --- | --- |
| 2026-06-28 | 0.1 | Jules | Initial draft for Phase 27 |
