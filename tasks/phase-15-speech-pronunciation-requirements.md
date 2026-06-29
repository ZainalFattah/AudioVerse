# Phase 15 - Speech and Pronunciation Requirements Execution Notes

Project: AudioVerse English
Phase: Phase 15
Status: Complete
Date: 2026-06-28

## Documents created or changed

- `docs/speech-pronunciation-requirements.md` (Created)
- `docs/software-requirements-specification.md` (Updated)
- `tasks/phase-15-speech-pronunciation-requirements.md` (This file)

## Key decisions

- ASR engine and Pronunciation Assessment engine are explicitly separated behind clear interfaces, allowing for different implementations.
- No ASR engine has been selected yet. The choice is explicitly deferred until a benchmark is performed to validate its performance and resource usage on low-end devices.
- Pronunciation Assessment logic will need to handle "fuzzy matching" to allow for typical ASR errors (e.g. homophones) to not penalize the learner incorrectly.
- Scoring translates to both text strings and audio cues for accessibility.

## Open questions

- What is the minimum acceptable ASR confidence threshold before prompting a retry?
- Should the scoring system provide detailed phoneme-level feedback, or is sentence/word-level sufficient for the MVP?

## Risks

- Finding an ASR model that is accurate enough for pronunciation assessment while remaining small enough for the 100MB app size limit and performant on low-end devices is a significant challenge.

## Quality gate result

- Pass. Speech and Pronunciation requirements are properly defined as distinct processes and aligned with project limits and benchmarks.

## Recommended next phase

- Phase 16 - AI Tutor Requirements
