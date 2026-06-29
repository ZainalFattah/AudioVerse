# Phase 05 - Glossary and Domain Model

Project: AudioVerse English
Status: Complete
Date: 2026-06-28

## Objective

Establish shared vocabulary and conceptual entities for lessons, units, soundscapes, speech, pronunciation, AI tutor, and sync.

## Deliverables

- `docs/glossary.md`
- `docs/domain-model.md`

## Summary

Created a glossary and domain model that explicitly differentiate Speech Recognition (ASR) from Pronunciation Assessment. Defined "Soundscape" purely as an asset-based structure (JSON + audio files) rather than dynamically generated content. Mapped out the relationships between Curriculum (Courses, Lessons, Content Packages), Learner Progress (Profiles, Attempts), and Engines (Audio, ASR, Pronunciation).

## Files changed

- Created `docs/glossary.md`
- Created `docs/domain-model.md`
- Created `tasks/phase-05-glossary-domain-model.md`

## Decisions made

- Content Packages are defined as the unit of distribution to ensure offline availability without massive initial downloads.
- Attempts are structured to hold both the ASR output (what was heard) and the Pronunciation Score (how well it was said compared to expectations), cementing the separation of these two engines.

## Risks

- The exact metrics making up a `PronunciationScore` (e.g., phoneme-level vs word-level) remain undefined and could complicate the engine adapter interface later.

## Open questions

- What specific metadata fields are required in the `Soundscape` JSON manifest (e.g., spatial coordinates, volume ducking)?
- How long are `Attempts` (specifically the associated audio files) retained on the local device before being purged to save space?

## Quality gate status

- Gate A (Foundation Complete): Progressing. Pending Documentation Inventory and Risk Register.

## Recommended next phase

- Phase 06 - Documentation Inventory and Gap Analysis
