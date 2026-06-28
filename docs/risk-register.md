# Risk Register v1

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers: User
Last updated: 2026-06-28
Related documents: [Documentation Inventory](documentation-inventory.md), [Project Charter](project-charter.md)

## Purpose

This document identifies, analyzes, and tracks risks for the AudioVerse English project. The risk register will be updated continuously throughout the project lifecycle.

## Risk Assessment Matrix

| Probability | Impact | Risk Level |
| :--- | :--- | :--- |
| High | High | Critical |
| High | Medium | High |
| High | Low | Medium |
| Medium | High | High |
| Medium | Medium | Medium |
| Medium | Low | Low |
| Low | High | Medium |
| Low | Medium | Low |
| Low | Low | Low |

## Initial Risks

| ID | Category | Description | Probability | Impact | Mitigation | Owner | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| R01 | Accessibility | Core learning flow may rely too heavily on visual UI, failing Total Blind users. | Medium | High | Require accessibility testing with TalkBack/VoiceOver at the prototype phase. Strictly adhere to accessibility interaction model. | Jules | Open |
| R02 | Offline | Essential app functionality may inadvertently depend on network requests. | Low | High | Test application with simulated offline states (airplane mode) at every milestone. Ensure local SQLite DB stores all critical data. | Jules | Open |
| R03 | Speech Accuracy | Selected offline Speech Recognition (ASR) engine may have poor accuracy on low-end devices or non-native accents. | High | High | Benchmark multiple engines (Vosk, Whisper.cpp). Plan for fallback strategies. Treat ASR output as uncertain, not absolute truth. | Jules | Open |
| R04 | Performance | High memory or battery usage from audio/speech processing could crash app on low-to-mid-range target devices. | Medium | High | Define strict performance budgets. Profile memory and battery impact early. | Jules | Open |
| R05 | Privacy | Sensitive voice recordings could be accidentally leaked, backed up to the cloud without consent, or retained longer than necessary. | Low | High | Default to zero retention. Clearly document data lifecycles. Ensure voice processing is local-first. Require explicit opt-in for any cloud sync. | Jules | Open |
| R06 | AI Uncertainty | Embedded AI Tutor (if implemented) may generate inappropriate responses or hallucinate feedback. | Medium | Medium | AI Tutor is strictly optional. Implement robust prompt engineering and safety guardrails. Never treat AI output as authoritative pronunciation scoring. | Jules | Open |
| R07 | Content | High-quality soundscape assets may make content package sizes prohibitively large for offline download. | Medium | Medium | Compress audio files aggressively. Implement modular lesson downloads rather than full course downloads. | Jules | Open |
| R08 | Schedule | Extensive documentation phase could delay code implementation past acceptable limits. | High | Medium | Follow the master development handbook strictly. Keep documentation focused on engineering needs. | Jules | Open |

## Open Questions

- What specific devices will be used for the performance and ASR benchmarks?
- What are the precise size constraints for offline content packages?
