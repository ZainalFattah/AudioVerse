# Architecture Principles and Constraints

Project: AudioVerse English
Status: Approved
Owner: Jules
Reviewers: User
Last updated: 2026-06-28
Related documents:

- [Software Requirements Specification](software-requirements-specification.md)
- [Jules Master Development Handbook](../jules/JULES_MASTER_DEVELOPMENT_HANDBOOK.md)

## 1. Purpose

This document outlines the foundational architecture principles, strict constraints, and acceptable design tradeoffs for AudioVerse English. These principles serve as the guidelines for all subsequent architectural decisions and technical designs, ensuring the system remains aligned with its core product goals: serving blind and low-vision users with a reliable, offline-first experience on low-to-mid-range devices.

## 2. Core Architecture Principles

### 2.1 Accessibility as a Foundational Attribute

Accessibility is not an afterthought or a UI-layer enhancement; it is a primary quality attribute that drives architectural choices.

- **Principle**: The system must inherently support audio-first navigation, deterministic focus flow, and non-visual feedback mechanisms at all layers, not just the presentation layer.
- **Implication**: State management and navigation architectures must explicitly support screen reader (TalkBack/VoiceOver) logical flows, high-contrast states, and blank-screen mode toggles.

### 2.2 Strict Offline-First Paradigm

The application must treat the local device as the primary, authoritative environment.

- **Principle**: All core learning flows, data persistence, and content delivery must function continuously and identically without an internet connection.
- **Implication**: The local SQLite database is the absolute source of truth. Any cloud synchronization must act only as a background, optional enhancement that degrades silently when offline.

### 2.3 Modular and Replaceable Engines

The application must not be tightly coupled to specific third-party technologies or ML models for core capabilities.

- **Principle**: High-risk or rapidly evolving components—specifically Speech Recognition (ASR), Pronunciation Assessment, AI Tutors, Audio Playback, and Cloud Sync—must be hidden behind abstract adapter interfaces.
- **Implication**: We design to interfaces, not concrete implementations. Swapping Vosk for Whisper.cpp, or replacing a local AI model with a cloud API, must require changing only the adapter, not the core business logic.

### 2.4 Performance and Resource Conservatism

The application must respect the limitations of target devices (e.g., 2GB RAM, older CPUs, limited battery).

- **Principle**: Memory, storage, and battery usage must be strictly budgeted and continuously profiled.
- **Implication**: Ephemeral data (like temporary voice recordings) must be aggressively deleted. Background processes must be minimized. Heavy compute tasks (like ASR) must be optimized to prevent thermal throttling and battery drain.

### 2.5 Privacy by Default

Given the sensitivity of voice data, the system must employ strict data protection strategies natively.

- **Principle**: User data, particularly voice recordings, must not leave the device without explicit, informed consent.
- **Implication**: Voice recordings are strictly ephemeral and processed locally. Syncing must be opt-in, and the user must have full control to clear all local data.

### 2.6 Predictable Testability

The architecture must support comprehensive, automated testing across all layers, especially for offline logic and accessibility state.

- **Principle**: Business rules, engine interfaces, and state logic must be testable in isolation without relying on the Flutter UI or real device hardware.
- **Implication**: Extensive use of dependency injection and clear separation of presentation from domain logic is mandatory.

## 3. Strict Architectural Constraints

The following constraints are non-negotiable and must not be violated by any technical design:

1. **No Mandatory Cloud Dependency**: The core learning loop (catalog browsing, playback, speaking practice, feedback, progress tracking) MUST NOT require network calls to function.
2. **Local Data Primacy**: The local SQLite database MUST act as the absolute source of truth.
3. **Engine Encapsulation**: Concrete implementations of ASR, Pronunciation, and AI models MUST NOT be directly instantiated in UI or core domain code. They must be accessed via defined adapter interfaces.
4. **Ephemeral Voice Processing**: Voice recordings captured for pronunciation assessment MUST be deleted immediately after scoring. They MUST NOT be stored persistently or synced to the cloud.
5. **Storage Limit**: The baseline installed application size (excluding downloaded content packages) MUST remain under 100MB.
6. **Benchmark-Driven AI/ASR Selection**: No local ML model (ASR or AI Tutor) may be permanently integrated without prior documented benchmark evidence proving it runs acceptably on the target low-end hardware without exceeding the 15%/hour battery drain limit.
7. **Asset-Driven Soundscapes**: Soundscapes MUST be defined by deterministic JSON manifests and static audio assets. They MUST NOT rely on generative AI at runtime.

## 4. Unacceptable Designs

To ensure alignment with the principles, the following architectural approaches are explicitly forbidden:

- **Direct API Coupling**: Making direct HTTP calls to Supabase (or any backend) from UI widgets or bypassing local repository abstractions.
- **UI-Bound Business Logic**: Placing pronunciation scoring logic, database queries, or offline check logic inside Flutter Widgets.
- **Silent Background Sync**: Syncing user data to the cloud without explicit opt-in consent or failing obtrusively when a sync attempt fails while offline.
- **Monolithic Audio Processing**: Tightly coupling audio playback, recording, ASR, and pronunciation scoring into a single class or pipeline.
- **Visual-Only Cues**: Relying solely on visual changes (colors, icons) to indicate state changes (e.g., recording started, error occurred) without corresponding accessibility/audio hooks.

## 5. Acceptable Tradeoffs

When designing the system, the following tradeoffs are acceptable and expected:

- **Storage vs. Content Granularity**: It is acceptable to require users to download content in larger, structured packages (e.g., full Units) rather than tiny micro-lessons to simplify offline package management and ensure complete offline functionality, even if it uses slightly more storage temporarily.
- **Accuracy vs. Performance (ASR/AI)**: It is acceptable to choose a slightly less accurate ASR model if it significantly reduces latency, memory usage, and battery drain on low-end devices, prioritizing a fluid user experience over perfect transcription.
- **Sync Eventual Consistency**: It is acceptable for cloud data to lag behind local data (eventual consistency) when sync is enabled. Real-time sync is not required, prioritizing offline responsiveness.
- **UI Aesthetics vs. Accessibility**: If a complex custom UI component cannot be made perfectly compatible with TalkBack/VoiceOver, it is acceptable (and required) to use simpler, standard UI components to ensure accessibility, sacrificing unique aesthetics for usability.
