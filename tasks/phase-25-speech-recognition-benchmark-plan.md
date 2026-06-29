# Phase 25 - Speech Recognition Benchmark Plan Execution Notes

Project: AudioVerse English
Phase: Phase 25
Status: Complete
Date: 2026-06-28

## Documents created or changed

- `docs/speech-recognition-benchmark-plan.md` (Created)
- `tasks/phase-25-speech-recognition-benchmark-plan.md` (This file)
- `docs/documentation-inventory.md` (Updated status and next steps)

## Key decisions

- The benchmark specifically focuses on evaluating the core Speech Recognition (ASR) engine and isolates it from Pronunciation Assessment.
- The target devices emphasize low-to-mid-range hardware, defining a hard minimum specification (2GB RAM, Android 8.0+/iOS 12+).
- Decision criteria weigh offline capability, memory limits, and battery drain as hard constraints, while treating the accuracy/latency balance as a trade-off.

## Open questions

- Will an alternate on-device engine like ML Kit support custom, offline language models without attempting network calls in the background?
- Where should the testing datasets (native vs. non-native accented English) be sourced to ensure they accurately represent the target learner demographics?

## Risks

- Both Vosk and Whisper.cpp might fail the memory and latency targets on the lowest-end target devices, requiring a graceful fallback or degraded operational mode.

## Quality gate result

- Pass. The benchmark plan defines how to measure and choose the ASR engine based on evidence, allowing the decision to be responsibly deferred until performance testing is complete.

## Recommended next phase

- Phase 26 - Pronunciation Evaluation Architecture
