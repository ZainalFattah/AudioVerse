# AI Tutor Requirements

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers:
Last updated: 2026-06-28
Related documents:
- [Software Requirements Specification](software-requirements-specification.md)

## 1. Purpose
This document defines the functional and non-functional requirements for the AI Tutor component of AudioVerse English. The AI Tutor is an optional, experimental feature intended to provide dynamic conversational practice and feedback for learners.

## 2. Scope
The scope includes defining the interactions, safety guardrails, operational constraints, and architectural boundaries for the AI Tutor. The AI Tutor is explicitly separate from Speech Recognition (ASR) and Pronunciation Assessment.

## 3. Audience
Engineering team, product managers, and reviewers.

## 4. Definitions
- **AI Tutor:** A conversational agent that can engage in free-form or guided dialogue with the learner.
- **Embedded AI:** AI models that run entirely on the local device without network access.
- **Guardrails:** Rules and mechanisms to prevent the AI from generating inappropriate, unsafe, or off-topic content.

## 5. Assumptions
- Running Large Language Models (LLMs) locally on low-end target devices (2GB RAM) is currently unfeasible for complex tasks.
- If implemented locally, the AI Tutor will likely rely on small, quantized models (e.g., Llama.cpp style) or rule-based fallback mechanisms.
- Cloud-based AI APIs (e.g., OpenAI, Anthropic) may be used if connectivity is available and user consent is granted.

## 6. AI Tutor Requirements (AI)

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| AI-001 | **Modularity and Replaceability:** The AI Tutor must be implemented behind a defined adapter interface, completely decoupled from core lesson logic. | High | Essential to swap local models, cloud APIs, or rule-based engines without app rewrites. | Architecture review confirms AI Tutor is an isolated module communicating via a standard contract. |
| AI-002 | **Optional Feature:** The AI Tutor must be an strictly optional feature that can be toggled on or off by the user. Core app functionality must not break when disabled. | High | Ensures the app remains functional for users who prefer structured learning, lack resources, or are offline. | Disabling the AI Tutor hides its entry points and prevents any associated background processes. |
| AI-003 | **Clear Separation from ASR:** The AI Tutor must not be used for Speech Recognition or Pronunciation Assessment. It receives text input and returns text/audio output. | High | Prevents architectural conflation; AI should focus on conversation/feedback, not transcription. | AI Tutor only processes strings provided by the ASR adapter. |
| AI-004 | **Graceful Degradation (Offline):** If configured to use a cloud API, the AI Tutor must fail gracefully with an accessible message when the device is offline. | High | Maintains offline-first reliability. | Attempting an AI conversation offline (when cloud-backed) triggers a clear audio/text message and returns to the lesson. |
| AI-005 | **Content Safety Guardrails:** The AI Tutor must be restricted to English language learning topics. It must reject or gracefully pivot away from inappropriate, unsafe, or non-educational topics. | High | Protects users from harmful content and keeps the focus on learning. | Prompting the AI with inappropriate content results in a standard rejection/pivot response. |
| AI-006 | **Benchmark Requirement (Local AI):** Any local embedded AI model must be benchmarked on target low-end devices before integration. It must not exceed memory (e.g., <500MB RAM overhead) or battery constraints. | High | Prevents the AI from crashing the app or draining the battery on limited hardware. | ADR exists approving a local model based on positive benchmark data. |
| AI-007 | **Accessible Output:** Output from the AI Tutor must be compatible with screen readers and seamlessly integrated into the audio playback pipeline. | High | Ensures blind and low-vision users can interact with the tutor. | AI responses are spoken aloud naturally or correctly read by TalkBack/VoiceOver. |

## 7. Risks
- Embedding a useful language model locally on a 2GB RAM device while keeping the app under 100MB is highly speculative.
- Cloud APIs introduce latency and break the strict offline-first experience if relied upon too heavily.

## 8. Open Questions
- Is a rule-based or template-based "Tutor" sufficient for the MVP instead of a true generative AI model?
- How will TTS (Text-to-Speech) for the AI Tutor be handled (local OS TTS vs. generated audio)?

## 9. Acceptance Criteria
- AI Tutor requirements clearly separate it from core app functions.
- The optional and modular nature of the AI Tutor is enforced.
- Safety and offline behaviors are defined.

## 10. Review Checklist
- [ ] Is the AI Tutor clearly optional?
- [ ] Are low-end device constraints explicitly mentioned?
- [ ] Is it separate from ASR and Pronunciation Assessment?

## 11. Change Log
| Date | Version | Author | Changes |
| --- | --- | --- | --- |
| 2026-06-28 | 0.1 | Jules | Initial Draft for Phase 16 |