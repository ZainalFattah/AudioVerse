# Vision Review

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers:
Last updated: 2026-06-28
Related documents: jules/JULES_MASTER_DEVELOPMENT_HANDBOOK.md, docs/project-charter.md

## Purpose

The purpose of this document is to convert the existing product vision (as outlined in the Project Charter and Jules Master Development Handbook) into engineering implications. It identifies what aspects of the vision are usable as requirements, what is missing, what technical risks exist, and what must be clarified before the Software Requirements Specification (SRS) can be completed.

## Scope

This review evaluates the project vision against engineering constraints, focusing specifically on accessibility, offline-first behavior, audio architecture, device targets, and modular engines.

## Audience

Jules (AI Agent), human maintainers, and future engineers who will translate the SRS into architecture and technical designs.

## Summary of the Vision

AudioVerse English aims to be an offline-first, audio-based English learning platform tailored for blind and low-vision learners. It relies on a screen reader-compatible interface (TalkBack/VoiceOver), modular speech recognition and pronunciation scoring (not just ASR as scoring), optional AI assistance, and structured, asset-driven soundscapes to create an immersive learning environment.

## Findings: What is Usable (Product Intent vs. Requirements)

The following aspects of the vision are clear enough to directly inform SRS requirements:

- **Accessibility First:** The mandate to support TalkBack, VoiceOver, high contrast mode, and blank screen mode is an actionable non-functional requirement (NFR) and UI constraint.
- **Offline Reliability:** The requirement that core lessons, playback, and progress tracking must function completely without internet access.
- **Audio-First Design:** The concept of using structured JSON manifests + WAV/MP3 files for "soundscapes" (as opposed to generative AI) provides a clear technical path for asset management.
- **Pluggable Engines:** The architecture constraint to decouple Speech Recognition (ASR), Pronunciation Assessment, and AI Tutors into modular components.

## Findings: What is Missing or Ambiguous

The following areas require clarification before detailed SRS drafting:

- **Target Device Baseline:** The vision mentions "low-to-mid-range phones," but specific minimum OS versions (e.g., Android API level, iOS version) and acceptable hardware constraints (RAM, CPU limits) are not yet defined.
- **Audio File Packaging:** How will the offline lesson content and audio files be packaged and distributed? (e.g., pre-installed in the app bundle vs. downloadable expansion packs).
- **Pronunciation Scoring Mechanics:** The vision separates ASR from pronunciation scoring, but the specific metrics for scoring (e.g., phoneme-level accuracy, word-level confidence) and the fallback mechanisms for false positives/negatives are unspecified.
- **Data Retention and Privacy:** While local storage is mandated, limits on local database size (SQLite) and rules for automatically pruning older voice recordings to save space are undefined.
- **Blank Screen Mode Implementation:** Is this an app-level overlay that simulates a screen off, or an OS-level integration requirement?

## Technical Risks

| Risk Area | Description | Engineering Implication |
| --- | --- | --- |
| **Speech Engine Performance** | Running models like Whisper.cpp or Vosk entirely locally on low-end devices may exceed CPU/RAM limits or drain battery quickly. | Benchmarking (Phase 25) is critical before committing to an engine. A lightweight fallback or mock is required for MVP. |
| **Storage Constraints** | Audio soundscapes and offline lessons (WAV/MP3s) can quickly bloat app size. | The architecture must support efficient content packaging, potentially requiring separate downloadable modules rather than a massive single APK/AAB. |
| **Screen Reader Conflicts** | Custom gestures in Flutter might conflict with native TalkBack/VoiceOver gestures. | The accessibility interaction model (Phase 39) must strictly define safe gesture zones and focus ordering. |
| **AI Tutor Unpredictability** | Even if optional, an embedded AI (LFM) might hallucinate or provide unhelpful feedback if not properly constrained. | Strict guardrails and a bounded interface are necessary (Phase 27). The MVP should likely use a heavily mocked or rule-based fallback initially. |

## Recommendations for SRS Preparation

Before completing the SRS, the following steps must be taken:

1. **Define Device Matrix:** Establish a clear NFR for the minimum supported Android and iOS devices to bound performance expectations.
2. **Clarify Content Delivery:** Determine if the MVP will ship with built-in content or require an initial download phase.
3. **Bound the AI Tutor:** Explicitly define the AI Tutor as out-of-scope for the core MVP or restrict it to a mocked "rule-based" implementation for early testing.
4. **Detail Accessibility Flows:** The SRS must not just state "support TalkBack," but detail the exact audio cues, focus order, and error recovery states required for a Total Blind user completing a lesson.

## Review Checklist

- [x] Does it avoid inventing unapproved features?
- [x] Does it expose ambiguity?
- [x] Are vision gaps actionable?

## Approval Status

- [x] Approved
- [ ] Approved with changes
- [ ] Changes required
