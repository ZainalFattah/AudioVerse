# Phase 16 - AI Tutor Requirements Execution Notes

Project: AudioVerse English
Phase: Phase 16
Status: Complete
Date: 2026-06-28

## Documents created or changed
- `docs/ai-tutor-requirements.md` (Created)
- `docs/software-requirements-specification.md` (Updated)
- `tasks/phase-16-ai-tutor-requirements.md` (This file)

## Key decisions
- AI Tutor module architecture is defined as strictly optional and decoupled from the core lesson logic.
- AI Tutor logic is distinctly separated from Speech Recognition (ASR) and Pronunciation Assessment.
- Clear safety guardrails (only English language learning topics allowed) and degradation behaviors (graceful failure when offline, if cloud-backed) are established.
- Local AI implementations must undergo benchmarking to ensure they don't exceed target constraints on low-end devices.

## Open questions
- Is a rule-based or template-based "Tutor" sufficient for the MVP instead of a true generative AI model?
- How will TTS (Text-to-Speech) for the AI Tutor be handled (local OS TTS vs. generated audio)?

## Risks
- Embedding a useful language model locally on a 2GB RAM device while keeping the app under 100MB is highly speculative.
- Cloud APIs introduce latency and break the strict offline-first experience if relied upon too heavily.

## Quality gate result
- Pass. The AI Tutor requirements clearly scope the AI Tutor feature as optional, modular, and bounded by low-end device capabilities.

## Recommended next phase
- Phase 17 - Security, Privacy, and Data Requirements