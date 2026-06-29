# Phase 26 - Pronunciation Evaluation Architecture

## Status

Complete

## Documents created or changed

- Created `docs/pronunciation-evaluation-architecture.md`
- Updated `docs/documentation-inventory.md`

## Key decisions

- Pronunciation Assessment logic is strictly decoupled from the ASR model.
- Fuzzy matching will be used to account for minor ASR errors (e.g. homophones).
- Low ASR confidence will result in an uncertainty response rather than a failing pronunciation score.

## Open questions

- What specific fuzzy matching algorithms (e.g., Levenshtein distance on phonetic representations) will be used for the initial MVP?
- What is the exact confidence score threshold required to trigger an "Uncertainty / Try Again" response instead of a grade?

## Risks

- The chosen ASR model might not provide word-level confidence scores or timestamps on low-end devices, limiting the granularity of pronunciation feedback.

## Quality gate result

Pass - the pronunciation prototype can now be planned.

## Recommended next phase

Phase 27 - AI Tutor Architecture
