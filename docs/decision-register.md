# Decision Register

Project: AudioVerse English
Status: Approved
Owner: Jules
Reviewers: User
Last updated: 2026-06-28
Related documents: [Jules Master Development Handbook](../jules/JULES_MASTER_DEVELOPMENT_HANDBOOK.md)

## Purpose

This document serves as the central register for all Architecture Decision Records (ADRs) and tracks the backlog of pending technical decisions.

## Accepted Decisions

The following decisions have been formally accepted and documented as ADRs in the `decisions/` directory:

| ADR ID | Title | Status | Date |
| --- | --- | --- | --- |
| [ADR-0001](../decisions/ADR-0001-use-flutter-as-primary-frontend.md) | Use Flutter as Primary Frontend | Accepted | 2026-06-28 |
| [ADR-0002](../decisions/ADR-0002-use-sqlite-for-offline-storage.md) | Use SQLite for Offline Storage | Accepted | 2026-06-28 |
| [ADR-0003](../decisions/ADR-0003-adopt-offline-first-architecture.md) | Adopt Offline-First Architecture | Accepted | 2026-06-28 |
| [ADR-0004](../decisions/ADR-0004-use-modular-engine-architecture.md) | Use Modular Engine Architecture | Accepted | 2026-06-28 |
| [ADR-0005](../decisions/ADR-0005-defer-speech-recognition-engine-decision.md) | Defer Speech Recognition Engine Decision | Accepted | 2026-06-28 |

## Decision Backlog

The following technical decisions are pending and will require an ADR before or during the technical design phases:

| Topic | Context | Target Phase | Status |
| --- | --- | --- | --- |
| State Management | Select the Flutter state management approach (e.g., Riverpod, Provider, BLoC) ensuring it supports complex audio state and offline persistence. | Phase 35 | Pending |
| AI Tutor Model Selection | Decide on the specific embedded AI model (e.g., LFM-style small model) for the optional AI Tutor, based on low-end device benchmarks. | Phase 27 / Later | Pending |
| Cloud Sync Provider | Formally select the optional cloud sync provider (e.g., Supabase) and define the exact sync mechanisms. | Phase 23 / Later | Pending |
| Audio Playback Library | Select the specific Flutter package for audio playback (e.g., `just_audio`) after evaluating its accessibility and offline capabilities. | Phase 24 / Later | Pending |
| Speech Recognition Engine | Final selection between Vosk, Whisper.cpp, or others based on the benchmark results. | Phase 57 | Pending |
